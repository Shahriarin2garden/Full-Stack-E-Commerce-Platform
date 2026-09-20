# Server Dockerization

## Prerequisites

- Docker Engine and Docker Compose v2.
- A reachable PostgreSQL database and Redis instance for standalone execution.

## Local Run

```bash
cd src/server
npm ci
npx prisma generate
npm run build
npm start
```

## Dockerization Steps

The server Dockerfile installs dependencies, generates the Prisma client, compiles TypeScript, and starts the compiled output. The runtime image does not include source files or build output beyond `dist` and Prisma runtime files.

Build and run only the server when dependency URLs are available:

```bash
cd src/server
docker build --tag ecommerce-server .
docker run --rm --publish 5000:5000 \
  --env NODE_ENV=production \
  --env PORT=5000 \
  --env DATABASE_URL=postgresql://ecommerce:ecommerce@host.docker.internal:5432/b2c_ecommerce \
  --env REDIS_URL=redis://host.docker.internal:6379 \
  --env ACCESS_TOKEN_SECRET=exam-access-secret \
  --env REFRESH_TOKEN_SECRET=exam-refresh-secret \
  --env SESSION_SECRET=exam-session-secret \
  --env COOKIE_SECRET=exam-cookie-secret \
  ecommerce-server
```

## Verification

Open `http://localhost:5000/live`. The combined Compose setup runs `prisma migrate deploy` before starting the server.