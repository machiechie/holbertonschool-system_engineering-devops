Secured and Monitored Distributed Web Infrastructure
This infrastructure builds upon the previous three-server design by adding essential layers of security and observability.

New Components and Rationale
3 Firewalls: Firewalls are necessary to prevent unauthorized access and protect each layer of the infrastructure from external and internal threats.

1 SSL Certificate: The certificate is added to enable HTTPS, which encrypts all traffic between the user and the load balancer, ensuring data security and client trust.

3 Monitoring Clients: These are added to collect real-time data (logs, metrics) from all servers, allowing for proactive detection of issues, performance analysis, and capacity planning.

Infrastructure Specifics
Firewalls and Security
What are firewalls for? Firewalls act as a barrier, inspecting network traffic based on predefined security rules to determine whether to permit or deny passage. They enforce access control policies based on ports, protocols, and source/destination IP addresses, effectively blocking malicious or unauthorized connections. In this design, a Perimeter Firewall protects the entry point (Load Balancer), and Host Firewalls protect the internal application and database servers.

Why is the traffic served over HTTPS? Traffic is served over HTTPS (HyperText Transfer Protocol Secure) because it uses the SSL/TLS certificate to encrypt data transmitted between the client's browser and the server. This encryption prevents eavesdropping and tampering, protecting sensitive information like login credentials, thereby establishing trust and data integrity.

Monitoring
What monitoring is used for? Monitoring is used for observability. Its primary purposes are:

Alerting: Notifying engineers instantly when critical performance thresholds are breached (e.g., high CPU usage, low disk space).

Troubleshooting: Providing logs and metrics to accurately diagnose the root cause of application errors or outages.

Capacity Planning: Tracking resource utilization over time to predict when servers need scaling or upgrading.

How the monitoring tool is collecting data The monitoring clients (or agents, e.g., Sumologic agent) installed on each server are responsible for collecting data. They typically run in the background, scraping system metrics (CPU, RAM, Disk I/O) and parsing application logs (Nginx access logs, application error logs). These clients then push or transmit this collected data to a centralized monitoring backend for aggregation, analysis, and visualization.

Explain what to do if you want to monitor your web server QPS To monitor the web server's QPS (Queries Per Second), you must configure the monitoring client to specifically collect and count data from the web server:

Log Parsing: Configure the monitoring client to parse the Nginx access logs in real-time, counting the number of successful entries recorded per second or minute.

Status Endpoint: Configure the monitoring client to periodically query a dedicated Nginx status endpoint that provides internal statistics. This allows the system to calculate the request rate (QPS) over time.

Issues with this Infrastructure
Despite the security and monitoring additions, this infrastructure still contains three major architectural weaknesses:

Why terminating SSL at the load balancer level is an issue

Loss of Internal Encryption: If SSL/TLS is terminated at the Load Balancer, traffic sent from the Load Balancer to the application servers is often unencrypted HTTP. This internal network segment becomes vulnerable to sniffing attacks if an attacker compromises the private network. It means encryption is not end-to-end.

Certificate Management SPOF: Having the single SSL certificate only on the Load Balancer creates a single point of failure for the decryption process.

Why having only one MySQL server capable of accepting writes is an issue

Single Point of Failure (SPOF) for Writes: The single Primary MySQL node remains a SPOF. If this node fails, the entire application loses the ability to perform any write or modify operations (e.g., creating a new user, updating inventory). This severely impairs the application's functionality, even if the Replica can still handle read requests.

Why having servers with all the same components (database, web server and application server) might be a problem

Resource Inefficiency and Scaling Waste: This monolithic architecture (also known as a vertical scale-up approach) is inefficient because the three core services (Web/App, Database) have fundamentally different resource requirements (App is CPU-intensive, DB is I/O-intensive). Bundling them prevents independent scaling. If only the application is under load, you must still scale the database component on that machine, wasting resources.

Maintenance Risk: Updates or maintenance on one component (e.g., MySQL patch) could inadvertently affect the functionality of the other components running on the same host, increasing complexity and risk.
