# Architecture

## 1. Overview

The application is a mobile-first system for shared, time-sensitive anime, game, VTuber, creator, shop, event, ticket, preorder, and merchandise activities. Shared activities can be contributed to by many users and monitored sources; each user has an independent tracking relationship with an activity.

The MVP uses a **modular monolith** architecture:

* Mobile client: Expo + React Native + TypeScript + NativeWind (Tailwind-style utilities)
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
│ Expo / React Native / TS /   │
│ NativeWind                   │
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
│  │  Auth  Activity  Matching  Follow    │  │
│  │  Discovery  Import  Notification    │  │
│  │  User                                │  │
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
     PostgreSQL      Redis           S3-compatible
     Source of      Cache /         object storage
      Truth         Job queue /     Uploaded sources
                    rate limits

Background workers ───────► External sources / LLM / APIs
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
│   ├── Matching
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

### Activity ownership and matching

`activities` is a shared domain resource. It must not be scoped to one user and it must not be deleted when one user stops tracking it. Personal state belongs in `user_activities`, including tracking state, current status, reminder preferences, and status history.

Both imports and discovery pass through the matching domain module before creating a shared activity. Matching should prefer stable identifiers and exact source URLs, then use normalized title, type, dates, venue, related subjects, and other extracted identifiers. The result is one of:

* associate the source with an existing activity and optionally apply a confirmed update;
* create a new shared activity and associate the source;
* request user confirmation when confidence is insufficient.

The matching operation must be idempotent and safe under concurrent imports. Database uniqueness constraints and transactions are the final protection against duplicate shared activities; Redis may improve throughput but is not the authority.

---

## 4. Data Architecture

PostgreSQL is the **source of truth**.

Redis is not authoritative and is used for:

* Frequently accessed data
* Short-lived state
* JWT revocation or refresh state, if enabled
* Rate limiting
* Background job queues

Object storage is used for binary source data such as screenshots.

The persistent ownership boundary is:

```text
Shared:   activities → milestones, sources, subjects, external references
Personal: users → user_activities, follows, notifications, settings, imports
Bridge:   user_activities(user_id, activity_id)
```

Activity fields are updated from trusted sources through the matching/update path. Personal status and notification settings are never stored on the shared activity row.

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

The client authenticates with the backend using JWT access tokens. JWTs carry the request identity; PostgreSQL remains the source of user and permission data. Redis may hold revocation, refresh, or rate-limit state, but it is not the system of record for authentication.

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
 ├── Matching / activity upsert
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

Object storage is external to the EC2 instance. External websites, APIs, and LLM providers are also outside EC2 and are called by the API or background workers as appropriate.

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
