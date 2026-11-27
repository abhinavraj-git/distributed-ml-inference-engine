# CLAUDE.md

## Locked stack

- **API:** Node.js 24 (CommonJS), Express, Zod, Prisma, amqplib, ioredis, pino
- **Worker:** Python 3.11, transformers, torch (CPU), pika, psycopg
- **Infra:** RabbitMQ, PostgreSQL 16, Redis 7, Docker Compose, GitHub Actions, GHCR, AWS EC2, Caddy
- **Testing and quality:** Jest + Supertest, pytest, ESLint + Prettier, Ruff, k6

## Hard rules

1. Prisma owns the schema.
2. Write the job before publishing.
3. Never ack before the DB commit.
4. Never use `requeue=True`.

## Shell setup

```sh
API=http://localhost:3000; CT='Content-Type: application/json'; H='X-API-Key: demo-key-1'
B='{"text":"Delivery was fast and the product works great"}'
q() { docker compose exec -T postgres psql -U app -d inference -c "$1"; }
post() { curl -s -H "$CT" -H "$H" -d "$B" "$API/jobs"; }
hcode() { curl -s -o /dev/null -w '%{http_code}\n' -H "$CT" -H "$H" -d "$B" "$API/jobs"; }
jid() { node -pe 'JSON.parse(require("fs").readFileSync(0)).jobId'; }
```
