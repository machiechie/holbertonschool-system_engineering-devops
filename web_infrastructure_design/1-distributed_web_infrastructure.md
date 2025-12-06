Three-Server Distributed Web Infrastructure Design
This infrastructure design adds a Load Balancer and introduces redundancy by having two identical application servers, mitigating the single point of failure (SPOF) issue of the previous single-server design.

Components and Purpose
The infrastructure consists of one load balancer and two redundant application nodes, plus a database layer.

Load Balancer (HAproxy): The Load Balancer is added to distribute incoming traffic evenly across the two application servers. This prevents any single server from being overwhelmed, thus improving performance and availability. It also provides redundancy; if one application server fails, the load balancer stops sending traffic to it.

Server 1 and Server 2 (Application Nodes): Two identical servers are added to achieve horizontal scaling and high availability. Each server contains a full stack:

Nginx (Web Server): Handles incoming requests from the Load Balancer.

Application Server: Executes the application code.

Application Files: The codebase.

MySQL Database Cluster: While the application nodes are replicated, the database is split into a Primary-Replica structure to handle increased read traffic.

Load Balancer Specifics
Distribution Algorithm
The load balancer is configured with the Round-Robin distribution algorithm.

How it Works: Round-Robin is the simplest method. The load balancer cycles sequentially through the list of available application servers. The first request goes to Server A, the second to Server B, the third back to Server A, and so on. This ensures fair distribution of traffic when all backend servers have similar specifications.

Active-Active vs. Active-Passive Setup
The HAproxy load balancer enables an Active-Active setup for the application servers.

Active-Active: In this configuration, all redundant servers are actively processing traffic simultaneously. This configuration is used for load balancing and horizontal scaling, allowing for higher overall throughput.

Active-Passive: In this configuration, only one server (the Active node) processes traffic. The other server (the Passive node) is idle, constantly monitoring the Active node. If the Active node fails, the Passive node takes over the workload (failover). This configuration is used solely for redundancy and high availability, not scaling.

Database Primary-Replica Cluster
How it Works
A Primary-Replica (Master-Slave) database cluster is configured to optimize performance, particularly for read-heavy applications.

The Primary node is the source of truth; it handles all write operations (e.g., creating a new user, updating a record).

Any changes made to the Primary node are asynchronously or synchronously replicated to the Replica node(s).

The Replica node(s) handle all read operations (e.g., retrieving a product list, fetching a user profile).

Primary vs. Replica Role in Application
Primary Node (Master): The application connects to the Primary node for all data writing operations (INSERT, UPDATE, DELETE). This ensures data consistency.

Replica Node (Slave): The application connects to the Replica node(s) for all data reading operations (SELECT). This offloads read traffic from the Primary, allowing the cluster to handle a much higher volume of requests overall.

Issues with this Infrastructure
While much improved, this infrastructure still contains critical shortcomings:

1. Single Points of Failure (SPOF)
The Load Balancer (HAproxy): If the single load balancer fails, it becomes a SPOF, as traffic cannot be routed to either application server. All incoming requests are blocked.

The Database Primary Node: If the Primary MySQL node fails, all write operations cease. While the Replica can still handle reads, the application will be functionally impaired until the Primary is restored or a failover occurs.

Single Codebase Location: If deployment relies on a single shared file system that fails, the application files may become corrupted or inaccessible.

2. Security Issues
No Firewall: There is no explicit firewall layer to inspect and filter network traffic, leaving the Load Balancer and Application Servers vulnerable to malicious requests and common attacks.

No HTTPS: The infrastructure likely runs over standard HTTP (port 80). Without HTTPS (port 443) and an SSL certificate, all data transmitted between the user and the server is unencrypted, creating a security risk for sensitive user information.

3. No Monitoring
There is no monitoring system to track metrics like server health, CPU usage, memory consumption, or application error rates. Without monitoring, issues like slow performance, high load on one server, or imminent disk failure can go undetected until they cause a critical outage.
