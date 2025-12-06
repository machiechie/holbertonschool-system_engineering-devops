Scaled-Up and Tiered Web Infrastructure Design
This infrastructure design addresses the weaknesses of the previous models by separating the responsibilities of the web stack into dedicated tiers and adding high availability (HA) for the load balancing layer. This results in a minimum of five servers (two Load Balancers, two Application Servers, and one Database Server).

New Components and Rationale
1 Additional Server: This server is added to completely decouple the database from the application layer. By moving the Primary MySQL to its own dedicated server, we eliminate resource contention (the database no longer competes with the application for CPU/RAM) and allow for independent vertical scaling and maintenance of the database tier.

1 Additional Load Balancer (HAproxy): A second load balancer is added to create an HA cluster with the first one. The single load balancer was a major Single Point of Failure (SPOF). This cluster ensures that if one load balancer fails, the second one immediately takes over (often using an Active-Passive setup with a floating IP), providing continuous traffic routing and high availability at the entry point.

Splitting Components into Tiers: The Web Server (Nginx), Application Server, and Database (MySQL) are now placed on separate dedicated tiers. This is a logical separation that is more efficient than the monolithic setup. It eliminates resource contention and allows for horizontal scaling of the application servers based on traffic demands, and independent vertical scaling of the database based on I/O and storage needs.

Infrastructure Overview
The design now uses dedicated, tiered separation:

HA Load Balancer Tier: Two HAproxy servers configured in an HA cluster to eliminate the Load Balancer SPOF.

Application Tier: Two dedicated servers containing the Nginx web server, application server, and application files. These handle client requests, execute business logic, and connect to the Database Tier.

Database Tier: One dedicated server hosting the Primary MySQL database. This separation ensures stability and dedicated resources for persistent data storage and retrieval.
