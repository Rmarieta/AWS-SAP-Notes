# Directory Service - Microsoft AD

- Built using Microsoft Active Directory 2012 R2
- Can be managed using the standard active directory tools
- Supports Group Policy and SSO
- Supports Schema extension - MS AD Aware Apps => such as Sharepoint, SQL, DFS (Distributed File System)
- Two sizes: Standard (30'000 objects) VS Enterprise (500'000)
- Native AD, used for AD authentication/authorisation of products and services within AWS
- HA by default => >=2AZs
- Includes monitoring, recovery, replication, snapshots, and maintenance => configurable but managed by AWS

- Supports 1-way or 2-way external and forest trusts with on-prem active directory (but are SEPARATE directories, they can only be configured to trust each other !)
- Directory in AWS => can operate through a network link failure to any connected on-prem systems (not the case wih AD Connector !)
- Supports RADIUS-based MFA
- Best choice for >5'000 users and if trust relationships between AWS and on-prem directories are needed

![alt text](images/microsoft-ad.png)
