# Database Dockerization

The database is PostgreSQL 15. It is private to the Compose network and persists data in a named Docker volume.

## Prerequisites

- Docker Engine with Docker Compose v2.
- A free Docker volume location.
- Host port `5432` is not required because the database is not published.

## Dockerfile Explanation

File: `src/database/Dockerfile`

```dockerfile
FROM postgres:15-alpine
```

Uses the official PostgreSQL 15 Alpine image. PostgreSQL does not need to be installed manually.

```dockerfile
ENV PGDATA=/var/lib/postgresql/data/pgdata
```

Places the PostgreSQL cluster under the directory mounted by the persistent volume.

```dockerfile
EXPOSE 5432
```

Documents PostgreSQL's internal port. Compose does not publish it because only the server needs access.

## Initialization Variables

Compose passes these values to the official image:

```text
POSTGRES_USER=ecommerce
POSTGRES_PASSWORD=change-me
POSTGRES_DB=b2c_ecommerce
```

PostgreSQL reads them only when the volume is first initialized. Changing them later does not change an existing volume.

## Persistence

```yaml
volumes:
  - postgres_data:/var/lib/postgresql/data
```

The named volume survives container removal and normal `docker compose down`.

Delete all local database data with:

```bash
cd docker/combined
docker compose down -v
```

## Migrations and Seed Data

```bash
cd docker/combined
docker compose up -d
docker compose exec server npm run seed
```

Migrations create the schema. Seeding inserts the sample users, categories, products, variants, and attributes.

## Verification

```bash
docker compose exec database pg_isready -U ecommerce -d b2c_ecommerce
```

Expected output contains `accepting connections`.
