# ⚡ DataPulse — Distributed Telemetry & Real-Time Anomaly Diagnostics Platform

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Flask-3.0+-000000?style=flat&logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![Ariadne GraphQL](https://img.shields.io/badge/Ariadne-GraphQL_Schema_First-E10098?style=flat&logo=graphql&logoColor=white)](https://ariadnegraphql.org/)
[![Next.js](https://img.shields.io/badge/Next.js-16.3-000000?style=flat&logo=next.js&logoColor=white)](https://nextjs.org/)
[![React](https://img.shields.io/badge/React-19.2-61DAFB?style=flat&logo=react&logoColor=black)](https://react.dev/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.7-3178C6?style=flat&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15+-4169E1?style=flat&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Redis](https://img.shields.io/badge/Redis-Pub%2FSub_%26_Cache-DC382D?style=flat&logo=redis&logoColor=white)](https://redis.io/)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3.4-38B2AC?style=flat&logo=tailwind-css&logoColor=white)](https://tailwindcss.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A high-assurance, distributed telemetry streaming pipeline, operational observability dashboard, and automated anomaly diagnostics engine built for microservice monitoring, high-throughput event processing, and incident mitigation.

The platform provides end-to-end telemetry ingestion across distributed microservices (`auth-service`, `payment-gateway`, `order-router`, `search-indexer`, `database-pool`), real-time event broadcasting via Server-Sent Events (SSE) and Redis Pub/Sub, a schema-first GraphQL API runtime with Apollo Client integration, low-latency query caching with automated invalidation, automated root-cause analysis (RCA) and remediation playbooks, and an in-browser GraphQL query inspector.

---

## 📑 Table of Contents

- [Architecture Overview](#-architecture-overview)
- [Repository Structure (Submodules)](#-repository-structure-submodules)
- [Key Features](#-key-features)
  - [Operational Observability Dashboard](#operational-observability-dashboard)
  - [Telemetry Ingestion & Real-Time Streaming](#telemetry-ingestion--real-time-streaming)
  - [Automated Anomaly Diagnostics & RCA](#automated-anomaly-diagnostics--rca)
  - [High-Performance Caching & Resilience](#high-performance-caching--resilience)
- [Telemetry & Diagnostic Model](#-telemetry--diagnostic-model)
- [Tech Stack](#-tech-stack)
  - [Backend](#backend)
  - [Frontend](#frontend)
- [Quick Start & Local Setup](#-quick-start--local-setup)
  - [Prerequisites](#prerequisites)
  - [1. Clone Repository (with Submodules)](#1-clone-repository-with-submodules)
  - [2. Backend Setup](#2-backend-setup)
  - [3. Frontend Setup](#3-frontend-setup)
- [Environment Configuration](#-environment-configuration)
  - [Backend (`data-pulse-backend/.env`)](#backend-data-pulse-backendenv)
  - [Frontend (`data-pulse-frontend/.env.local`)](#frontend-data-pulse-frontendenvlocal)
- [API Reference](#-api-reference)
  - [GraphQL Operations](#graphql-operations)
  - [REST & Streaming Endpoints](#rest--streaming-endpoints)
- [Working with Git Submodules](#-working-with-git-submodules)
  - [Pulling the Latest Changes](#pulling-the-latest-changes)
  - [Making Changes in a Submodule](#making-changes-in-a-submodule)
- [License](#-license)

---

## 🏗️ Architecture Overview

The project is structured as a parent orchestration repository that links the frontend and backend services through **Git Submodules**:

```
                                  ┌────────────────────────────────────────┐
                                  │           Client / Browser             │
                                  │   (Next.js Observability Dashboard)    │
                                  └──────────────────┬─────────────────────┘
                                                     │
                                       GraphQL & SSE Telemetry Stream
                                                     │
                                                     ▼
                                  ┌────────────────────────────────────────┐
                                  │         Flask 3.0 API Service          │
                                  │          data-pulse-backend            │
                                  └──────┬───────────────┬───────────────┬─┘
                                         │               │               │
                            SQLAlchemy   │   Redis Cache │  Redis PubSub │
                            PostgreSQL   │   & Key Inval │  Event Stream │
                                         │               │               │
                                         ▼               ▼               ▼
                                  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
                                  │ PostgreSQL  │ │ Redis Cache │ │ Live Client │
                                  │  Database   │ │ (TTL 10s)   │ │ Connections │
                                  └─────────────┘ └─────────────┘ └─────────────┘
                                         │
                                         ▼
                            ┌─────────────────────────┐
                            │ Anomaly Diagnostic      │
                            │ Engine (RCA & Mitigate) │
                            └─────────────────────────┘
```

---

## 📂 Repository Structure (Submodules)

This parent repository links the two standalone repositories that make up the system:

| Submodule | Description | Repository Link |
| :--- | :--- | :--- |
| **`data-pulse-backend`** | Python 3 (Flask) telemetry backend API with Ariadne GraphQL, SQLAlchemy, PostgreSQL, Redis Pub/Sub & Caching, SSE streaming, and AI anomaly diagnosis. | [Backend Repo](https://github.com/gurjotsingh1774/data-pulse-backend) |
| **`data-pulse-frontend`** | Next.js 16, React 19, and TypeScript operational dashboard with real-time SSE feed, anomaly alert modals, metric cards, and in-browser GraphQL inspector. | [Frontend Repo](https://github.com/gurjotsingh1774/data-pulse-frontend) |

---

## ✨ Key Features

### Operational Observability Dashboard
- **Live Telemetry Stream:** Consumes high-frequency metric feeds over Server-Sent Events (SSE) with auto-reconnect, pause/resume toggling, buffer capacity controls, and latency flags.
- **Service Health Cards:** Instant aggregate status indicators tracking `HEALTHY`, `WARNING`, and `CRITICAL` metric frequencies plus rolling average P99 latencies per service.
- **In-Browser GraphQL Inspector:** Interactive query execution console allowing on-the-fly execution of GraphQL operations against the backend with full JSON syntax display.
- **Multi-Service Filtering & Search:** Filter streams and summaries across microservices (`auth-service`, `payment-gateway`, `order-router`, `search-indexer`, `database-pool`) and operational severities.
- **Deep Historical Insights:** Dedicated `/insights` view visualizing historical operational anomalies, deviation patterns, and systemic root causes.

### Telemetry Ingestion & Real-Time Streaming
- **Schema-First GraphQL Runtime:** Powered by Ariadne for strongly typed query, mutation, and subscription execution.
- **Real-Time Telemetry Broadcasting:** HTTP-based Server-Sent Events (`/events`) powered by Redis Pub/Sub channels (`telemetry:stream`) with automatic keep-alive ping cycles.
- **Autonomous Telemetry Simulator:** Built-in background daemon generating representative Poisson and Gaussian telemetry events across 5 microservices, injecting realistic anomaly spikes for load testing.

### Automated Anomaly Diagnostics & RCA
- **Automated Root-Cause Analysis (RCA):** Algorithmic anomaly diagnostic service assessing metric deviations from baseline SLAs.
- **Mitigation Playbooks:** Recommends targeted, actionable remediation steps for P99 latency spikes, CPU exhaustion, memory leaks, and error rate surges.
- **Interactive Anomaly Modal:** Instant visual pop-up providing engineers with context-rich deviation data and immediate troubleshooting steps.

### High-Performance Caching & Resilience
- **Multi-Tier Query Caching:** Redis caching for aggregate queries (`getMetrics`, `getServiceSummaries`) with configurable TTL (`REDIS_CACHE_TTL`).
- **Targeted Cache Invalidation:** Automated invalidation of stale cache keys upon new metric ingestion to ensure data fresh consistency.
- **Dual-Layer Database Fallback:** SQLAlchemy 2.0 configuration with connection pooling and transparent fallback to SQLite (`sqlite:///./datapulse_local.db`) for offline development.
- **Stream Resilience:** Seamless fallback to database polling if Redis is temporarily unreachable.

---

## 🔍 Telemetry & Diagnostic Model

Telemetry points are evaluated against predefined SLA thresholds and classified into three operational categories:

| Status | Condition | Severity / Behavior |
| :--- | :--- | :--- |
| **`HEALTHY`** | `metricValue <= thresholdLimit` | Normal operations. Ingested, indexed, and aggregated into rolling metrics. |
| **`WARNING`** | `metricValue > thresholdLimit` (within 1.5x) | Elevated operational stress. Highlighted in service health cards. |
| **`CRITICAL`** | `metricValue > thresholdLimit * 1.5` | SLA breach. Emits critical event, surfaces interactive anomaly alert modal, and generates RCA. |

### Diagnostic Playbooks

- **P99 Latency (`p99_latency_ms`):** Identifies database connection contention or thread pool saturation; recommends Redis cache flushes, slow-query inspection, and pod scaling.
- **CPU Utilization (`cpu_utilization_pct`):** Identifies runaway thread execution or payload serialization loops; recommends quota scaling, rate limiting, and thread profiling.
- **Error Rate (`error_rate`):** Flags downstream provider timeouts or unhandled connection retries; recommends circuit breaker triggers and fallback responses.
- **Memory Footprint (`memory_usage_mb`):** Detects uncollected heap garbage and memory leaks; recommends process heap dumps and worker container restarts.

---

## 💻 Tech Stack

### Backend
- **Language:** Python 3.10+ / 3.12+
- **Framework:** Flask 3.0+ & Flask-CORS
- **GraphQL Engine:** Ariadne 0.23+ (`schema-first`)
- **Database & ORM:** PostgreSQL 15+ / SQLite with SQLAlchemy 2.0+
- **Cache & Pub/Sub:** Redis 5.0+ (`redis-py`)
- **WSGI Production Server:** Gunicorn 21.2+
- **Testing:** Pytest 8.0+

### Frontend
- **Framework:** Next.js 16.3+ (App Router)
- **Library:** React 19.2+
- **Language:** TypeScript 5.7+
- **GraphQL Client:** Apollo Client 3.12+
- **Styling:** Tailwind CSS 3.4+ & PostCSS
- **Icons:** Lucide React (`lucide-react`)
- **Utilities:** `clsx`, `tailwind-merge`

---

## 🚀 Quick Start & Local Setup

### Prerequisites

Ensure you have the following installed on your machine:
- [Git](https://git-scm.com/) (version 2.13+)
- [Python](https://www.python.org/downloads/) (version 3.10 or newer)
- [Node.js](https://nodejs.org/) (version 18.18 or newer) & npm
- [PostgreSQL](https://www.postgresql.org/) & [Redis](https://redis.io/) *(Optional; SQLite and in-memory polling fallbacks are included)*

---

### 1. Clone Repository (with Submodules)

Clone the repository and automatically initialize and clone all submodules:

```bash
# Using SSH (recommended)
git clone --recurse-submodules git@github.com:gurjotsingh1774/data-pulse.git

# Or using HTTPS
git clone --recurse-submodules https://github.com/gurjotsingh1774/data-pulse.git

cd data-pulse
```

> **Note:** If you already cloned without `--recurse-submodules`, initialize them with:
> ```bash
> git submodule update --init --recursive
> ```

---

### 2. Backend Setup

1. Navigate to the backend directory:
   ```bash
   cd data-pulse-backend
   ```

2. Create and activate a Python virtual environment:
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

3. Install backend dependencies:
   ```bash
   pip install -r requirements.txt
   ```

4. Configure environment variables:
   ```bash
   cp .env.example .env
   ```

5. Start the backend development server:
   ```bash
   python app.py
   ```

The backend server will start on `http://localhost:5000`:
- **Health Check:** `http://localhost:5000/health`
- **GraphiQL Explorer:** `http://localhost:5000/graphql`
- **SSE Stream:** `http://localhost:5000/events`

---

### 3. Frontend Setup

1. Open a new terminal and navigate to the frontend directory:
   ```bash
   cd data-pulse-frontend
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Configure environment variables:
   ```bash
   cp .env.example .env.local
   ```

4. Launch the Next.js development server:
   ```bash
   npm run dev
   ```

Open `http://localhost:3000` in your browser.

---

## ⚙️ Environment Configuration

### Backend (`data-pulse-backend/.env`)

| Variable | Required | Default | Description |
| :--- | :---: | :--- | :--- |
| `PORT` | No | `5000` | Port on which the HTTP server listens |
| `DATABASE_URL` | No | PostgreSQL connection string | Primary database URL (supports SQLite fallback) |
| `REDIS_URL` | No | `redis://localhost:6379/0` | Redis URL for PubSub and caching |
| `REDIS_CACHE_TTL` | No | `10` | Cache time-to-live in seconds for aggregate queries |

### Frontend (`data-pulse-frontend/.env.local`)

| Variable | Required | Default | Description |
| :--- | :---: | :--- | :--- |
| `NEXT_PUBLIC_BACKEND_URL` | No | `http://localhost:5000` | Base URL of the DataPulse backend API service |
| `NEXT_PUBLIC_GRAPHQL_ENDPOINT` | No | `http://localhost:5000/graphql` | Full URL to the backend GraphQL execution endpoint |
| `NEXT_PUBLIC_SSE_ENDPOINT` | No | `http://localhost:5000/events` | Full URL to the real-time Server-Sent Events stream |

---

## 📡 API Reference

### GraphQL Operations

#### Queries (`POST /graphql`)

| Query | Parameters | Description |
| :--- | :--- | :--- |
| `getMetrics` | `service`, `status`, `limit`, `offset` | Retrieve paginated telemetry records with optional filtering. |
| `getServiceSummaries` | None | Retrieve aggregate operational metrics and average latencies across all microservices. |
| `getMetricHistory` | `service`, `metricName`, `minutes` | Fetch time-series historical data points for a specific service metric. |
| `diagnoseAnomaly` | `service`, `metricName` | Execute automated AI root-cause analysis and return mitigation recommendations. |

#### Mutations (`POST /graphql`)

| Mutation | Input | Description |
| :--- | :--- | :--- |
| `ingestMetric` | `IngestMetricInput!` | Ingest a single telemetry metric point, evaluate SLA status, and broadcast to subscribers. |
| `batchIngestMetrics` | `[IngestMetricInput!]!` | High-throughput batch ingestion of multiple telemetry points. |

---

### REST & Streaming Endpoints

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/health` | System health check returning database and Redis operational statuses. |
| `GET` | `/events` | Continuous Server-Sent Events (SSE) telemetry event stream. |
| `GET` | `/graphql` | Interactive GraphiQL IDE for testing and querying schema operations. |
| `POST`| `/graphql` | Standard GraphQL execution endpoint. |

---

## 🔄 Working with Git Submodules

### Pulling the Latest Changes
To fetch latest changes for both the parent repo and all submodules:
```bash
git pull --recurse-submodules
git submodule update --remote --merge
```

### Making Changes in a Submodule
1. Enter the submodule directory:
   ```bash
   cd data-pulse-backend
   ```
2. Make your edits, commit, and push to origin:
   ```bash
   git add .
   git commit -m "feat: enhance latency telemetry precision"
   git push origin main
   ```
3. Return to the root directory to commit the updated submodule reference:
   ```bash
   cd ..
   git add data-pulse-backend
   git commit -m "chore: bump backend submodule reference"
   git push origin main
   ```

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
