# PostgreSQL is already distributed as an official, optimized image.
FROM postgres:15-alpine

# PostgreSQL stores its cluster in this path inside the persistent volume.
ENV PGDATA=/var/lib/postgresql/data/pgdata

# Document the database port; Compose controls whether it is published.
EXPOSE 5432