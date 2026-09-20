# Database Dockerization

## Prerequisites

- Docker Engine and Docker Compose v2.
- A database password for the local exam environment.

## Local Run

Without Docker, start PostgreSQL 15 and create a database matching the server `DATABASE_URL`.

## Dockerization Steps

The database Dockerfile extends the official `postgres:15-alpine` image. Compose supplies `POSTGRES_USER`, `POSTGRES_PASSWORD`, and `POSTGRES_DB`, and the named `postgres_data` volume preserves data between restarts.

The database is started as part of the combined setup:

```bash
cd docker/combined
cp .env.example .env
docker compose up --build
```

## Verification

```bash
cd docker/combined
docker compose ps
docker compose exec database pg_isready -U ecommerce -d b2c_ecommerce
```

To reset the exam database completely, remove the volume:

```bash
docker compose down --volumes
```