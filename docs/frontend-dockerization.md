# Frontend Dockerization

## Prerequisites

- Docker Engine and Docker Compose v2.
- The repository cloned locally.

## Local Run

```bash
cd src/client
npm ci
npm run dev
```

## Dockerization Steps

The frontend Dockerfile uses `node:22-alpine`, installs dependencies, builds Next.js once, and runs the production server with `npm start`. `NEXT_PUBLIC_API_URL_PROD` and `NEXT_PUBLIC_SOCKET_URL` are build arguments because Next.js embeds public environment values in the browser bundle.

Build and run only the frontend from its directory:

```bash
cd src/client
docker build --tag ecommerce-frontend .
docker run --rm --publish 3000:3000 \
  --env NEXT_PUBLIC_API_URL_PROD=http://localhost:5000/api/v1 \
  --env NEXT_PUBLIC_SOCKET_URL=http://localhost:5000 \
  ecommerce-frontend
```

## Verification

Open `http://localhost:3000`. For the complete application, use the combined setup instead of running this container alone.