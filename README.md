# Docker Multi-Website Hosting with Nginx Proxy Manager

## Project Overview

This project demonstrates how to host multiple websites using Docker containers and Nginx Proxy Manager (NPM).

A single reverse proxy receives incoming HTTP requests and forwards them to the appropriate backend web server based on the requested domain name.

The project also demonstrates load balancing by distributing requests between two backend servers.

## Architecture

```text
                  User Browser
                       |
                       |
                 HTTP Request
                       |
                       v
             Nginx Proxy Manager
                 (Reverse Proxy)
                       |
          +------------+------------+
          |            |            |
          v            v            v
       Company       Portal       Public
       Website       Website      Website
          |            |            |
          v            v            v
       Server A     Server B    Load Balancer
                                   /    \
                                  v      v
                             Server A  Server B
```

**Note:** Server A and Server B are simulated backend services running as Docker containers in the same AWS EC2 instance.

## Technologies Used

- Docker
- Docker Compose
- Nginx Proxy Manager
- Nginx
- AWS EC2
- Linux
- Git and GitHub

## Project Requirements

- AWS EC2 instance running Linux
- Docker installed
- Docker Compose plugin installed
- Nginx Proxy Manager
- Three website domains for hostname-based routing

## Website Configuration

| Domain | Backend | Purpose |
|---|---|---|
| company.example.com | Server A | Company website |
| portal.example.com | Server B | Internal portal |
| www.example.com | Server A and Server B | Load-balanced website |

## Container Structure

The target architecture uses three containers:

1. **npm-proxy** – Handles reverse proxy and hostname-based routing.
2. **server-a** – Hosts the company website and serves as a load-balancing backend.
3. **server-b** – Hosts the portal website and serves as another load-balancing backend.

## Installation and Setup

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR-USERNAME/docker-multisite-lab.git

cd docker-multisite-lab
```

### 2. Start Docker Containers

```bash
sudo docker compose up -d
```

### 3. Check Running Containers

```bash
sudo docker compose ps
```

### 4. Access Nginx Proxy Manager

Open the following URL in your browser:

```text
http://EC2-PUBLIC-IP:81
```

Configure the Proxy Hosts using the domain and backend details listed above.

## Load Balancing

The public website is configured to use two backend servers.

```text
                 www.example.com
                        |
                        v
                  Nginx Proxy
                        |
                        v
                  common_backend
                    /       \
                   v         v
               Server A   Server B
```

Requests are distributed between the two backend servers using Nginx upstream configuration.

## Testing

### Test Company Website

```bash
curl --resolve company.example.com:80:127.0.0.1 \
http://company.example.com
```

### Test Portal Website

```bash
curl --resolve portal.example.com:80:127.0.0.1 \
http://portal.example.com
```

### Test Load Balancing

```bash
for i in $(seq 1 10); do
  curl -s --resolve www.example.com:80:127.0.0.1 \
  http://www.example.com | grep 'Backend:'
done
```

Check whether responses are coming from both backend servers.

## Security

- Access Lists can protect internal websites using username and password authentication.
- Use HTTPS for production websites.
- Restrict the NPM administration port (81) to trusted IP addresses.
- Never upload passwords, private keys, certificates or sensitive configuration files to GitHub.

## Important Notes

- `example.com` is a reserved documentation domain. Replace these sample domains with domains you own for real public DNS and HTTPS deployment.
- The backend servers in this lab run as Docker containers on one EC2 instance. They are not independent physical or virtual servers.
- The three-container architecture is the intended final design. Adjust the Compose file and Nginx configuration to match it.

## Author

**Docker Multi-Website Hosting Project**

## Learning Outcomes

- Understanding Docker containers and Docker Compose.
- Understanding reverse proxy architecture.
- Configuring hostname-based routing.
- Hosting multiple websites.
- Understanding Nginx upstream configuration.
- Implementing load balancing.
- Managing internal website access using authentication.
