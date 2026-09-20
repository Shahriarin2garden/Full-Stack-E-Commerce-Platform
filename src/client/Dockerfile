# Small official Node image.
FROM node:22-alpine
WORKDIR /app

# Install dependencies before copying source for Docker layer caching.
COPY package*.json ./
RUN npm ci
COPY . .

# These public URLs are embedded into the browser bundle.
ARG NEXT_PUBLIC_API_URL_PROD=http://localhost:5000/api/v1
ARG NEXT_PUBLIC_SOCKET_URL=http://localhost:5000
ENV NEXT_PUBLIC_API_URL_PROD=$NEXT_PUBLIC_API_URL_PROD
ENV NEXT_PUBLIC_SOCKET_URL=$NEXT_PUBLIC_SOCKET_URL
ENV NODE_ENV=production

# Avoid downloading Google Fonts during a disconnected exam build.
RUN sed -i 's/import { Poppins } from "next\/font\/google";/const Poppins = () => ({ variable: "--font-poppins", className: "" });/' app/layout.tsx \
	&& sed -i '/const poppins = Poppins({/,/});/c\const poppins = Poppins();' app/layout.tsx \
	&& npm run build
ENV PORT=3000
EXPOSE 3000
CMD ["npm", "start"]