# Chapter 4 - Amazon Virtual Private Cloud (VPC)

> Amazon VPC is the _networking layer_ for Amazon EC, and it allows you to build your own virtual network within AWS

## Defintions

- __Amazon VPC__
  : network layer for Amazon EC2
  - components:
    1. IP address range (_CIDR_ block)
    2. subnets
    3. route tables
    4. network gateways
    5. security settings
  - two different networking platforms available: _EC2-Classic_ & _EC2-VPC_
  - default VPC created in each region with a default subnet created in each availability zone

![Amazon VPC Overview](./images/vpc_overview)

- __Subnets__
  : a segment of an Amazon VPC's IP address range where you can launch EC2, RDS and other AWS resources
  - __CIDR Blocks__ define subnets (e.e `10.0.1.0/24` and `192.168.0.0/24`)
  - smallest subnet CIDR block allowed `/28`
  - Note: _AWS reserve the first four and last IP addresses of every subnet. Hence subtract five for usable range_
  - 

