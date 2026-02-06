# Docker Reference

## Dockerfile - Node.js (Multi-Stage)

```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

FROM node:20-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
RUN addgroup -g 1001 -S appgroup && adduser -S appuser -u 1001 -G appgroup
COPY --from=builder --chown=appuser:appgroup /app/dist ./dist
COPY --from=builder --chown=appuser:appgroup /app/node_modules ./node_modules
COPY --from=builder --chown=appuser:appgroup /app/package.json ./
USER appuser
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/server.js"]
```

## Dockerfile - Python

```dockerfile
FROM python:3.12-slim
WORKDIR /app
ENV PYTHONDONTWRITEBYTECODE=1 PYTHONUNBUFFERED=1
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
RUN adduser --disabled-password appuser
USER appuser
EXPOSE 8000
CMD ["gunicorn", "app:app", "--bind", "0.0.0.0:8000", "--workers", "4"]
```

## Dockerfile - Go

```dockerfile
FROM golang:1.22-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o /server ./cmd/server

FROM scratch
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=builder /server /server
EXPOSE 8080
ENTRYPOINT ["/server"]
```

## Docker Compose - Development

```yaml
services:
  app:
    build: { context: ., dockerfile: Dockerfile.dev }
    ports: ["3000:3000"]
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@db:5432/myapp
      - REDIS_URL=redis://redis:6379
    depends_on:
      db: { condition: service_healthy }

  db:
    image: postgres:16-alpine
    ports: ["5432:5432"]
    environment: { POSTGRES_DB: myapp, POSTGRES_USER: postgres, POSTGRES_PASSWORD: postgres }
    volumes: [pgdata:/var/lib/postgresql/data]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 3s
      retries: 5

  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]

volumes:
  pgdata:
```

## Networks, Env Vars, .dockerignore

```yaml
# Networks: isolate frontend from DB
services:
  frontend: { networks: [public] }
  api: { networks: [public, private] }
  db: { networks: [private] }

# Env vars: inline or from file
services:
  app:
    environment: [NODE_ENV=production, "API_KEY=${API_KEY}"]
    env_file: [.env.production]
```

`.dockerignore`: `node_modules`, `.git`, `.env*`, `dist`, `Dockerfile*`, `docker-compose*`

## Common Commands

```bash
docker compose up -d --build       # Build and start
docker compose logs -f app         # Follow logs
docker compose exec app sh         # Shell into container
docker compose down -v             # Stop + remove volumes
```

## Production Checklist

- Use specific image tags (`node:20.11-alpine`), never `latest`
- Run as non-root user (`USER appuser`)
- Multi-stage builds to minimize image size
- `.dockerignore` to exclude dev files
- `HEALTHCHECK` for orchestrator integration
- `--no-cache-dir` (pip) and `npm ci` (node) for reproducible installs
- Set resource limits: `deploy.resources.limits.memory: 512M`
- Log rotation: `logging.options.max-size: "10m"`
- Scan images: `docker scout cves myimage:latest`
