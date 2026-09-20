# Small official Node image.
FROM node:22-alpine
WORKDIR /app

# Install dependencies before copying source for Docker layer caching.
COPY package*.json ./
RUN npm ci

COPY prisma ./prisma
RUN npx prisma generate
COPY . .
RUN npm run build
ENV NODE_ENV=production
ENV PORT=5000
EXPOSE 5000
CMD ["npm", "start"]
