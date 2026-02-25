# Snow Family (DEEPRECATED !)

**Replaced by AWS DataSync for online transfers, AWS Data Transfer Terminal for secure physical transfers**.

- Snowball series are designed to move large amount of data in or out of AWS
- The products in Snow series are physical storage units: suitcases and trucks
- We can order them empty, load them up and return them or vice-versa

## Snowball

- It is a device which is ordered from AWS, log a job and device will be delivered to us
- Any data stored in Snowball is encrypted using KMS
- There are 2 types of devices with 50 TB or 80TB capacity
- In terms of network connectivity we can have 1 Gbps (RJ45 1GBase-TX) or 10 Gbps (LR/SR) networking
- Economical range for a Snowball is 10 TB to 10 PB range of data (multiple devices can be used)
- Multiple devices can be ordered and be sent to multiple business premises
- Snowball only includes storage capability

## Snowball Edge

- Includes both storage capability and compute capability
- It has a larger capacity compared to classic Snowball and has faster networking connection
- There are 3 different type of Snowball Edge:
  - Storage optimized (with EC2 capability): 80 TB, 24 vCPU, 32 Gib RAM, 1 TB of local SSD for EC2 usage
  - Compute optimized: 100 TB + 7.68 NVME, 52 vCPU, 208 Gib RAM
  - Compute optimized with GPU: 100 TB + 7.68 NVME, 52 vCPU, 208 Gib RAM, GPU
- Ideal for remote sites or where data processing on ingestion is needed

## Snowmobile

- Portable data center within a shipping container on a truck
- Needs to be specially ordered from AWS
- Ideal for single location when 10 PB+ is required
- Can store up to 100PB of data per Snowmobile
- Not economical for multi-site or sub 10PB
