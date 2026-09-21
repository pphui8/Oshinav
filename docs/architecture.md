# Architecture

## 1. Overview

The application is a mobile-first activity tracking system for time-sensitive anime, game, VTuber, creator, shop, event, ticket, preorder, and merchandise activities.

The MVP uses a **modular monolith** architecture:

* Mobile client: Expo + React Native + TypeScript
* Backend: Go + Gin
* Database: PostgreSQL
* Cache / temporary state / job queue: Redis
* Authentication: JWT
* File storage: S3-compatible object storage
* Deployment: AWS EC2

No horizontal scaling, replication, service redundancy, or microservices are required for the MVP.

---

## 2. System Architecture

```text
┌──────────────────────────────┐
│       Mobile Client          │
│                              │
│ Expo / React Native / TS /   |
| TailWind                     │
└──────────────┬───────────────┘
               │ HTTPS / REST
               │ JWT
               ▼
┌─────────────────────────────────────────────┐
│                   EC2                       │
│                                             │
│  ┌───────────────────────────────────────┐  │
│  │          Go / Gin Application         │  │
│  │                                       │  │
│  │  Auth  Activity  Follow  Discovery   │  │
│  │  Import  Notification  User          │  │
│  │                                       │  │
│  └───────────────────┬───────────────────┘  │
│                      │                      │
│  ┌───────────────────▼───────────────────┐  │
│  │             Background Jobs           │  │
│  │                                       │  │
│  │ Extraction / Discovery / Notification │  │
│  └───────────────────┬───────────────────┘  │
│                      │                      │
└──────────────────────┼──────────────────────┘
                       │
          ┌────────────┼─────────────┐
          ▼            ▼             ▼
     PostgreSQL      Redis           S3
     Source of      Cache /        Uploaded
      Truth         Jobs /          Sources
                    Sessions
                       │
                       ▼
                External Sources
                / LLM / APIs
```

---

## 3. Backend Architecture

The backend is a **modular monolith** rather than multiple services.

```text
Go Application
│
├── API Layer
│
├── Domain Modules
│   ├── Auth
│   ├── Activity
│   ├── Follow
│   ├── Discovery
│   ├── Import
│   └── Notification
│
├── Background Workers
│   ├── Extraction
│   ├── Discovery
│   └── Notification
│
└── Infrastructure
    ├── PostgreSQL
    ├── Redis
    ├── Object Storage
    └── External Services
```

The primary request path is:

```text
HTTP Request
    ↓
Gin Handler
    ↓
Domain Service
    ↓
Repository
    ↓
PostgreSQL
```

Long-running or asynchronous work is handled by background workers rather than HTTP handlers.

---

## 4. Data Architecture

PostgreSQL is the **source of truth**.

Redis is not authoritative and is used for:

* Frequently accessed data
* Short-lived state
* JWT/session-related state
* Rate limiting
* Background job queues

Object storage is used for binary source data such as screenshots.

```text
                 ┌──────────────┐
                 │ PostgreSQL   │
                 │              │
                 │ Source Truth │
                 └──────────────┘
                        ▲
                        │
              ┌─────────┴─────────┐
              │                   │
        ┌─────┴─────┐       ┌─────┴─────┐
        │   Redis   │       │     S3    │
        │            │       │           │
        │ Cache/Jobs │       │  Files    │
        └────────────┘       └───────────┘
```

---

## 5. Authentication

The client authenticates with the backend using JWT.

```text
Mobile
  │
  │ JWT
  ▼
Go API
  │
  ├── PostgreSQL
  └── Redis
```

Authentication-specific behavior is defined separately in the authentication documentation.

---

## 6. Asynchronous Processing

The system uses Redis-backed background jobs for operations that should not block API requests.

```text
API
 │
 ├── create job
 │
 ▼
Redis
 │
 ▼
Worker
 │
 ├── Extraction
 ├── Discovery
 └── Notification
```

This provides an initial asynchronous architecture without introducing a separate message-broker service.

---

## 7. Deployment

The MVP uses a single EC2 environment.

```text
AWS EC2
│
├── Go API
├── Background Worker
├── PostgreSQL
└── Redis
```

Object storage is external to the EC2 instance.

The MVP intentionally does not include:

* Load balancers
* Multiple application instances
* Database replicas
* Redis clusters
* Multi-AZ deployment
* Kubernetes
* Microservices

The architecture can later move PostgreSQL to RDS, Redis to ElastiCache, and API/worker processes to separate compute resources without changing the core application architecture.

---

## 8. Architectural Principles

### Modular Monolith

Keep domain boundaries clear without introducing distributed services prematurely.

### PostgreSQL as Source of Truth

Redis improves performance and supports asynchronous processing but does not own persistent business state.

### Asynchronous by Default for Expensive Work

Extraction, discovery, external-source processing, and notification delivery should run outside the request-response path.

### Mobile-First API

The backend exposes a versioned REST API designed primarily for the Expo/React Native client.

### Replaceable Infrastructure

External dependencies such as LLM providers, object storage, notification providers, and job implementations should be isolated behind application-level interfaces where appropriate.

### Scale Only When Necessary

The MVP optimizes for development simplicity and clear separation of responsibilities rather than infrastructure redundancy.
