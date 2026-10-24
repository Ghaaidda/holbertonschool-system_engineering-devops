# Distributed Web Infrastructure

This project demonstrates a distributed web infrastructure that hosts `www.foobar.com` using three servers: one load balancer and two additional servers.

## Infrastructure Components

- **Domain Name (`foobar.com`):** Provides a human-readable name for the website. The `www` subdomain has an A record pointing to the load balancer's IP address (`8.8.8.8`).
- **Load Balancer (HAProxy):** Distributes incoming traffic between the three backend servers using the Round Robin algorithm.
- **Web Servers (Nginx):** Handle incoming HTTP requests, serve static content, and forward dynamic requests to the application servers.
- **Application Servers:** Execute the application's logic and communicate with the databases when needed.
- **Application Files:** Contain the codebase executed by the application servers.
- **Databases (MySQL):** Store and manage application data using a Primary-Replica architecture.

## How It Works

1. The user enters `http://www.foobar.com` in their browser.
2. DNS resolves the domain name to the load balancer's IP address (`8.8.8.8`).
3. HAProxy receives the request and forwards it to one of the three backend servers.
4. Nginx handles the request or forwards it to the application server for dynamic processing.
5. The application server executes the required logic and communicates with MySQL if needed.
6. The response is returned through Nginx and HAProxy to the user's browser.

## Load Balancing

The load balancer uses the **Round Robin algorithm** to distribute incoming traffic equally between the three backend servers.

The algorithm selects the next available server in rotation:

`i = (i + 1) % 3`

The infrastructure follows an **Active-Active setup**, meaning all three backend servers actively handle incoming requests.

Unlike Active-Passive, where one server handles traffic while another remains on standby, Active-Active allows better resource utilization and distributes the workload across multiple servers.

## Database Replication

The infrastructure uses a **Primary-Replica MySQL architecture**:

- **Primary (Server A):** Handles database write operations and can also serve read requests.
- **Replica 1 (Server B):** Maintains a copy of the Primary database and serves read requests.
- **Replica 2 (Server C):** Maintains another copy of the Primary database and serves read requests.

Application servers send write operations to the Primary database. The Primary records changes, which are then replicated to the Replicas.

This setup distributes read operations across multiple databases. However, replication may experience delays, meaning Replicas might temporarily contain outdated data.

## Infrastructure Limitations

- **Single Point of Failure (SPOF):** HAProxy remains a single point of failure. If it fails, users cannot access the backend servers through the load balancer. The Primary database is also a critical dependency for write operations.
- **Security:** The infrastructure does not include firewalls or HTTPS encryption, leaving network traffic and services exposed to security risks.
- **No Monitoring:** Without monitoring tools, it is difficult to track server health, resource usage, application errors, and database replication issues.
- **Database Availability:** If the Primary database fails, write operations become unavailable until a Replica is promoted and the application connections are updated.
- **Scalability:** Although traffic is distributed across multiple servers, the infrastructure is still limited by the capacity of its components, particularly the single load balancer and Primary database.
