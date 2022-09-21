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


