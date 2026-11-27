# f1-1 — Repo frame

## Goal

Set up the repo frame: check the local toolchain, then add the project docs, directory skeleton, ignore rules and environment template. Install nothing and write no code.

## Scope

### Toolchain check (install nothing)

| Tool | Command | Expected |
|---|---|---|
| Node.js | `node -v` | `v24.x` |
| Python | `python3.11 --version` | `3.11.x` |
| Docker | `docker --version` | prints a version |
| Docker Compose | `docker compose version` | prints a version |
| Git | `git --version` | prints a version |
| Docker Desktop | Settings → Resources → Memory | 6 GB or more |

If a version is wrong, run `which <tool>` to see which binary is being used, then name the official install method (for example nvm for Node.js 24, python.org or the official installer for Python 3.11, Docker Desktop for docker and docker compose, git-scm.com for git).

### Project docs

- **CLAUDE.md**
  - Locked stack:
    - API: Node.js 24 (CommonJS), Express, Zod, Prisma, amqplib, ioredis, pino
    - Worker: Python 3.11, transformers, torch (CPU), pika, psycopg
    - Infra: RabbitMQ, PostgreSQL 16, Redis 7, Docker Compose, GitHub Actions, GHCR, AWS EC2, Caddy
    - Testing and quality: Jest + Supertest, pytest, ESLint + Prettier, Ruff, k6
  - Hard rules:
    - Prisma owns the schema.
    - Write the job before publishing.
    - Never ack before the DB commit.
    - Never use `requeue=True`.
  - Shell setup block (verbatim):
    ```sh
    API=http://localhost:3000; CT='Content-Type: application/json'; H='X-API-Key: demo-key-1'
    B='{"text":"Delivery was fast and the product works great"}'
    q() { docker compose exec -T postgres psql -U app -d inference -c "$1"; }
    post() { curl -s -H "$CT" -H "$H" -d "$B" "$API/jobs"; }
    hcode() { curl -s -o /dev/null -w '%{http_code}\n' -H "$CT" -H "$H" -d "$B" "$API/jobs"; }
    jid() { node -pe 'JSON.parse(require("fs").readFileSync(0)).jobId'; }
    ```
- **SCOPE.md**
  - In scope: the locked stack, 3 endpoints, API keys, DLQ, 2–3 workers, batching, one AWS VM with HTTPS.
  - Out of scope: Kubernetes, Kafka, GPUs, more models, autoscaling, user accounts, frontend, multi-region, TypeScript, extra services or endpoints.
- **PROGRESS.md**: one checkbox per feature.
- **IDEAS.md**: parking lot for stretch goals.
- **README.md**: one paragraph.

### Directory skeleton

Add an empty `.gitkeep` to each of these 10 directories:

1. `api/src/routes`
2. `api/src/middleware`
3. `api/src/queue`
4. `api/src/jobs`
5. `api/prisma`
6. `api/tests`
7. `worker/tests`
8. `scripts`
9. `deploy`
10. `.github/workflows`

### .gitignore

- `node_modules`
- `.env` in any folder
- `__pycache__`
- `.venv`

### .env.example, then copied to .env

Every password is `change-me`. Values:

```
DATABASE_URL=postgresql://app:change-me@localhost:5432/inference
RABBITMQ_URL=amqp://app:change-me@localhost:5672
REDIS_URL=redis://localhost:6379
API_KEYS=demo-key-1,bench-key
RATE_LIMIT_PER_MIN=60
MAX_QUEUE_DEPTH=5000
MAX_BATCH_SIZE=16
MAX_BATCH_WAIT_MS=50
MAX_ATTEMPTS=3
MODEL_NAME=distilbert/distilbert-base-uncased-finetuned-sst-2-english
TORCH_THREADS=1

# postgres:16
POSTGRES_USER=app
POSTGRES_PASSWORD=change-me
POSTGRES_DB=inference

# rabbitmq:3-management
RABBITMQ_DEFAULT_USER=app
RABBITMQ_DEFAULT_PASS=change-me
```

Copy `.env.example` to `.env`. `.env` stays untracked.

## Out of scope

- Installing any tool or dependency.
- Any application code, config code, Dockerfiles, compose files, Prisma schema or CI workflows.
- Everything listed as out of scope in SCOPE.md.

## Files

| Path | Action |
|---|---|
| `CLAUDE.md` | create |
| `SCOPE.md` | create |
| `PROGRESS.md` | create |
| `IDEAS.md` | create |
| `README.md` | create |
| `.gitignore` | create |
| `.env.example` | create |
| `.env` | create (copy of `.env.example`, not committed) |
| `api/src/routes/.gitkeep` | create |
| `api/src/middleware/.gitkeep` | create |
| `api/src/queue/.gitkeep` | create |
| `api/src/jobs/.gitkeep` | create |
| `api/prisma/.gitkeep` | create |
| `api/tests/.gitkeep` | create |
| `worker/tests/.gitkeep` | create |
| `scripts/.gitkeep` | create |
| `deploy/.gitkeep` | create |
| `.github/workflows/.gitkeep` | create |

## Acceptance criteria

- [ ] Toolchain check: `node -v` prints `v24.x`, `python3.11 --version` prints `3.11.x`, and `docker --version`, `docker compose version` and `git --version` each print a version. Docker Desktop has 6 GB or more of memory.
- [ ] `find . -name .gitkeep -not -path './.git/*'` lists exactly the 10 paths above.
- [ ] `git check-ignore .env` prints `.env`.
- [ ] `git status --short` does not list `.env`.
- [ ] Every password in `.env.example` is `change-me`, and every variable in it has the value listed above.
- [ ] No application code is added and nothing is installed.
