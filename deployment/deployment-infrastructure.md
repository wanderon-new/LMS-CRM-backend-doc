

# Deployment & Infrastructure Guide

## 🚀 Overview

Guidance for running the Lead Processor system end-to-end:

* **Local development** uses Docker Compose
* **Frontend** is deployed on **Vercel** (branch → dev/prod)
* **Backend** is deployed on **DigitalOcean App Platform** (App Service), with environment-variable–driven control over which instance runs the Redis-stream consumer.

---

## 🏗️ Infrastructure Architecture

```mermaid
graph TB
    Browser[Web Browser] --> Vercel[Vercel Frontend]
    Vercel --> API[API Server - DigitalOcean App Platform]

    subgraph Backend_App
      API --> MongoDB[MongoDB Cluster]
      API --> Redis[Redis Cluster]
      API --> LSQ[LeadSquared API]
      API --> Consumers[Redis Stream Consumer - Controlled via ENV]
    end

    subgraph Health_Monitoring
      HealthEndpoint[Health Check - /health]
      HealthEndpoint --> DO[DigitalOcean App Platform]
    end
```

---

## 🌍 Environment Configurations

### Local Development

**Tooling**: Docker Compose (`docker-compose.dev.yml`)

* **Services**: MongoDB, Redis, & App (Next.js + Express)
* **Bindings**:

  * `NODE_ENV=development`
  * `MONGO_URI=mongodb://devuser:devpass123@mongodb:27017/leadprocessor`
  * `REDIS_URI=redis://localhost:6379)`
* **Usage**:

  ```bash
    # Clone the repository
    git clone <repository-url>
    cd lead-processor

    # Enter Nix development shell (auto-installs all dependencies)
    nix develop .#default 

    # Start local services
    docker-compose up -d

    # Install dependencies
    npm install

    # Start development server
    npm run dev
    ```

    ### Option 2: Traditional Setup
    ```bash
    # Install Node.js 20+ from nodejs.org
    # Clone and enter project
    git clone <repository-url>
    cd lead-processor

    # Start local services
    docker-compose up -d

    # Install dependencies
    npm install

    # Start development server
    npm run dev
  ```

### Frontend Deployment

* **Platform**: Vercel, connected to GitHub
* **Branches**:

  * `main` → Production
  * `dev`  → Staging/Preview
* **Env-Vars**: `NEXT_PUBLIC_API_URL` points to the corresponding DO App Platform URL

### Backend Deployment

* **Platform**: DigitalOcean App Platform
* **Services**:

  * **App Service** (container image built from `main` or `dev` branch)
  * **Managed MongoDB & Redis** (DigitalOcean Databases)
* **Environments**:

  * `main` branch → Production
  * `dev`  branch → Staging
* **Consumer Control**:

  * All instances run the API & Redis-stream **producer** by default.
  * **Consumers** (i.e. the background stream processors) are enabled via an env-var flag (e.g. `ENABLE_CONSUMER=true`).
  * In **production**, the consumer flag is set **only** on a separate “worker” instance to isolate load.

---

## 🚀 Deployment Strategies

### Git-Based Auto-Deploy

* **Frontend**: Vercel automatically builds & deploys on push to `dev`/`main`.
* **Backend**: DigitalOcean App Platform automatically rebuilds from `dev`/`main` on push.

### Versioning & Rollbacks

* Use tags (e.g. `v1.2.3`) on your Docker image in DO; App Platform can roll back to previous versions in case of failure.

---

## 🐳 Local Only vs. Cloud

| Environment           | Docker Compose | Vercel | DigitalOcean |
| --------------------- | :------------: | :----: | :----------: |
| **Development**       |        ✓       |    ✕   |       ✕      |
| **Staging (Preview)** |        ✕       |    ✓   |       ✓      |
| **Production**        |        ✕       |    ✓   |       ✓      |

---

## 🛠️ Development Limitations

* **Single Instance**: In `dev`, the same container handles API, Redis-stream production, and consumption.
* **Latency Spike**: Occasional delays on lead creation due to Redis-stream “race” on the shared instance.
* **Workaround**: In prod, stream consumers are isolated to their own instance.

---

## 🔧 Configuration Management

### Key Environment Variables

```bash
# Core App Settings
NODE_ENV=development
PORT=3000
LOG_LEVEL=info
APP_NAME=lead-processor

# MongoDB
MONGO_URI=mongodb://localhost:27017
MONGO_DB_NAME=leadProcessorDB

# Redis
REDIS_URI=redis://localhost:6379
REDIS_DUPLICATION_READ_COUNT=10

# WEST Service
WEST_API_URL=https://api.west.dev
DEFAULT_WEST_NAME=WestBot
DEFAULT_WEST_EMAIL=westbot@example.com
DEFAULT_WEST_PHONE=9999999999

# PSV Service
PSV_API_URL=https://api.psv.dev
DEFAULT_PSV_NAME=PSVBot
DEFAULT_PSV_EMAIL=psvbot@example.com
DEFAULT_PSV_PHONE=8888888888

# LeadSquared
LSQ_ACCESS_KEY=your-lsq-access-key
LSQ_SECRET_KEY=your-lsq-secret-key
LSQ_BASE_URL=https://api.leadsquared.com

# Authentication
MAIN_SEEDER_SECRET=superseedersecret
PASS_SALT_ROUNDS=10
COOKIE_SECRET=supersecretcookie
DEFAULT_PASS=password123
TOKEN_EXPIRY=3600

# Client
CLIENT_DOMAIN=https://client.example.com
CLIENT_APPLICATION=travel-app

# Walrus Integration
WALRUS_APP_2_ACCESS_TOKEN=walrus-app-token-2
WALRUS_API_KEY=walrus-api-key
WALRUS_BASE_URL=https://api.walrus.dev

# Webhooks
PSV_JUNK_DOUBLE_TICK_WEBHOOK=https://webhooks.example.com/psv-junk
WEST_DOUBLE_TICK_ASSIGNMENT_WEBHOOK=https://webhooks.example.com/west-assign
WEST_DOUBLE_TICK_STATUS_SYNC_WEBHOOK=https://webhooks.example.com/west-status
LMS_JUNK_AUTOMATION_TRIGGER_WEBHOOK=https://webhooks.example.com/lms-junk

# Logging - Logtail
LOGTAIL_TOKEN=logtail-token-xyz
LOG_TO_CONSOLE=true
LOG_TO_LOGTAIL=true
LOGTAIL_ENDPOINT=https://in.logtail.com/

# Logging - Elastic
LOG_TO_ELASTIC=false
ELASTIC_NODE=https://elastic-node-url.dev
ELASTIC_API_KEY=elastic-api-key-123
ELASTIC_LOG_INDEX=lead-processor-logs

# OpenTelemetry
OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318
OTEL_EXPORTER_OTLP_HEADERS=Authorization="<your key>"
OTEL_METRICS_EXPORTER=none
OTEL_LOGS_EXPORTER=none
OTEL_RESOURCE_ATTRIBUTES=name=lead-processor,version=1.0.0,environment=development
ELASTIC_OTEL_LOG_INJECTION=true

# Node Options
NODE_OPTIONS=--max_old_space_size=4096

# Consumers
RUN_QUEUE_CONSUMERS=true
START_CDC=true

```
