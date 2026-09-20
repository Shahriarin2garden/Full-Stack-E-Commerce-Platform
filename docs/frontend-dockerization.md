# Frontend Dockerization

The frontend is the Next.js application in `src/client`. It runs in a Node.js container and is published at `http://localhost:3000`.

## Prerequisites

- Docker Engine with Docker Compose v2.
- The repository cloned locally.
- Ports `3000` and `5000` available.

## Local Run Without Docker

```bash
cd src/client
npm ci
npm run dev
```

## Dockerfile Explanation

File: `src/client/Dockerfile`

```dockerfile
FROM node:22-alpine
```

Uses the official small Node.js 22 Alpine image.

```dockerfile
WORKDIR /app
```

Sets `/app` as the working directory inside the container.

```dockerfile
COPY package*.json ./
RUN npm ci
```

Copies dependency manifests first, then installs the exact lock-file versions. This also lets Docker cache dependencies when only source code changes.

```dockerfile
COPY . .
```

Copies the frontend source. `.dockerignore` excludes local dependencies, build output, and secrets.

```dockerfile
ARG NEXT_PUBLIC_API_URL_PROD=http://localhost:5000/api/v1
ARG NEXT_PUBLIC_SOCKET_URL=http://localhost:5000
```

Declares build-time public URLs. Next.js embeds these values into the browser bundle.

```dockerfile
ENV NEXT_PUBLIC_API_URL_PROD=$NEXT_PUBLIC_API_URL_PROD
ENV NEXT_PUBLIC_SOCKET_URL=$NEXT_PUBLIC_SOCKET_URL
ENV NODE_ENV=production
```

Makes the build arguments visible to Next.js and selects production mode.

```dockerfile
RUN sed -i '...' app/layout.tsx \
    && sed -i '...' app/layout.tsx \
    && npm run build
```

The two `sed` commands modify only the temporary copy inside the image. They replace the network-dependent Google font with a no-download fallback, then build Next.js. The repository source is unchanged.

```dockerfile
ENV PORT=3000
EXPOSE 3000
```

Sets and documents the internal Next.js port. Compose publishes it to the host.

```dockerfile
CMD ["npm", "start"]
```

Starts the compiled Next.js production server.

## Build Frontend Only

```bash
cd src/client
docker build --tag ecommerce-frontend .
docker run --rm --publish 3000:3000 \
  --env NEXT_PUBLIC_API_URL_PROD=http://localhost:5000/api/v1 \
  --env NEXT_PUBLIC_SOCKET_URL=http://localhost:5000 \
  ecommerce-frontend
```

Open `http://localhost:3000`.

## URL Rule

- Browser to API: `http://localhost:5000`.
- Container to container: Docker service names such as `database` and `redis`.

Never use `http://server:5000` as a `NEXT_PUBLIC_*` browser URL. The browser cannot resolve Docker service names.

## Verification

```bash
curl --fail http://localhost:3000/
```
