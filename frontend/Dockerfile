# Stage 1: Build React App
FROM node:20-alpine AS build

WORKDIR /app

COPY package*.json ./
RUN npm install --no-audit

COPY . .
RUN npm run build

# Stage 2: Nginx non-root, port 8080
FROM nginxinc/nginx-unprivileged:alpine

USER root
RUN apk upgrade --no-cache
USER 101

COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 8080