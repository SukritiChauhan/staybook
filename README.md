# StayBook

A hotel booking backend that stays correct under heavy traffic. Built with Java, Spring Boot, PostgreSQL and Redis.

> **Status:** In progress (started Nov 2026). Sections marked *(planned)* will be filled in as I build. I'm updating this README as each part ships.

## Why I built this

Most booking demos are simple CRUD apps that break when two users book the same room at the same time. I wanted to build the part that is actually hard: **correctness under concurrency**, safe retries, and fast search, and to explain every design choice along the way.

## Features

- Search hotels by city, dates and price *(planned)*
- Check room availability for a date range *(planned)*
- Book and cancel a room *(planned)*
- **No double-booking**, even with many simultaneous requests *(planned)*
- **Idempotent booking requests**, so retries never create duplicates *(planned)*
- Cached search with rate limiting *(planned)*

## Tech stack

| Layer | Choice | Why |
|---|---|---|
| Language / framework | Java 25, Spring Boot | Widely used for backend services, strong support for transactions |
| Database | PostgreSQL | Reliable transactions, row-level locking, constraints |
| Cache | Redis | Fast reads for search, rate limiting |
| Build | Maven | *(to confirm)* |
| Testing | JUnit, Testcontainers | *(planned)* |
| Packaging | Docker | *(planned)* |

## Architecture *(planned)*

Add a diagram here: Client → REST API (Spring Boot) → PostgreSQL, with Redis for search caching and rate limiting.

**Key entities:** User, Hotel, Room, Booking (check-in and check-out dates).

## Key design decisions

*(I'll write these in my own words as I implement each one.)*

1. **Preventing double-booking:** which approach I chose (row locking, optimistic locking, or a database constraint) and why.
2. **Idempotency:** how an `Idempotency-Key` header prevents duplicate bookings on retries.
3. **Caching strategy:** why search can be cached while booking always checks the database.
4. **Trade-offs I rejected:** and what would change at larger scale.

## API overview *(planned)*

| Method | Endpoint | Description |
|---|---|---|
| GET | `/hotels?city=&checkIn=&checkOut=` | Search available hotels |
| GET | `/hotels/{id}/rooms` | List rooms and availability |
| POST | `/bookings` | Create a booking (requires `Idempotency-Key`) |
| DELETE | `/bookings/{id}` | Cancel a booking |

## Getting started *(planned)*

```bash
git clone https://github.com/<your-username>/staybook.git
cd staybook
docker compose up -d      # PostgreSQL + Redis
./mvnw spring-boot:run
```

Requirements: Java 21+ and Docker.

## Testing and performance *(planned)*

- Concurrency test: N simultaneous booking requests for one room, exactly 1 succeeds.
- Load test results (add real numbers after measuring, e.g. requests per second and p95 latency).

## Roadmap

- [ ] Project setup and data model
- [ ] Search and availability endpoints
- [ ] Booking with transaction safety
- [ ] Idempotency keys
- [ ] Redis caching and rate limiting
- [ ] Tests, Docker and CI
- [ ] Load testing and write-up

## What I'm learning

Concurrency control, database transactions and locking, idempotent API design, caching trade-offs, and writing tests that prove correctness.

## Author

**Sukriti Chauhan**, B.Tech CSE (AI & ML), MAIT, Delhi
[LinkedIn](#) · [GitHub](#)
