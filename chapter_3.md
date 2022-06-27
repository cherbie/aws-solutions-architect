# EC2 Instances

## Definitions

- __Bootstrapping__
  : the process of providing code to be run on an instance at launch
  - __UserData__
    : one of the parameters when an instance is launched, passed to the operating system to be executed as part of the launch process
    - applying patches and updates to the OS
    - Enrolling in a directory service
    - Installing application softare

- __Instance Metadata__
  : data about your ec2 instance that you can use to configure or manage the running instance
  - unique mechanism to obtain AWS properties of the instance from within the OS without making a call to the AWS API
  - e.g `http://169.254.169.254/latest/meta-data` - return the top node of the instance metadata tree
  - attributes:
    - _associated security groups_
    - _instance ID_
    - _instance type_
    - _AMI_ used

- Tags
  : can be used to manage / group ec2 instances 

- CloudWatch
  : provides monitoring and altering for Amazon EC2 instances

- __Modifying an Instance__
  1. _Instance Type_
  2. _Security Groups_
  
- __Termination Protection__
  : prevent termination via _Management Console_, _CLI_ or _API_
  - Calls to terminate the instance will fail until termination protection is enabled
  - Helps prevent accidental termination


- __On-Demand Instances__
  : price per hour for each instance type
  - most flexible, no up-front commitment
  - have control over when instance is launched and terminated
  - _least cost-effective_

- __Reserved Instances__  
  : enables customers to make capacity reservations for predictable workloads
  - can save up to 75%
  - specify _instance type_ and _availability zone_ 
  - cost of reservation dependent on
    - _term commitment_
      : duration of the reservation
    - _payment option_
      : _all upfront_ vs _partial upfront_ vs _no upfront_
  - _modifications_ do not change the remaining term and there is no fee

- __Spot Instances__
  : customers specify the price they are willing to pay for a certain instance type
  - _non-critical workloads_ and _tolerant for interruption_
  - when the customers bid price is above the current spot price, the customers will receive the requested instance(s)
  - terminated if (2min notice):
    1. customer terminates them
    2. spot price > bid price
    3. not enough capacity for spot instances
    
- __Shared Tenancy__
  : single host machine may house instances from different customers
  - default tenancy model
  - AWS does not use overprovisioning and fully isolates instances (secure)

- __Dedicated Instances__
  : run on hardware that's dedicated to a single customer

- __Dedicated Host__
  : a physical server with Amazon EC2 instance capacity fully dedicated to a single customer's use
  - help address licensing requirements

- __Placement Groups__
  : a logical grouping of instances within a signle availability zone
  - enables _low-latency_ (10Gbps) network
  - choose an instance type that supports _enhanced networking_ and _10 Gbps networking performance_

- __Instance Store__
  : temporary block-level storage
  - located on disks that are physically attached to host computer
  - temporary storage that changes frequently (buffers, caches, scratch data)
  - included in cost of an EC2 instance
  - _data is temporary_
 
- __Amazon Elastic Block Store (Amazon EBS)__
  : persistent block-level storage volumes for use with Amazon EC2 instances
  - automatically replicated within its _availability zone_
  - attached to a single instance

- __Magnetic Volumes__
  - lowest performance
  - 1GB - 1TB
  - ~ 100 IOPS
  - best suited for:
    1. infrequent data access
    2. sequential reads
    3. low cost storage

- __General-Purpose SSD__
  : cost effective storage, ideal for a broad range of workloads
  - 1GB - 16 TB
  - 3 IOPS/GB - 10,000 IOPS
  - SSD volumes under 1TB can burst to 3,000 IOPS
  - Billed on amount of data space provisioned
  - Suited for:
    1. System boot volumes
    2. Small-medium sized databases

- __Provisioned IOPS SSD__
  : designed for I/O intensive workloads
  - 4GB - 16TB
  - 30IOPS/GB - 20,000 IOPS
  - cost per GB and additional monthly fee for povisioned IOPS
  
- __Amazon EBS-Optimized Instances__
  : an optimized configuration stack and provides additional dedicated capacity for Amazon EBS I/O
  - additional hourly fee

- __Snapshots__
  : incremental backups
  - only the blocks that have changed saved
  - only pay for storage cost of snapshot
  - stored in S3 but cannot be manipulated (must use EBS snapshot feature)
  - constrained to the region they are created (need to instance copy to another region)

- __Creating Volume From Snapshot__
  - EBS volume created immediately but data is loaded lazily
  - best practice to access all the blocks in the volume

> Snapshots can be used to increase the size of an Amazon EBS volume




