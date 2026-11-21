# ---- Build stage ----
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install --omit=dev --no-audit

# ---- Production stage ----
FROM node:20-alpine

RUN apk upgrade --no-cache \
 && apk add --no-cache dumb-init \
 && rm -rf /usr/local/lib/node_modules/npm /usr/local/lib/node_modules/corepack \
           /usr/local/bin/npm /usr/local/bin/npx /usr/local/bin/corepack \
           /usr/local/bin/yarn /usr/local/bin/yarnpkg /opt/yarn-*

ENV NODE_ENV=production

WORKDIR /app

RUN addgroup -g 1001 -S appgroup && \
    adduser -S appuser -u 1001 -G appgroup

COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./
COPY src ./src
COPY seed.js ./

RUN mkdir -p uploads && chown -R appuser:appgroup /app

USER appuser

EXPOSE 3002

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:3002/health || exit 1

ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "src/server.js"]