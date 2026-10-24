# Scale Up Web Infrastructure

This project demonstrates a scaled-up web infrastructure that hosts `www.foobar.com` using two HAProxy load balancers configured as a cluster, with the web server, application server, and database hosted on separate servers.

## Infrastructure Components

- **Domain Name (`foobar.com`):** Provides a human-readable name for the website. The `www` subdomain has an A record pointing to the shared Virtual IP (`8.8.8.8`).
- **Load Balancers (HAProxy):** Two HAProxy instances are configured in an Active-Passive cluster to provide redundancy and prevent the load balancer from becoming a single point of failure.
- **Keepalived:** Manages the shared Virtual IP (VIP) using VRRP. If the active load balancer fails, the standby instance takes over the VIP, allowing traffic to continue reaching the infrastructure.
- **Web Server (Nginx):** Hosted on a dedicated server to handle HTTP requests, serve static content, and forward dynamic requests to the application server.
- **Application Server:** Hosted on a separate server to execute application logic and communicate with the database when needed.
- **Application Files:** Contain the application's codebase, which is executed by the application server.
- **Database (MySQL):** Hosted on a dedicated server to store and manage application data.
- **Firewalls:** Control incoming and outgoing network traffic based on configured security rules.
- **TLS Certificates:** Enable HTTPS encryption between the user's browser and the active load balancer.
- **Monitoring Clients:** Collect server metrics and logs to monitor infrastructure health and performance.

## How It Works

1. The user enters `https://www.foobar.com` in their browser.
2. DNS resolves the domain name to the shared Virtual IP (`8.8.8.8`).
3. The active HAProxy instance receives the HTTPS request and terminates TLS.
4. HAProxy forwards the request to the dedicated Nginx server.
5. Nginx handles static content or forwards dynamic requests to the application server.
6. The application server executes the required logic and communicates with MySQL if needed.
7. The response is returned through Nginx and the active HAProxy instance to the user's browser.

## Load Balancer Clustering

The infrastructure uses two HAProxy instances configured in an **Active-Passive setup**:

- **HAProxy 1 (`8.8.8.9`):** Initially active and responsible for handling incoming requests.
- **HAProxy 2 (`8.8.8.10`):** Remains on standby and takes over when the active instance fails.
- **Shared VIP (`8.8.8.8`):** Provides a single IP address for clients to access the website.

Keepalived uses VRRP to coordinate VIP ownership between the two load balancers. Health checks can also be configured to detect HAProxy service failures and trigger failover.

This setup improves availability by reducing the risk of downtime caused by a single load balancer failure.

## Separation of Components

Unlike the previous infrastructure, where the web server, application server, and database were hosted together, this architecture separates them onto dedicated servers.

- **Web Server:** Handles HTTP traffic independently of application execution and database operations.
- **Application Server:** Processes application logic without competing directly with Nginx or MySQL for the same machine's resources.
- **Database Server:** Manages database operations independently, allowing its resources to be optimized for data storage and queries.

This separation reduces resource competition, improves maintainability, and allows each component to be scaled independently.

## Infrastructure Limitations

- **Single Points of Failure:** Although the load balancer is redundant, Nginx, the application server, and MySQL are each hosted on a single server. Failure of any of these components can affect website availability.
- **Limited Backend Redundancy:** The infrastructure has only one web server and one application server, so there are no additional instances to handle requests if either fails.
- **TLS Termination:** Since HTTPS terminates at HAProxy, communication between the load balancer and Nginx may remain unencrypted.
- **Database Availability:** MySQL runs on a single server without replication or automatic failover, making it a single point of failure.
- **Firewall Coverage:** The load balancer machines do not have dedicated firewalls shown in the current design.
