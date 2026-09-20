# Compose selects one of these two targets.

FROM node:22-alpine AS frontend
WORKDIR /app
COPY src/client/package*.json ./
RUN npm ci
COPY src/client ./

ARG NEXT_PUBLIC_API_URL_PROD=http://localhost:5000/api/v1
ARG NEXT_PUBLIC_SOCKET_URL=http://localhost:5000
ENV NEXT_PUBLIC_API_URL_PROD=$NEXT_PUBLIC_API_URL_PROD
ENV NEXT_PUBLIC_SOCKET_URL=$NEXT_PUBLIC_SOCKET_URL
ENV NODE_ENV=production

# Keep the build independent of Google Fonts network access.
RUN sed -i 's/import { Poppins } from "next\/font\/google";/const Poppins = () => ({ variable: "--font-poppins", className: "" });/' app/layout.tsx \
	&& sed -i '/const poppins = Poppins({/,/});/c\const poppins = Poppins();' app/layout.tsx \
	&& npm run build
EXPOSE 3000
CMD ["npm", "start"]

FROM node:22-alpine AS server
WORKDIR /app
COPY src/server/package*.json ./
RUN npm ci
COPY src/server/prisma ./prisma
RUN npx prisma generate
COPY src/server ./
RUN npm run build
ENV NODE_ENV=production
ENV PORT=5000
EXPOSE 5000
CMD ["npm", "start"]