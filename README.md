# Convex Self-Hosted Traefik Template

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A ready-to-use template for deploying a self-hosted [Convex](https://convex.dev) backend using **Docker Compose** and Traefik for reverse proxying and SSL termination.

This setup allows you to easily run an isolated Convex instance, perfect for development, staging, or production environments. It includes:

- **Convex Backend:** The core Convex runtime.
- **Convex Dashboard:** The web UI for managing your Convex deployment.
- **PostgreSQL:** The required database backend for Convex.
- **Traefik Integration:** Pre-configured labels for automatic HTTPS routing based on domain names.

## Features

- **Easy Configuration:** Simple `.env` file for setting instance name, domain, ports, and credentials.
- **Multi-Instance Ready:** Deploy multiple isolated Convex backends by changing the `APP_NAME` in the `.env` file.
- **Traefik v3 Ready:** Uses modern Traefik Docker labels for automatic service discovery and routing.
- **Persistent Storage:** Data for PostgreSQL and Convex is stored in named Docker volumes.
- **(Optional) Direct Port Exposure:** Uncomment `ports` in `compose.yml` for direct access if needed.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- A server with a publicly accessible IP (for HTTPS with Let's Encrypt via Traefik).
- A Traefik instance configured to watch the `traefik-default` Docker network.
  - Your Traefik must be configured with an entrypoint for `websecure` (e.g., `:443`) and a Let's Encrypt `certResolver` named `letsencrypt`.
  - The network `traefik-default` must exist. Create it with `docker network create traefik-default` if it doesn't.
- (Optional, for direct access) Ensure the ports defined in your `.env` (e.g., `POSTGRES_EXTERNAL_PORT`) are open on your server's firewall.

## Getting Started

1.  **Clone the Repository:**

    ```bash
    git clone https://github.com/Alexandrsv/convex-selfhosted-traefik-template.git my-app
    cd my-app
    ```

2.  **Configure your Instance:**

    - Copy the example environment file:
      ```bash
      cp .env.example .env
      ```
    - Edit the `.env` file:
      ```bash
      nano .env
      ```
    - **Crucial:** Change at least the `APP_NAME` and `DOMAIN`.
    - Update `POSTGRES_USER` and `POSTGRES_PASSWORD` with secure credentials.

3.  **Ensure Traefik Network Exists:**
    Make sure the Docker network `traefik-default` exists:

    ```bash
    docker network ls | grep traefik-default
    ```

    If it's missing, create it:

    ```bash
    docker network create traefik-default
    ```

4.  **Start the Services:**

    ```bash
    docker compose up -d
    ```

5.  **Access your Services (via Traefik):**

    - **Convex Dashboard:** `https://dashboard.<your-domain-from-env>`
    - **Convex API:** `https://api.<your-domain-from-env>`
    - **Convex Site/HTTP Actions:** `https://<your-domain-from-env>`

6.  **Generate Admin Token (for Dashboard Login):**
    Run the provided script to generate an admin token for the Convex Dashboard:
    ```bash
    ./generate_admin_key.sh
    ```
    This script will execute the token generation inside the backend container and display the token in a friendly format.

## Stopping the Services

To stop and remove the containers and volumes:

```bash
# warning: this will delete all stored data including your database!
docker compose down -v
```
