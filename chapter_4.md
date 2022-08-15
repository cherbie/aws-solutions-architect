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
  - can create 1 or more subnets in each _availability zone_
  - _subnets reside in **one** availability zone_
  - classifications:
    - _Public_ : routes to AWS IGW (internet)
    - _Private_ : does not direct traffice to AWS IGW (internet)
    - _VPN Only_ : directs traffic to AWS VPG & not to IGW
  - Default VPC -> 1 public subnet with mask of `/20`

- __Route Tables__
  : a logical construct within VPC that contains a set of rules (routes) that are applied to the subnet and used to determine where network traffic is directed
  - each route table contains a __local route__
    : allows communication within the VPC
    - cannot be modified or removed
  - VPC has an implicit router
  - VPC automatically comes with a main route table that you can modify
  - Each route in a table specifies a destination CIDR and a target

- __Internet Gateway__
  : allows communication between instances in your VPC and the internet
  - horizontally scaled, redundant, and highly available Amazon VPC component
  - it is a target in your route tables for internet-routable traffic
  - performs network address translation for instances that have been assigned public IP addresses
  - EC2 instances are only aware of their __private__ IP addresses
  - IGW translates the public address to private and maintains a one-to-one map of the instances private ip address and public ip address

- __Creating a IGW__
  1. Attach IGW to your VPC
  2. Create a subnet route table to send all non-local traffic (`0.0.0.0/0`) to the IGW
  3. Configure ACLs and security group rules to allow relevant traffic to flow to and from your instance
  - Note: to connect EC2 instance to the internet, need to assign a public IP address

- __Dynamic Host Configuration Protocol (DHCP)__
  : a set of standard for passing configuration information to hosts on a TCP/IP network
  - options fields of a DHCP message contains the configuration parameters
    - e.g _domain name_, _domain name server_, _netbios-node-type_
  - AWS automatically creates and associates a DHCP option set for your VPC
  - Assign your own name to your instances -> create a custom DHCP option set and assign it to your VPC
  - Configurable values:
    - __domain name servers__
      : IP addresses of up to 4 DNS servers
    - __domain name__
      : specify desired domain name
    - __ntp servers__
      : ip addresses of up to 4 _network time protocol (NTP) servers
    - __netbios name servers__
      : IP addresses of up to 4 NetBIOS name servers
    - __netbios node type__
      : set this value to 2
  - __Every VPC must have only one DHCP assigned to it__

- __Amazon Provided DNS__
  : amazon domain name system (DNS) server
  - enables DNS for systems communicating over internet (IGW)

- __Elastic IP Addresses__
  : static, public IP address in the pool for the region that you can allocate to your account
  - allows you to maintain a set of IP addresses that remain fixed while the underlying infrastructure may change over time
  - Important Points:
    - You must first allocate an EIP for use within a VPC, and then assign to an EC2 instance
    - EIPs are specific to a region
    - There is a one-to-one relationship between network interfaces and EIPs
    - Can move EIPs from one instance to another (same region but different VPC allowed)
    - Charges for EIPs allocated to your account (even if not in use)

- __Elastic Network Interfaces (ENI)__
  : virtual network interface that you can attach to an instance in an Amazon VPC
  - only available within a VPC
  - associated with a subnet on creation
  - one public IP address, multiple private IP addresses
  - created independentely of a particular instance 
    - IP address may be preserved by attaching the ENI to a replacement instance
  - Enables:
    - create a management network
    - use network and security appliances in your VPC
    - create dual-homed instances w/ workloads/roles on distinct subnets
    - create a low-budget, high availability solution

- __Endpoints__
  : create a private connection between your amazon VPC and another AWS service without requiring access over the internet, NAT, VPN connection or AWS Direct Connect.
  - support communicating with Amazon S3
  - can create multiple endpoints for a single service
  - can use different route tables to enforce different access policies from different subnets
  - How to create:
    1. Specify the Amazon VPC
    2. Specify the service (e.g `com.amazonaws.<region>.<service>`)
    3. Specify the policy (can be changed at any time)
    4. Specify the route tables

_e.g Endpoint Route Table_

|Destination|Target|
|--|--|
|`10.0.0.0/16`|Local|
|`0.0.0.0/0`|igw-1a2b3c5d|

- __Peering__
  : networking connection between two Amazon VPCs
  - different VPC's communicate as if they are in the same VPC
  - **not** a Gateway or an Amazon VPN & does **not** introduce a _single point of failure_
  - peering connections are created through a _request/accept_ protocol
    - identified by its VPC ID and Account ID depending on if the VPC exists in the same or different account.
    - Owner of the peer has _1 week_ to accept or reject the request to peer before expiry
  - may have multiple peering connections
  - peering is a _one-to_one_ relationship
  - peering connections _do not support **transitive routing**_
    : can not communicate to VPC `B` from VPC `A` through VPC `C`
  - Important points:
    - cannot create a peering connection between VPCs that have matching or overlapping CIDR blocks
    - cannot be in _different_ regions
    - cannot have more than one peering connection between the same two Aamzon VPCs at the same time

- __Security Groups__
  : virtual _stateful_ firewall that controls inbound and outbound network traffic to AWS resources and EC2 instances
  - default security group if not specified
    1. allow inbound traffic from instances within the same security group
    2. Allow all outbound traffic
  - Important points:
    - up to 500 security groups for each Amazon VPC
    - up to 50 inbound and outbound rules for each security group
    - specify "allow rules" not "deny rules" (whitelisting)
    - Security groups are stateful
      : responses to allowed inbound traffic are allowed to flow outbound regardless of outbound rules
    - instances associated with the same security group cannot talk to each other unless you add rules allowing it (_excl. default security group_)
    - can change the security groups after launch

- __Network Access Control Lists (ACLs)__
  : another layer of security that acts as a _stateless_ firewall on a subnet level
  - a numbered list of rules that AWS evaluates in order
  - determines whether traffic is allowed in or out of any subnet associated with the network ACL
  - default allows all (can be modified)
  - Every subnet must be associated with a network ACL

__Security Groups Vs Network ACL__

|Security Group|Network ACL|
|--|--|
|Operates at the instance level|Operates at the Subnet level|
|Supports allow rules only|Supports allow rules and deny rules|
|Stateful|Stateless|
|AWS evaluates all rules before deciding whether to allow traffic|AWS processes rules in number order deciding whether to allow traffic|
|Applied selectively to individual instances|Automatically applied to all instances in the associated subnets|

- __Network Address Translation (NAT)__
  : Amazon Linux Machine (AMI) that is designed t oaccept traffic from instances within a private subnet, translate the source IP address to the public IP address of the NAT instance.
  - 