# Secured and Monitored Web Infrastructure

This project demonstrates a secured and monitored web infrastructure that hosts `www.foobar.com` using HAProxy, three backend servers, firewalls, HTTPS encryption, and monitoring clients.

## Infrastructure Components

- **Domain Name (`foobar.com`):** Provides a human-readable name for the website. The `www` subdomain has an A record pointing to the load balancer's IP address (`8.8.8.8`).
- **Load Balancer (HAProxy):** Distributes incoming requests between the three backend servers using the Round Robin algorithm.
- **Firewalls:** Three host-based firewalls filter incoming and outgoing network traffic based on configured security rules, protecting the backend servers from unauthorized connections.
- **TLS Certificate:** Installed on HAProxy to enable HTTPS and encrypt communication between the user's browser and the load balancer.
- **Web Servers (Nginx):** Handle HTTP requests, serve static content, and forward dynamic requests to the application servers.
- **Application Servers:** Execute application logic and communicate with the databases when needed.
- **Application Files:** Contain the application's codebase.
- **Databases (MySQL):** Store and manage application data using a Primary-Replica architecture.
- **Monitoring Clients:** Three monitoring agents collect server metrics and logs to help monitor infrastructure health and performance.

## How It Works

1. The user enters `https://www.foobar.com` in their browser.
2. DNS resolves the domain name to the load balancer's IP address (`8.8.8.8`).
3. The browser establishes an HTTPS connection with HAProxy, where TLS termination takes place.
4. HAProxy distributes the incoming request to one of the three backend servers using Round Robin.
5. The backend server's firewall filters traffic based on its configured rules.
6. Nginx handles the request or forwards it to the application server for dynamic processing.
7. The application server executes the required logic and communicates with MySQL if needed.
8. The response is returned through Nginx and HAProxy to the user's browser.

## Security

### Firewalls

Each backend server has a host-based firewall that controls incoming and outgoing traffic based on predefined rules.

The firewalls help restrict unauthorized access to services and limit unnecessary network exposure.

### HTTPS Encryption

The infrastructure uses HTTPS to encrypt communication between the user's browser and HAProxy.

The TLS certificate allows the browser to verify the server's identity and establish an encrypted connection, protecting data from interception and modification during transmission.

However, since TLS terminates at HAProxy, traffic between the load balancer and backend servers may remain unencrypted.

## Monitoring

Each backend server has a monitoring client that collects information such as:

- CPU and memory utilization
- Disk usage
- Server and application logs
- Service availability
- Database performance

The collected data can be sent to a monitoring service such as Sumo Logic for analysis, visualization, and alerting.

To monitor the web server's **Queries Per Second (QPS)**, the monitoring client can collect Nginx request counts from access logs or metrics and calculate the number of HTTP requests processed per second.

## Database Replication

The infrastructure uses a Primary-Replica MySQL architecture:

- **Primary (Server A):** Handles database writes and can serve read requests.
- **Replica 1 (Server B):** Maintains a copy of the Primary database and handles read requests.
- **Replica 2 (Server C):** Maintains another copy of the Primary database and handles read requests.

All application servers send write operations to the Primary database. The Primary records changes, which are replicated to the Replicas.

This helps distribute database read operations, although replication delays may temporarily cause inconsistent data between the Primary and Replicas.

## Infrastructure Limitations

- **TLS Termination:** Since HTTPS terminates at HAProxy, communication between HAProxy and the backend servers may be unencrypted, exposing internal traffic to potential interception.
- **Single Writable Database:** Only the Primary database accepts writes. If it fails, write operations become unavailable until a Replica is promoted and application connections are updated.
- **Identical Server Components:** Hosting Nginx, the application server, and MySQL on the same machine can cause resource competition. High resource consumption by one component may affect the performance of the others. It also makes independent scaling of individual components more difficult.
- **Single Point of Failure (SPOF):** HAProxy remains a single point of failure. If it becomes unavailable, users cannot access the backend servers through the load balancer.
- **Monitoring Coverage:** The monitoring clients cover the backend servers, but HAProxy is not directly monitored in the current design.
- **Security Coverage:** The three firewalls protect the backend servers, but the load balancer does not have a dedicated firewall in the current design.
