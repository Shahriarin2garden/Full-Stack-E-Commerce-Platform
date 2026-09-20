# Server Dockerization

The server is the Express, TypeScript, Prisma, GraphQL, and Socket.IO application in `src/server`. Compose publishes it at `http://localhost:5000`.

## Prerequisites

- Docker Engine with Docker Compose v2.
- PostgreSQL and Redis, local or running through Compose.

## Local Run Without Docker

```bash
cd src/server
npm ci
npx prisma generate
npm run build
npm start
```

## Dockerfile Explanation

File: `src/server/Dockerfile`

```dockerfile
FROM node:22-alpine
```

Uses the official lightweight Node.js image.

```dockerfile
WORKDIR /app
```

Sets `/app` as the container working directory.

```dockerfile
COPY package*.json ./
RUN npm ci
```

Copies dependency manifests and installs reproducible locked dependencies.

```dockerfile
COPY prisma ./prisma
RUN npx prisma generate
```

Copies Prisma schema and migrations, then generates the Prisma client.

```dockerfile
COPY . .
RUN npm run build
```

Copies server source and compiles TypeScript into `dist`.

```dockerfile
ENV NODE_ENV=production
ENV PORT=5000
EXPOSE 5000
```

Configures production mode, the internal HTTP port, and the documented container port.

```dockerfile
CMD ["npm", "start"]
```

Runs the compiled server through the package `start` script.

## Compose Database and Redis URLs

Inside Compose, use service names as hostnames:

```text
DATABASE_URL=postgresql://ecommerce:ecommerce@database:5432/b2c_ecommerce
REDIS_URL=redis://redis:6379
```

Compose supplies DNS records for `database` and `redis` on its private network.

## Migrations and Seed Data

The combined Compose command is:

```bash
npx prisma migrate deploy && npm start
```

Migrations create tables. They do not insert products. Load the sample application data separately:

```bash
cd docker/combined
docker compose exec server npm run seed
```

The seed script creates users, categories, products, variants, and attributes. It clears existing application records first, so do not run it on production data.

## Verification

```bash
curl --fail http://localhost:5000/live
```

Check real product data:

```bash
curl -X POST http://localhost:5000/api/v1/graphql \
  -H "content-type: application/json" \
  --data '{"query":"{ products(first: 3) { totalCount products { name slug } } }"}'
```
