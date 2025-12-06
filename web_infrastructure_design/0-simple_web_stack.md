Simple Web Stack Design: The User Request Flow
The entire process begins when a User enters the URL www.foobar.com into their web browser.

DNS Lookup: The user's computer queries a DNS server to translate the human-readable domain name, www.foobar.com, into the server's numerical IP address (8.8.8.8).

Request to Server: The browser then sends an HTTP request to that IP address, pointing directly to the single Server.

Web Server (Nginx): The Nginx web server accepts the request. It acts as a gatekeeper:

For static files (like images, CSS), Nginx serves the file directly.

For dynamic pages, Nginx forwards the request to the Application Server.

Application Server: This component executes the application's dynamic business logic contained within the Application Files (your code). If data is needed, it queries the MySQL database.

Database (MySQL): The MySQL database retrieves or stores the required structured data and returns the result to the Application Server.

Response: The Application Server generates the final dynamic content, which Nginx then sends as an HTTP response back across the network to the user's browser for display.

Specifics of the Infrastructure
What is a Server: A server is a machine (hardware or virtual) that manages access to centralized resources and services in a network. In this case, it's a single machine hosting all web stack components.

Role of the Domain Name: The domain name (foobar.com) provides a human-readable and memorable address, eliminating the need for users to recall the IP address.

Type of DNS record: The www in www.foobar.com is typically an A record (Address record) that maps the hostname directly to the IPv4 address (8.8.8.8).

Role of the Web Server (Nginx): Nginx is responsible for handling client connections, serving static content, and acting as a reverse proxy to pass dynamic requests to the application layer.

Role of the Application Server: This executes the application code, processes user input, and manages the logic necessary to generate dynamic content, often by interacting with the database.

Role of the Database (MySQL): The database provides persistent storage, organization, and efficient retrieval of the application's structured data.

Communication Protocol: The server communicates with the user's computer primarily using the HTTP (HyperText Transfer Protocol) over TCP/IP.

Issues with this Infrastructure
This single-server setup presents significant risks and limitations for a production environment.

1. Single Point of Failure (SPOF)
The entire infrastructure relies on this one server. If the server fails (hardware malfunction, software crash, or an issue with the database service), the whole website becomes inaccessible. The server's IP address (8.8.8.8) is the definitive SPOF.

2. Downtime When Maintenance is Needed
Any essential maintenance, such as deploying new code, patching the operating system, or upgrading a service like Nginx, often requires a service restart or server reboot. Since there is only one server, any such procedure results in total website downtime.

3. Cannot Scale if Too Much Incoming Traffic
The single server must handle all tasks: serving static files, running application code, and processing database queries. If the volume of requests per second (QPS - Queries Per Second) increases significantly, the server's limited resources (CPU, RAM, I/O) will be quickly overwhelmed. This inability to scale horizontally makes the system unstable under high load.
