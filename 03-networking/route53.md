# Route53

## DNS Fundamentals

- DNS is a discovery service, translate information which machines need into information that humans need and vice-versa
- Example: www.amazon.com => 104.98.34.131
- DNS database is a huge distributed database
- DNS allows a DNS resolver server to find a Zone File ona Name Sever (NS) and query the it, retrieving the necessary IP address for a DNS name

## DNS Terminology

- **DNS Client**: refers to a customer PC, laptop, tablet, etc.
- **DNS Resolver**: software running on a device or a server which queries DNS on our behalf
- **DNS Zone**: a part of the DNS database (example: amazon.com)
- **Zone file**: it is a physical database for a zone, contains all the DNS information for a particular domain
- **DNS Name server**: a server which hosts the zone files

## DNS Root

- DNS is structures like a tree, DNS root is at the top of the tree
- DNS root is hosted in 13 special nameservers, known as DNS Root Servers
- Root hints file: an OS file containing the address of all root servers
- Authority: when something is trusted in DNS, example root hints file
- Delegation: trusted authorities can delegate a part of themselves to other entities, those entities becoming authoritative for the part delegated

## Route53 Introduction

- It is a managed DNS product
- Provides 2 main services:
  - Register domains
  - Can host zone files on managed nameservers
- It is a global service, its database is distributed globally and resilient

## Register a Domain

![R53 Register](images/Route53Register.png)

## Hosted Zones

- DNS as a service
- Let's us create and manage zone files
- Zone files are hosted on 4 AWS managed nameservers
- A hosted zone can be public, accessible for the public internet, part of the public DNS system, or it can be private, linked to one or more VPC(s)
- A hosted zone stores DNS records (recordsets)

## DNS Record Types

- Name server (NS): allow delegation to occur in DNS
- A and AAAA records: map host names to IP addresses. A records maps a host name to IPv4, AAAA maps the host to IPv6 addresses
- CNAME (canonical name): maps host to host records, example ftp, mail, www can reference different servers. CNAME can not point directly to IP addresses, they can point to other names only
- MX records: used for sending emails. Can have 2 parts: priority and value. If we include a dot (.) in the end of the domain to which the record points, that will co considered as a FQDN (Fully Qualified Domain Name)
- TXT (text) records: arbitrary text to domain. Commonly used to prove domain ownership

## DNS TTL - Time To Live

- Indicates how long records can be cached for
- Resolver server will store the records for the amount of time specified by the TTL in seconds
- TTL is a balance: low values means less queries to the server, high values mean less flexibility when the records are changed

## Public Hosted Zones

- A hosted zone is DNS database for a given section of the global DNS database
- Hosted zones are created automatically when a domain is registered in R53, they can be created separately as well (we will have to update the name severs values after that)
- There is a monthly fee for each running hosted zone
- A hosted zone hosts DNS records, example A, AAAA, MX, NS, TXT etc.
- Hosted zones are authoritative for a domain
- When a public hosted zone is created, R53 allocates 4 public name servers for it, on these name servers the zone file is hosted
- We use NS records to point at these name servers to be able to connect to the global DNS
- Externally registered domains can point to R53 public zone
- From within VPC, all DNS queries (private or public) first go to the resolver AmazonProvidedDNS, is available at VPC CIDR base + 2. If query is for a public domain, the AmazonProvidedDNS resolver walks the public DNS tree and returns the NS record.

## Private Hosted Zones

- Similar to a public hosted zone except it can not be accessed from the public internet
- They are associated with VPCs from AWS and it only can be shared with VPCs from the account. Cross account access is possible
- Split-view (Split Horizon) DNS: it is possible to create split-view (split-horizon) DNS for public and internal use with the same zone name. Useful for accessing systems from the private network without accessing the public internet.
- Since only from within VPC => resolved by AmazonProvidedDNS

## CNAME vs Alias Records

- The problem with CNAME:
  - An A record maps a NAME to an IP Address
  - A CNAME records maps a NAME to another NAME
  - **We can not have a CNAME record for an APEX/naked domain name (for ex, this is not allowed with DNS standards: CNAME rmarieta.click => ALB hostname), so Alias records solve that problem.**
  - Many AWS services use DNS Name (example: ELBs)
- For the APEX domain to point to another domain, we can use ALIAS records
- An alias record maps a NAME to an AWS resource
- Can be used for both apex and normal records
- There is no additional charge for alias requests pointing at AWS resources
- An alias is a subtype, we can have an A record alias and a CNAME record alias

## Route 53 Routing Policies

Available routing policies:

- Simple Routing
- Failover Routing
- Multivalue Routing
- Weighted Routing
- Latency-based Routing
- Geolocation Routing
- Geoproximity Routing
- IP-based Routing

## Simple Routing

- Each DNS record can have multiple values for a NAME (for ex A record with 4 IPs)
- With simple routing, in case of a request: all the values for the record are returned to the client in a random order
- The client chooses one of the values and connects to the server
- Limitations: does not support health checks!

## Health Checks

- Health checks are separate from, but used by records in Route53
- They are created separately and they can be used by records in Route53
- Health checks are performed by a fleet of health checkers distributed globally
- Health checks are not just limited to AWS targets, can be any service with an IP address
- Health checkers check every 30s (or 10s at extra cost)
- Health checks can be TCP, HTTP, HTTPS, HTTP(S) with String Matching. A TCP connections should be completed in 4s and endpoint should respond with a 2xx or 3xx status code within 2s after connections. After the status code is received, the response body should also be received within 2 seconds
- In case of string matching the text should be present entirely in the first 5120 characters of the request body or the endpoint fails the health check
- Based on the health checks the service can be categorized as `Healthy` or `Unhealthy`
- Health checks can be of 3 types:
  - Endpoint: assess the health of an endpoint we specify
  - CloudWatch Alarm Checks: they react to CloudWatch alarms
  - Calculated Checks: checks of other checks
- If 18%+ of the health checkers report the target as healthy, the target is considered healthy

## Failover Routing

- We can add 2 records of the same name (a primary and a secondary)
- Health checks happen on the primary record
- If the primary record fails the health checks, the address of the secondary record is returned
- Failover routing should be used when we configure active-passive failover

## Multi Value Routing

- Multi Value Routing is mixture to simple and failover routing
- With multi value routing we can create many records with the same name
- Each record can have an associated health check
- When queried, 8 records are returned to the client. If more than 8 records are present, 8 records will be randomly selected and returned
- The client picks one a of the values and connects to the service
- Any records which fails the health checks won't be returned when queried
- Multi value routing is not a substitute for an actual load balancer (no SSL termination, no perfect distribution, no path routing, etc).

## Weighted Routing

- Weighted Routing can be used when looking for a simple form of load balancing or when we want to test new versions of an API (canary deployments => for ex new app only to 10% of users)
- With weighted routing we can specify a weight for each record
- For a given name the total of weight is calculated. Each record is returned depending on the percentage of the record compared to the total weight
- Setting a weight to 0, the record will not be returned. If every record is set to 0, all of them will be returned
- Weighted routing can be combined with health checks.
- **Health checks don't remove records** from the calculation of the total weight. If a record is selected, but it is unhealthy, another selection will be made until a healthy record is selected

## Latency-Based Routing

- Should be used when we trying to optimize for performance and better user experience
- For each record we can specify an region
- AWS maintains a list of latencies from user locations to each AWS region (user DNS resolver location <=> destination latency)
- When a request comes in, it will be directed to the lowest latency destination based on the location from where the request is coming from
- Latency-based routing can be combined with health checks. If the lowest latency record fails, the second lowest latency record will be returned to the client
- The latency database maintained by AWS is not updated in real time

## Geolocation Routing

- Geolocation routing is similar to latency-based routing, only instead of latency the location of customer and resources is used to determine the resolution decisions
- When we create records, we tag the records with a location
- This location is generally a country (ISO2 code), continent or default (optional). There can be also a subdivision, in USA we can tag records with a state
- When the user does a query, an IP checks verifies the location is the user
- Geolocation does not return the closest record, it returns relevant records: when the resolution happens, the location of the user IP is cross-checked with the location specified for the records and the matching on is returned
- User location normally = Resolver location
- Order of the cross-checks is the following:
  1. R53 checks the state (US & Ukraine only)
  2. R53 checks the country
  3. R53 checks the continent
  4. Returns default (optional) if no previous match
- If no match is detected, then a `NO ANSWER` is returned
- Geolocation is ideal for **restricting** content based on the location of the user:
  - Load balancing across regional endpoints
  - Regional restrictions
  - Language specific content
- **NOT about proximity**

## Geoproximity Routing

- Geoproximity aims to provide records as close to the customer as possible, aims to calculate the distance between the resource and customer IP and return the record with the smallest one
- 3 location types with Geoproximity records:
  - Region: an AWS region. Distance is computed between the client IP and the centre of the region
  - Lat/lon coordinates. Distance is computed with that coordinate
  - Local zone: AWS local zone location.
- A Geoproximity record allows defining a bias (between -99 <=> +99), increasing or decreasing the region size. We can influence the routing distance based on this bias

## IP-based Routing

- Chooses the DNS response based on the source IP of the client mapped to a CIDR block
- If client has IP in CIDR A => return record A
- If client has IP in CIDR B => return record B

## Route53 Interoperability

- Route53 acts as a domain registrar and as a domain hosting
- We can also register domains using other external services
- Steps happening when we register a domain using R53:
  - R53 accepts the registration fee
  - Allocates 4 Name Servers
  - Creates a zone file (domain hosting) on the NS
  - R53 communicates with the registry of the top level domain and adds the address of the 4 NS for the given domain
- Route53 acting as a registrar only:
  - We pay for the domain for Route53 but the name servers are allocated by other entity
  - We have to allocate the name servers to Route53 which will communicate with the top level domain registry
- Using Route53 for hosting only:
  - Generally used for existing domains. The domain is registered at third party
  - We create a hosted zone inside R53 and provide the address of the name servers to the third party

## Implementing DNSSEC with Route53

- DNSSEC can be enabled form the console and from the CLI
- Once initiated, the process starts with KMS, an asymmetric key pair is required/created within KMS
- The key-signing keys (KSK) is created from this KMS key, both the public and private ones which will be used by R53
- These keys need to be in us-east-1
- Next, R53 creates the zone-signing keys (ZSK) internally
- Next, R53 adds the KSK and zone-signing key public parts into a DNS key record within the hosted zone, this tells every DNSSEC resolver which public keys to use to verify signatures on any other records
- The private key signing key used to sign those DNS key records and create new records:
  - (Child) DNSKEY record 256 = public ZSK
  - (Child) DNSKEY record 257 = public KSK
  - (Child) RRSIG = Signed RRSET (ex: group of A records with same name), signed by ZSK for all RRSETs except DNSKEY, signed by KSK.
  - (Parent) DS record (Delegate Signer) = hash of child's public KSK
- At this point signing is configured (step 1)
- Next, R53 has to establish the chain of trust with the parent zone
- The parent zone needs ot add a DS record, which is hash of the public part of the KSK
- If the domain was registered with R53, the AWS console or the CLI can be used to make this change. R53 will liaise with the appropriate top-level domain and add the delegated sign record
- If the domain was created externally, we will have to add this record manually
- Once done, the top level domain will trust this domain with the delegated sign record (DS)
- The domain zone will sign each record either with the KSK or with the ZSK
- When enabling DNSSEC we should make sure we configure CloudWatch Alarms, specifically create alarms for `DNSSECInternalFailure` and `DNSSECKeySigningKeyNeedingAction`, both of these need urgent intervention

Useful commands:

- `dig click NS +short` returns the Nameservers DNS names for the TLD
- `dig rmarieta.click DS @<ns-name>` returns the DS records with name rmarieta.click in the specified NS
- `dig rmarieta.click NS +short` returns the Nameservers from R53 for the hosted zone rmarieta.click
- `dig rmarieta.click A @<ns-name>` returns the a records defined in the hosted zone with name rmarieta.click
- `dig rmarieta.click A +dnssec` returns the DNSsec records along with the A record

![DNS Sec](images/DNSSec.png)

## VPC DNS and DNS Endpoints

- In every VPC the VPC.2 IP address is reserved for the DNS
- In every subnet the .2 is reserved for Route53 resolver
- Via this address VPC resources can access R53 Public and associated private hosted zones
- Route53 resolver is only accessible from within the VPC (non site-to-site VPN or DX), hybrid network integration is problematic both inbound and outbound
  ![Isolated DNS Environments](images/Route53Endpoints1.png)
- Solution to the problem before Route53 endpoints were introduced:
  ![Before Route53 Endpoints](images/Route53Endpoints2.png)
- Route53 endpoints:
  - Are delivered as VPC network interfaces (ENIs)
  - Accessible over VPN or DX
  - 2 different type of endpoints:
    - Inbound: on-premises can forward request to the R53 resolver
    - Outbound: interfaces in multiple subnets used to contact on-premises DNS
      - Rules control what requests are forwarded
      - Rules can be associated to outbound endpoints
      - Outbound endpoints have IP addresses (ENIs) assigned which can be whitelisted on-prem
- Route53 endpoint architecture:
  ![Route53 Endpoints Architecture](images/Route53Endpoints3.png)
- Route53 endpoints are delivered as a service
- They are HA and they scale automatically based on load
- They can handle around 10k queries per second per endpoint
