# Chapter 5 - Elastic Load Balancing, Amazon CloudWatch and Auto Scaling

- __Elastic Load Balancing__
  : a service that allows you to distribute traffic across a group of Amazon EC2 instances in one or more _availability zones_
  - allows for high availability within an application
  - supports _routing and load-balancing_ for the following protocols:
    - HTTP
    - HTTPS
    - TCP
    - SSL
  - Supports _internet-facing_ and _internal application-facing_ load balancers
  - supports _health checks_ for Amazon EC2 instances
    - ensure traffic is not routed to unhealthy or failing instances
  - automatically scale based on collected metrics
  - highly available __within__ a region
  - seemlessly integrates with the _Auto Scaling_ service to automaticaly scale the Amazon _EC2_ instances behind the load balancer
  - Secure within the _Amazon VPC_ to route traffic internally between application tiers
  - supports _integrated certificate management_ and _SSL termination_
  - _Amazon VPC_ -> `IPv4`
  - _EC2-Classic_ -> `IPv4 & IPv6`
  - supports protocols operating at two different _Open System Interconnection (OSI)_ layers
    - e.g _transport layer (layer 4)_ to _application layer (layer 7)_

- __Canonical Name (CNAME)__
  : single, stable entrypoint for _Domain Name System (DNS)_ configuration

- __Internet-Facing Load Balancers__
  : load balancer that takes requests from clients over the Internet
  - best practice is always to reference a load balancer by its __DNS name__
    - because _Elastic Load Balancing_ scales in and out to meet traffic demand, it is __not recommended__ to bind an application to an IP address that may no longer be part of a load balancer's pool of resources

- __Internal Load Balancers__
  : route traffic to your Amazon EC2 instances in _VPCs_ with _private subnets_
  - multi-tier applications often need to load balance the _tiers of the application_

- __HTTPS Load Balancers__
  : load balancer that uses the _SSL/TLS_ protocol for encrypted connections
  - enables _traffic encryption_ between your load balancer and the clients that initiate _HTTPS_ sessions
  - predefined SSL negotiation configurations to use to negotiate connections
  - __SSL Certificate__ needs to be installed on the load balancer
  - optionally choose to enable authentication on your backend instances
  - does __not__ support _Server Name Indication (SNI)__ on your load balancer
    - to host multiple websites on a fleet of _EC2 instances_ behind _Elastic load balancing_ with a __single__ _SSL certificate_, you will need to add a _Subject Alternative Name (SAN)_ for each website
      - site users may see a warning message otherwise
 
- __Listeners__
  : a process that checks for connection requests
  - every load balancer must have one or more _listeners_ configured
  - e.g _CNAME_ configured for the _A record_ of the load balancer
  - every listener configured with a protocol and port (frontend and backend)

- __Idle Connection Timeout__
  : triggered when no data has been sent or received
  - load balancer closes the connection
  - load balancer maintains two connections for each client
  - default: `60 seconds`
  - recommended to enable `keep-alive` option for _HTTP/HTTPS_ connections
    - reuse connections which reduces CPU utilization

- __Cross-Zone Load Balancing__
  : ensure that request traffic is routed evenly across all backend instances for your load balancer
  - reduces the need to maintain equivalent numbers of backend instances in each _Availability Zone_
  - e.g for environments where clients cache DNS lookups, incoming requests might favor one of the _Availability Zones_. Cross-zone load balancing ensures this imbalance in the request load is spread across all available backend instances in the region

- __Connection Draining__
  : ensure the load balancer stops sending requests to instances that are deregistering or unhealthy
  - keeps existing connections open (load-balancer can complete in-flight requests)
  - can specify a maximum time for the load balancer to keep connections alive before reporting the instance as **deregistered**
  - defaults to 300 seconds (1-3600sec)

- __Proxy Protocol__
  : uses the proxy protocol to add _human-readable_ header info (source IP address, destination IP address etc)
 - ensure that the load balancer is not behind a proxy server with the _Proxy-Protocol_ enabled (duplication and may result in errors)

- __Sticky Sessions / Session Affinity__
  : enables the load balancer to bind a user's session to a specific instance
  - ensures all requests from a user during the session are sent to the same instance
  - _default:_ load balancer routes each request indepedently to the registered instance
  - can configure _Elastic Load Balancing_ so that the _session cookie_ follows the duration specified by the application's session cookie.
    - **Cookie Name:** `AWSELB`
    - used to map the session to the instance

- __Health Checks__
  : test the status of the _Amazon EC2 instances_ behind an _Elastic Load Balancing_ load balancer
  - `InService` -> healthy status at time of health check
  - `OutOfService` -> _unhealthy_ status at time of health check

- __Amazon CloudWatch__
  : service used to monitor AWS resources and your applications in real time
  - collect & track metrics, create alarms, make changes to resources being monitored based on rules
  - __Basic Monitoring:__
    - sends data points to _CloudWatch_ every 5 minutes
    - limited amount of pre-selected metrics
    - no charge
  - __Detailed Monitoring:__
    - sends data points to _CloudWatch_ every minute
    - allows data aggregation
    - cost involved
  - Can provide custom metrics (e.g Amazon EC2 memory consumption & disk metrics that are visible to the operating system of the Amazon EC2 instance but not visible to AWS or application specific thresholds
  - Custom metrics can be `PUT` into _CloudWatch_ via an API as a _name-value_ pair
  - used to monitor, store and access log files
  - limited to **5000 alarms** per AWS account
  - metrics data is retained for **2 weeks**

- **CloudWatch Logs Agent**
  : provides an automated way to send log data to _CloudWatch_ for _EC2 instances_ running Amazon Linux or Ubuntu

- **Auto-Scaling**
  : service that allows you to scale your Amazon _EC2_ capacity automatically by scaling in and out in according to criteria you define

- **Auto Scaling Plans**
  1. Maintain Current Instance Levels
     : maintain a minimum or specified number of running instances at all times
  2. Manual Scaling
     : only need to specify the change in your minimum, maximum or desired capacity of your Auto Scaling group
     - most basic option
  3. Scheduled Scaling
     : scaling functions are performed automatically as a function of time and date
  4. Dynamic Scaling
     : you define parameters that control the auto-scaling process in a scaling policy

- **Auto Scaling Components**
  - Launch Configuration
  - Auto Scaling Group
  - Scaling Policy

- **Launch Configuration**
  : the template that _Auto Scaling_ uses to create new instances
  - each _Auto Scaling_ group can only have **one** launch configuration at a time
  - composed of:
    - configuration name
    - AMI
    - EC2 instance type
    - security group
    - instance key-pair

- **Auto Scaling Group**
  : collection of _EC2 instances_ managed by the auto-scaling service
  - configuration of when new instances should be launched or terminated
  - name, minimum and maximum number of instances that the group must have
  - e.g cli
    ```bash
    aws autoscaling create-auto-scaling-group \
        --auto–scaling-group-name myASG \
        --launch-configuration-name myLC \
        --availability-zones us-east-1a, us-east-1c \
        --min-size 1 \
        --max-size 10 \
        --desired-capacity 3 \
        --load-balancer-names myELB
    ```
  - can use either _on-demand_ or _spot_ ec2 instances
    - _spot ec2 instances_ need to list a maximum bid price

- **Scaling Policy**
  : associate _CloudWatch alarms_ and _scaling policies_ with _Auto Scaling groups_ to adjust dynamically
