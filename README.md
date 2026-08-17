# ZEB

> **ZEB** is a lightweight, educational Machine Identity Provider designed for secure machine-to-machine (M2M) authentication. Built from scratch using Node.js and Express, it provides verifiable digital identities for microservices and applications operating under a Zero Trust security architecture.

---

## 📌 Executive Summary

Modern application architectures rely heavily on distributed microservices that need to communicate securely. Historically, this interaction has relied on static credentials, such as hard-coded API keys or unexpiring shared secrets, creating major security risks.

**ZEB** eliminates static credentials by acting as a central, self-hosted Identity Provider (IdP). Built on the **OAuth 2.0 Client Credentials Grant** flow, ZEB issues short-lived, cryptographically signed JSON Web Tokens (JWTs) that serve as verifiable proof of identity and specific permissions across microservices.

---

## 🛠️ Key Capabilities (Version 1.0 MVP)

- **Machine Identity Management:** Automated issuance of unique `client_id` and salted `client_secret` pairs with active/disabled lifecycle management.
- **Token Issuance & Verification:** Decentralized signature validation, expiration checking, and OAuth 2.0-compliant token generation.
- **Granular Authorization:** Strict API-level permission enforcement using a flat, scope-based allow-list model.
- **Immutable Audit Logging:** Every authentication attempt, token issuance, and verification decision is recorded in an append-only database.
- **Zero Trust Posture:** Every cross-service call must present a valid, scoped token—no network-location trust assumed.

---

## 🧰 Tech Stack

- **Runtime & Framework:** Node.js, Express
- **Database & ORM:** PostgreSQL (Production) / SQLite (Local Dev) via Prisma ORM
- **Cryptography & Tokens:** `jsonwebtoken` (HS256 / RS256), `argon2` / `bcrypt` secret hashing
- **Testing:** Jest, Supertest
- **Containerization:** Docker, Docker Compose

---

## 📁 Repository Structure

```plaintext
zeb-identity-provider/
├── prisma/               # Data Layer (Database Schema & Migrations)
│   ├── schema.prisma     # Machine and AuditLog entities
│   └── migrations/
├── src/
│   ├── api/              # HTTP API Layer (Routes & Controllers)
│   │   ├── admin.js      # Admin endpoints (/machines, /admin/audit-logs)
│   │   ├── auth.js       # Authentication endpoint (/oauth/token)
│   │   └── verify.js     # Verification endpoint (/oauth/verify)
│   ├── services/         # Business Logic Layer
│   │   ├── machine.js    # Registration, listing, disabling, scope checks
│   │   ├── token.js      # JWT signing, signature validation, expiry checks
│   │   └── audit.js      # Cross-cutting Security Audit Logger
│   ├── middleware/
│   │   ├── adminAuth.js  # Static X-API-Key header enforcement
│   │   └── errorHandler.js # Standardized JSON error response handler
│   ├── utils/
│   │   └── crypto.js     # CSPRNG, argon2/bcrypt hashing, constant-time checks
│   ├── config/
│   │   └── env.js        # Environment variable validation
│   └── server.js         # Express initialization & app setup
├── tests/
│   ├── integration/      # Integration test suite covering TC-01 to TC-10
│   └── setup.js
├── demo-apps/            # Minimal reference microservice topology
│   ├── order-service/    # Machine Client requesting tokens
│   ├── inventory-service/# Protected Target (requires 'inventory:read')
│   └── payment-service/  # Protected Target (requires 'payment:create')
├── Dockerfile            # Container definition
├── docker-compose.yml    # Local multi-container orchestration
├── .env.example          # Environment template
└── package.json          # Dependencies and scripts

```

---

## 🚀 Quick Start Guide

### Prerequisites

- **Node.js:** `>= 18.0.0`
- **npm:** `>= 9.0.0`
- **Docker:** (Optional, for containerized local setups)

### Installation & Local Setup

1. **Clone the repository:**

```bash
git clone https://github.com/MrBereketK/ZEB.git
cd ZEB

```

2. **Install dependencies:**

```bash
npm install

```

3. **Configure Environment Variables:**
   Copy the example environment file and fill in your configuration:

```bash
cp .env.example .env

```

Ensure your `.env` contains:

```env
PORT=3000
DATABASE_URL="file:./dev.db"
ADMIN_API_KEY="super_secret_admin_key"
JWT_SIGNING_ALG="HS256"
JWT_SIGNING_KEY="your-256-bit-secret"
ACCESS_TOKEN_TTL_SECONDS=900
ZEB_ISSUER="https://zeb.internal"

```

4. **Run Database Migrations:**

```bash
npx prisma migrate dev

```

5. **Start the Development Server:**

```bash
npm run dev

```

---

## 📡 API Reference Overview

### Admin API

| Method  | Endpoint            | Description                             | Auth Required |
| ------- | ------------------- | --------------------------------------- | ------------- |
| `POST`  | `/machines`         | Register a new machine identity         | `X-API-Key`   |
| `GET`   | `/machines`         | List all registered machines            | `X-API-Key`   |
| `PATCH` | `/machines/:id`     | Disable a machine identity              | `X-API-Key`   |
| `GET`   | `/admin/audit-logs` | Query chronological security audit logs | `X-API-Key`   |

### Auth & Verification API

| Method | Endpoint        | Description                                         | Auth Required   |
| ------ | --------------- | --------------------------------------------------- | --------------- |
| `POST` | `/oauth/token`  | Obtain a short-lived JWT (Client Credentials Grant) | `client_secret` |
| `POST` | `/oauth/verify` | Introspect and verify token validity & scopes       | Bearer Token    |

---

## 🧪 Running Tests

The test suite includes full-loop integration testing for all core functionalities, verifying token issuance, scope enforcement, and audit trail assertions (TC-01 through TC-10).

```bash
# Run unit and integration test suite
npm test

# Run tests with coverage output
npm run test:coverage

```

---

## 👥 Contributors & Team

- **Bereket Kiros** ([@MrBereketK](https://github.com/)) — Software Engineering, Mekelle University
- **Dagmawit Tibebu** ([@dagm24](https://github.com/)) — Software Engineering, Addis Ababa Science & Technology University
- **Nejwa Abrar** ([@NejwaAbrar](https://github.com/)) — Computer Science, Hawassa University
- **Lemi Tadesse** ([@LemiTadesse](https://github.com/)) — Computer Science, Dilla University

---
