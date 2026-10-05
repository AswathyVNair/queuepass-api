# QueuePass | Distributed Event Ticketing & Seat Reservation API

QueuePass is a backend REST API designed for high-concurrency event ticketing. It prevents duplicate seat allocations during high-traffic sales drops using atomic database transactions and manages temporary seat holds with automated Redis expiration.

---

## Architecture & Problem Solved

During flash ticket sales, high traffic can trigger race conditions where two users attempt to purchase the same seat simultaneously.

QueuePass addresses this through:

- **Atomic Concurrency Control:** Wraps reservation checks and seat state updates inside a PostgreSQL transaction with row-level locking via Prisma ORM, preventing double-bookings.
- **Temporary Seat Holds (10-Minute TTL):** Places reserved seats on a temporary hold in Redis. If payment is not completed within 10 minutes, background workers release the seat back to the public pool automatically.
- **Cache-Aside Pattern:** Caches seat maps and event metadata in Redis to reduce direct queries against PostgreSQL during traffic surges.

---

## Tech Stack

- **Runtime & Framework:** Node.js, Express.js, TypeScript
- **Database & ORM:** PostgreSQL, Prisma ORM
- **In-Memory Store:** Redis (Temporary seat locks & caching)
- **Authentication:** JSON Web Tokens (JWT) & Role-Based Access Control (RBAC)
- **Containerization:** Docker, Docker Compose
- **Testing:** Jest, Supertest

---

## API Endpoints

### Authentication

| Method | Route                   | Description                            | Access |
| :----- | :---------------------- | :------------------------------------- | :----- |
| `POST` | `/api/v1/auth/register` | Register customer or organizer account | Public |
| `POST` | `/api/v1/auth/login`    | Authenticate user and issue JWT        | Public |

### Tickets & Reservation

| Method | Route                      | Description                                     | Access   |
| :----- | :------------------------- | :---------------------------------------------- | :------- |
| `GET`  | `/api/v1/events/:id/seats` | Retrieve venue seat map (Redis-cached)          | Public   |
| `POST` | `/api/v1/tickets/reserve`  | Lock a seat for 10 minutes (Atomic transaction) | Customer |
| `POST` | `/api/v1/tickets/confirm`  | Finalize purchase and mark seat as booked       | Customer |

---

## Getting Started Locally

### 1. Prerequisites

- Node.js (v18+)
- Docker & Docker Compose

### 2. Clone and Install Dependencies

```bash
git clone [https://github.com/AswathyVNair/queuepass-api.git](https://github.com/AswathyVNair/queuepass-api.git)
cd queuepass-api
npm install
```
