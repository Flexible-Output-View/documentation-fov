# Deployment Guide

> **Last Updated:** October 5th, 2026

This guide outlines how to deploy the web application stack using your preferred architecture. Choose the method that best fits your environment, whether you prefer building from source, utilizing pre-built container images via GitHub Packages (GHCR), or running natively without Docker.

---

## Choose Your Deployment Approach

1. **[Docker Compose (Recommended)](#method-1-docker-compose-deployment):** The easiest way to run the entire multi-container stack. Ideal for most homelabbers and standard deployments.
2. **[Standalone Docker CLI](#method-2-standalone-docker-cli-deployment):** Run individual `docker run` commands within a custom network. Best for users who want granular control over each container without Compose.
3. **[Native / Bare Metal](#method-3-native--bare-metal-deployment):** Run the application directly on your host machine's OS without containerization.

---

## Prerequisites

Before you begin, ensure your host machine is prepared:

* **Docker & Docker Compose:** Must be installed on your system. [Official Installation Guide](https://docs.docker.com/engine/install/?utm_source=gemini).
* **Git:** Required only if you plan to clone the repository and build from source.
* **Environment Configuration:** See the [Environment Variables Reference](#environment-variables-reference) for required values.

---

## Method 1: Docker Compose Deployment

This method uses Docker Compose to spin up the Database, Backend, and Frontend simultaneously.

### Option A: Build from Source (Local Repository)

Use this option if you have cloned the repository and want to build the images locally.

1. **Set up your environment variables:**

```bash
cp .env.example .env
```

*Open the `.env` file and customize the values (passwords, JWT secrets, etc.).*

2. **Create media directories and assign permissions:**

```bash
mkdir -p ./backend/media/hls
chmod -R 777 ./backend/media
```

3. **Build and start the stack:**

```bash
docker compose up -d --build
```

> [!TIP]
> You can also use the provided local script: `./deploy-local.sh`

### Option B: Use Pre-Built Images (GHCR)

Use this option for a faster deployment using stable, pre-built images. Because you are not cloning the repository, you will need to create your own `docker-compose.yml` file.

1. **Create your environment file:**
Create an `.env` file in your directory and populate it with the required variables (see [Reference](#environment-variables-reference)).
2. **Create media directories and assign permissions:**

```bash
mkdir -p ./backend/media/hls
chmod -R 777 ./backend/media
```

3. **Create the Docker Compose file:**
Create a file named `docker-compose.yml` and paste the following configuration:

```yaml
services:
  backend:
    image: ghcr.io/flexible-output-view/fov-backend:main
    restart: unless-stopped
    volumes:
      - ./backend/media:/var/media
    ports:
      - "4000:4000" # API
      - "9999-10010:9999-10010"
      - "9999-10010:9999-10010/udp" # SRT
    networks:
      - frontend
      - backend
    depends_on:
      fov-db:
        condition: service_healthy

    environment:
      - PORT=4000
      - DB_HOST=fov-db
      - DB_PORT=5432
      - DB_USER=root
      - SRT_PORT=9999
      - DB_PASSWORD=${DB_PASSWORD}
      - DB_NAME=fovwebdb
      - FFMPEG_PATH=/usr/local/bin/ffmpeg
      - MEDIA_ROOT=/var/media
      - SRT_URL=${SRT_URL}
      - API_HOSTNAME=${API_URL}
      - TWITCH_ID=${TWITCH_ID}
      - TWITCH_SECRET=${TWITCH_SECRET}
      - JWT_SECRET=${JWT_SECRET}
      - JWT_EXPIRES_IN=${JWT_EXPIRES_IN}

  frontend:
    image: ghcr.io/flexible-output-view/fov-frontend:main
    restart: unless-stopped
    depends_on:
      backend:
        condition: service_started
    ports:
      - "4200:80"
    networks:
      - frontend
    environment:
      - API_URL=http://backend:4000

  fov-db:
    image: postgres:18-bookworm
    restart: unless-stopped
    volumes:
      - fov-db:/var/lib/postgresql
      - ./fovwebdb.sql:/docker-entrypoint-initdb.d/init.sql  # NOTE: Ensure you provide the initial sql file, or remove this mount if handling the database separately.
    networks:
      - backend

    healthcheck:
      test: [ "CMD-SHELL", "pg_isready -U root -d fovwebdb" ]
      interval: 10s
      timeout: 5s
      retries: 10

    environment:
      POSTGRES_DB: fovwebdb
      POSTGRES_USER: root
      POSTGRES_PASSWORD: ${DB_PASSWORD}

networks:
  frontend:
  backend:
    internal: true

volumes:
  fov-db:
```

4. **[Download the initial database sql file](https://raw.githubusercontent.com/Flexible-Output-View/web-fov/refs/heads/main/fovwebdb.sql)** and save it to `fovwebdb.sql` in the same directory as your docker compose.
5. **Pull images and start the stack:**

```bash
docker compose pull
docker compose up -d
```

### Local Endpoints Overview

Once the stack is running on default settings, your services are available at:

* **Frontend Web App:** `http://localhost:4200`
* **API Backend:** `http://localhost:4000`
* **Stream Ingest (SRT):** Ports `9999-10010` (TCP/UDP)
* **Database:** `localhost:5432`

> [!WARNING]
> Default configurations bind services directly to your host machine. This is **not** recommended for public-facing production environments. See the Production Guide below.

---

## Method 2: Standalone Docker CLI Deployment

If you prefer to manage containers manually without Docker Compose, follow these steps to isolate the stack on a custom bridge network.

**1. Create a custom network:**

```bash
docker network create fov-network
```

**2. Deploy the Database:**
*If using pre-built images, please [**download the initial database sql file**](https://raw.githubusercontent.com/Flexible-Output-View/web-fov/refs/heads/main/fovwebdb.sql)*

```bash
docker run -d \
  --name fov-db \
  --network fov-network \
  -v fov-db:/var/lib/postgresql \
  -v $(pwd)/fovwebdb.sql:/docker-entrypoint-initdb.d/init.sql \
  -e POSTGRES_DB=fovwebdb \
  -e POSTGRES_USER=root \
  -e POSTGRES_PASSWORD=your_secure_db_password_here \
  postgres:18-bookworm
```

**3. Deploy the Backend:**
*If using pre-built images, replace `fov-backend` with `ghcr.io/flexible-output-view/fov-backend:main` in the command below.*

```bash
docker run -d \
  --name backend \
  --network fov-network \
  -p 4000:4000 \
  -p 9999-10010:9999-10010 \
  -p 9999-10010:9999-10010/udp \
  -v $(pwd)/backend/media:/var/media \
  -e PORT=4000 \
  -e DB_HOST=fov-db \
  -e DB_PORT=5432 \
  -e DB_USER=root \
  -e DB_PASSWORD=your_secure_db_password_here \
  -e DB_NAME=fovwebdb \
  -e FFMPEG_PATH=/usr/local/bin/ffmpeg \
  -e MEDIA_ROOT=/var/media \
  -e SRT_PORT=9999 \
  -e SRT_URL=localhost \
  -e API_HOSTNAME=http://localhost:4000/api \
  -e JWT_SECRET=your_jwt_secret_here \
  -e JWT_EXPIRES_IN=7d \
  fov-backend
```

**4. Deploy the Frontend:**
*If using pre-built images, replace `fov-frontend` with `ghcr.io/flexible-output-view/fov-frontend:main` in the command below.*

```bash
docker run -d \
  --name frontend \
  --network fov-network \
  -p 4200:80 \
  -e API_URL=http://backend:4000 \
  fov-frontend
```

---

## Method 3: Native / Bare Metal Deployment

To run the application directly on your host operating system (without Docker):

* **Backend Setup:** Refer to the [backend documentation](./docs-backend/DEPLOYMENT.md) for configuring a native Node.js and FFmpeg environment.
* **Frontend Setup:** Refer to the [frontend documentation](./docs-frontend/DEPLOYMENT.md) for running the Angular application natively via Node.js and the Angular CLI.

---

## Production Deployment (Nginx Proxy Manager)

For production environments, the application stack should sit behind a reverse proxy like **Nginx Proxy Manager (NPM)**. This secures traffic over standard HTTP/HTTPS ports (`80`/`443`) and safely hides internal database and API ports from the public internet.

> [!IMPORTANT] Hardware & Bandwidth Considerations
> **High Network Bandwidth:** Ingesting live video feeds and serving HLS chunks consumes massive inbound and outbound data.
>
> **Compute Limits:** Every active live stream spawns a dedicated FFmpeg process. Provision your server's CPU accordingly based on your expected concurrent streams.

**1. Create a shared external network:**

```bash
docker network create nginx-proxy-network
```

**2. Deploy Nginx Proxy Manager:**
Deploy this NPM stack connected to the shared network:

```yaml
services:
  app:
    image: 'jc21/nginx-proxy-manager:2.15.1'
    restart: unless-stopped
    ports:
      - '80:80'   # Public HTTP
      - '443:443' # Public HTTPS
      - '81:81'   # Admin Panel
    environment:
      TZ: "Europe/Paris"
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    networks:
      - nginx-proxy-network

networks:
  nginx-proxy-network:
    external: true
```

**3. Use the Production Override File (`docker-compose.prod.yml`):**
The docker-compose.prod.yml file restricts public ports to loopback/SRT streams and joins the nginx-proxy-network. You can customize it to your needs:

```yaml
services:
  backend:
    ports: !override
      - "127.0.0.1:4001:4000"
      - "0.0.0.0:9999-10010:9999-10010"
      - "0.0.0.0:9999-10010:9999-10010/udp"
    networks:
      - nginx-proxy-network

  frontend:
    ports: !override
      - "127.0.0.1:4201:80"
    networks:
      - nginx-proxy-network

  fov-db:
    ports: !override
      - "127.0.0.1:5432:5432"

networks:
  nginx-proxy-network:
    external: true
```

**4. Deploy the application with production overrides:**
Update your `.env` file with production domain names, then start the stack:

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d --build
```

**5. Configure Proxy Hosts in NPM:**
Log into NPM (port `81`) and map your domains to the internal Docker containers:

* **Frontend Host:** Forward to container `frontend` on port `80` (Enable Force SSL).
* **Backend API Host:** Forward to container `backend` on port `4000` (Enable Force SSL).

*(Note: Stream ingestion ports `9999-10010` bypass the proxy and remain publicly accessible for your SRT streaming sources).*

---

## Appendix: Architecture & Configuration Reference

### Environment Variables Reference

These core variables must be defined in your `.env` file for the application to run.

| Variable | Description | Default / Example |
| --- | --- | --- |
| `DB_PASSWORD` | Secure root password for PostgreSQL. | `your_secure_db_password_here` |
| `SRT_URL` | Hostname or IP for SRT video streaming endpoints. | `localhost` |
| `API_URL` | Publicly accessible URL pointing to the backend API. | `http://localhost:4000/api` |
| `JWT_SECRET` | Secret key for generating JSON Web Tokens. | `your_jwt_secret_here` |
| `JWT_EXPIRES_IN` | Token expiration timeframe. | `7d` |
| `TWITCH_ID` | *(Deprecated soon)* Twitch API client ID. | `your_twitch_id_here` |
| `TWITCH_SECRET` | *(Deprecated soon)* Twitch API client secret. | `your_twitch_secret_here` |

### Docker Volumes & Storage

| Volume / Mount | Target Container | Purpose |
| --- | --- | --- |
| `./backend/media:/var/media` | Backend | Stores persistent media assets and generated HLS video segments. |
| `fov-db:/var/lib/postgresql` | Database | Named volume ensuring database records persist across restarts. |
| `./fovwebdb.sql:/docker-entrypoint-initdb.d/init.sql` | Database | Seeds the schema and initial data on first-time initialization. |

### Docker Networking

| Network Name | Type | Purpose |
| --- | --- | --- |
| `frontend` | Bridge | Connects the frontend to the backend API for web traffic. |
| `backend` | Internal Bridge | Completely isolates the database so it is exclusively reachable by the backend. |
| `nginx-proxy-network` | External Bridge | Used to route traffic securely through an external reverse proxy (Production only). |

### Internal Container Variables (Hardcoded in Compose)

For users modifying the architecture, these are the internal routing variables configured within the Docker Compose files.

| Variable | Target | Default Value | Description |
| --- | --- | --- | --- |
| `PORT` | Backend | `4000` | Internal port that the Node.js backend listens on. |
| `DB_HOST` | Backend | `fov-db` | Internal Docker network hostname to reach the database. |
| `DB_PORT` | Backend | `5432` | Port number for database communication. |
| `DB_USER` | Backend | `root` | Database user profile name. |
| `DB_NAME` | Backend | `fovwebdb` | Exact target database name. |
| `SRT_PORT` | Backend | `9999` | Base port for stream ingest. *Scaling this dictates max streams.* |
| `FFMPEG_PATH` | Backend | `/usr/local/bin/ffmpeg` | Absolute path inside the container where FFmpeg is installed. |
| `MEDIA_ROOT` | Backend | `/var/media` | Directory mapped for persistent media and HLS storage. |
| `POSTGRES_DB` | Database | `fovwebdb` | Instructs PostgreSQL to automatically create a database on startup. |
| `POSTGRES_USER` | Database | `root` | Administrative user account created for the database instance. |
| `API_URL` | Frontend | `http://backend:4000` | Internal route via Docker DNS pointing to the backend API. |
