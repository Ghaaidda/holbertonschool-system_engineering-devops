# Simple Web Stack
![Simple Web Stacl](./whiteboard/0-simple_web_stack.png)
This project demonstrates a simple web infrastructure that hosts `www.foobar.com` on a single server.

## Infrastructure Components

- **Domain Name (`foobar.com`):** Provides a human-readable name for the website. The `www` subdomain has an A record that points to the server's IP address (`8.8.8.8`).
- **DNS:** Resolves `www.foobar.com` to its corresponding IP address so the browser knows where to send the request.
- **Server:** A computer that hosts the web server, application server, application files, and database.
- **Web Server (Nginx):** Handles incoming HTTP/HTTPS requests and forwards dynamic requests to the application server. It can also serve and cache static content, such as images, to improve performance.
- **Application Server:** Executes the application's logic and communicates with the database when needed.
- **Database (MySQL):** Stores and manages the application's data.

## How It Works

1. The user enters `https://www.foobar.com` in their browser.
2. The browser uses DNS to resolve the domain name to `8.8.8.8`.
3. The browser establishes an HTTPS connection with the server through port `443`, using TCP/IP for communication.
4. Nginx receives the request and either handles it directly or forwards it to the application server.
5. The application server executes the required logic and communicates with MySQL if needed.
6. The response is returned through Nginx to the user's browser.

## Why Separate the Web Server and Application Server?

Although some web servers can handle application logic, we chose to separate two different responsibilities: traffic handling and dynamic execution of program logic.

This separation can improve performance, as Nginx can serve and cache static content without involving the application server. It also acts as a reverse proxy, making the implementation of security mechanisms easier and more convenient.

## Infrastructure Limitations

- **Single Point of Failure (SPOF):** Since the whole web stack is hosted on one server, if the server fails, the entire website becomes unavailable.
- **Maintenance Downtime:** Deploying new code or restarting services may require downtime, making the website temporarily unavailable.
- **Limited Scalability:** If incoming traffic exceeds the server's capacity, the website may slow down, requests may time out, or services may crash. The server can be upgraded vertically, but horizontal scaling requires changes to the infrastructure.
