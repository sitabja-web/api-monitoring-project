# API Monitoring System

A full-stack monitoring platform for tracking API usage, performance, and health in real time. The project captures incoming API hits, validates requests through a secure backend, processes analytics asynchronously, and presents clean visual insights in a React dashboard.

## Overview

This repository contains three main parts:

- `server/` — Node.js + Express API server for authentication, client onboarding, ingesting hit data, and serving analytics
- `dashboard/` — React + Vite dashboard for visualizing metrics and monitoring system health
- `demo/` — sample implementation and architecture examples for testing and learning

The system is designed for teams that want to monitor endpoint usage, identify failures, detect high-latency calls, and understand request patterns across services.

## Key Features

- Secure authentication and authorization
- Client onboarding and API key management
- API hit ingestion with validation and rate limiting
- RabbitMQ-based async processing for event handling
- PostgreSQL and MongoDB integration for persistent storage
- Real-time analytics for requests, success/error ratios, and latency
- Dashboard with metrics cards, charts, and endpoint rankings
- Docker-based local setup for services and infrastructure

## Architecture

The backend is structured into service modules:

- `auth` — onboarding, login, profile, logout, and role-based access
- `client` — client registration and API key operations
- `ingest` — API hit collection and validation
- `analytics` — dashboard summary and stats endpoints
- `processor` — consumer for asynchronous processing of events
- `shared` — configuration, middleware, utilities, and common logic

The dashboard consumes the backend analytics endpoints and renders overview, performance, and status charts.

## Tech Stack

### Backend
- Node.js
- Express.js
- PostgreSQL
- MongoDB
- RabbitMQ
- JWT-based authentication
- Zod validation
- Winston logging

### Frontend
- React
- Vite
- TanStack Query
- ApexCharts / React Charts
- React Router
- SCSS modules

## Repository Structure

```text
API-Monitoring-System/
├── readme.md
├── dashboard/
│   ├── README.md
│   ├── package.json
│   ├── vite.config.js
│   └── src/
├── demo/
│   └── code_architecture/
├── server/
│   ├── docker-compose.yml
│   ├── Dockerfile
│   ├── Dockerfile.consumer
│   ├── package.json
│   ├── scripts/
│   └── src/
└── .gitignore
```

## Prerequisites

Before running the project locally, make sure you have:

- Node.js 18+ or compatible LTS version
- npm
- Docker and Docker Compose
- Access to localhost ports 5000, 5173, 5432, 27017, 5672, and 15672

## Getting Started

### 1) Start infrastructure services

From the `server` directory:

```bash
cd server
docker compose up -d postgres mongo rabbitmq pgadmin
```

This starts:

- PostgreSQL on `localhost:5432`
- MongoDB on `localhost:27017`
- RabbitMQ on `localhost:5672`
- RabbitMQ Management UI on `http://localhost:15672`
- pgAdmin on `http://localhost:8080`

### 2) Install server dependencies

```bash
cd server
npm install
```

### 3) Configure environment variables

Create a `.env` file in the `server` folder with values similar to:

```env
NODE_ENV=development
PORT=5000
MONGO_URI=mongodb://localhost:27017/api_monitoring
MONGO_DB_NAME=api_monitoring
PG_HOST=localhost
PG_PORT=5432
PG_DATABASE=api_monitoring
PG_USER=postgres
PG_PASSWORD=rahul
RABBITMQ_URL=amqp://localhost:5672
RABBITMQ_QUEUE=api_hits
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=24h
RATE_LIMIT_WINDOW_MS=60000
RATE_LIMIT_MAX_REQUESTS=100
```

### 4) Run the backend

```bash
cd server
npm run dev
```

The API should be available at:

- `http://localhost:5000/health`
- `http://localhost:5000/`

### 5) Install and run the dashboard

From the project root:

```bash
cd dashboard
npm install
npm run dev
```

Open the dashboard at:

```text
http://localhost:5173
```

## Initial Authentication Setup

The system includes a super-admin onboarding endpoint for first-time setup.

### Create the initial admin

```bash
curl -X POST http://localhost:5000/api/auth/onboard-super-admin \
  -H "Content-Type: application/json" \
  -d '{
    "username": "admin",
    "email": "admin@example.com",
    "password": "admin123"
  }'
```

Then log in using the same credentials in the dashboard.

## Main API Endpoints

### Authentication
- `POST /api/auth/onboard-super-admin`
- `POST /api/auth/register`
- `POST /api/auth/login`
- `GET /api/auth/profile`
- `GET /api/auth/logout`

### Ingesting API hits
- `POST /api/hit`

### Analytics
- `GET /api/analytics/stats`
- `GET /api/analytics/dashboard`

### Health
- `GET /health`

## Docker Deployment

To run the full project stack using Docker Compose:

```bash
cd server
docker compose up --build
```

This starts the application container, consumer worker, database services, and supporting monitoring tools.

## Production Notes

- Set strong JWT secrets via environment variables
- Use secure credentials for PostgreSQL, MongoDB, and RabbitMQ
- Restrict CORS and internal service exposure in production
- Configure reverse proxy / TLS before exposing the app publicly
- Use log rotation and monitoring for infrastructure health and API traffic

## License

This project is licensed under the MIT License.

## Contributing

Contributions are welcome. For changes, create a feature branch, make the required updates, and open a pull request with a clear description of the improvement.
