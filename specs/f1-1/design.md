# f1-1 — Repo frame: Design

## Context
Spec: `specs/f1-1/spec.md`. Repo has only an initial commit; `CLAUDE.md` does not exist yet (this feature creates it). Constraints: create files only; copy the folder list exactly; `.env` stays untracked. No code, no installs.

## Modules
| Module | Files |
|---|---|
| Toolchain check | none (read-only commands) |
| Project docs | `CLAUDE.md`, `SCOPE.md`, `PROGRESS.md`, `IDEAS.md`, `README.md` |
| Skeleton | `.gitkeep` in the 10 listed directories, nothing else |
| Ignore rules | `.gitignore` |
| Environment | `.env.example`, `.env` (byte copy) |

## Functions (units of work)
1. `checkToolchain` — run `node -v`, `python3.11 --version`, `docker --version`, `docker compose version`, `git --version`; Docker Desktop memory confirmed manually (Settings → Resources → Memory ≥ 6 GB).
2. `writeDocs`
   - `CLAUDE.md`: Locked stack (API / Worker / Infra / Testing and quality, as spec), Hard rules (4, as spec), Shell setup block copied verbatim in a ```sh fence.
   - `SCOPE.md`: In scope / Out of scope lists, verbatim from spec.
   - `PROGRESS.md`: all 27 features listed up front, all unchecked, one `- [ ]` checkbox each, in this order:
     - f1-1 Toolchain, repo skeleton, control files
     - f1-2 Infrastructure containers: Postgres, Redis, RabbitMQ
     - f1-3 Model run and RabbitMQ warm-up
     - f2-1 Express API skeleton
     - f2-2 Prisma schema and first migration
     - f2-3 Queue publisher and topology
     - f3-1 POST /jobs: write first, then publish
     - f3-2 GET /jobs/:id
     - f4-1 Worker model module
     - f4-2 Worker state transitions in SQL
     - f4-3 Consume loop with ack
     - f5-1 Dockerfiles for API and worker
     - f5-2 Compose services and smoke test
     - f6-1 Idempotent claim/complete and ack-after-commit audit
     - f6-2 Failure test hook, retries and dead-letter queue
     - f7-1 API keys and job ownership
     - f7-2 Redis rate limiter
     - f7-3 Queue-depth backpressure
     - f8-1 The sweeper
     - f8-2 Graceful shutdown
     - f8-3 The chaos test
     - f9-1 GET /health, lint/format config, CI workflow
     - f9-2 Provision the VM, production compose and Caddyfile
     - f9-3 Build, push, deploy with health gate and rollback
     - f10-1 One PyTorch thread per worker and dynamic batching
     - f10-2 report.js, k6 load script, benchmark matrix
     - f10-3 README, design decisions, demo video
   - `IDEAS.md`: heading + one-line "parking lot for stretch goals", empty list.
   - `README.md`: one paragraph describing the async sentiment-inference service (API → RabbitMQ → Python workers → Postgres/Redis).
3. `createSkeleton` — empty `.gitkeep` in exactly: `api/src/routes`, `api/src/middleware`, `api/src/queue`, `api/src/jobs`, `api/prisma`, `api/tests`, `worker/tests`, `scripts`, `deploy`, `.github/workflows`.
4. `writeGitignore` — lines: `node_modules/`, `.env` (unanchored → any folder), `__pycache__/`, `.venv/`.
5. `writeEnvExample` — exact block from spec.
6. `copyEnv` — `cp .env.example .env`.

## Order of operations
1. Toolchain check (stop and report on mismatch).
2. `.gitignore` (before `.env` exists, so `.env` is never untracked-visible).
3. `.env.example` → `.env`.
4. Docs.
5. `.gitkeep` files.
6. Run acceptance checks.

## Error handling
- Wrong/missing tool: run `which <tool>`, report the binary in use and the official install method (nvm for Node 24; python.org installer for 3.11; Docker Desktop for docker/compose; git-scm.com for git). Install nothing.
- Docker memory not verifiable from CLI: report as a manual check.
- Never `git add .env`; if `git status --short` shows `.env`, fix `.gitignore` before continuing.
- No overwrites: all target files are new; verify absence before writing.

## Acceptance criteria → how met
| Criterion | Met by |
|---|---|
| Toolchain versions + Docker ≥ 6 GB | Step 1 command output + manual Docker Desktop check |
| `find . -name .gitkeep -not -path './.git/*'` = exactly 10 paths | Step 5 creates only the listed 10 |
| `git check-ignore .env` prints `.env` | `.env` rule in `.gitignore` |
| `git status --short` omits `.env` | Same ignore rule; `.env` never staged |
| `.env.example` passwords `change-me`, values exact | Spec block copied verbatim; verify with `grep -i pass .env.example` and `diff .env.example .env` |
| No app code, nothing installed | Only docs, ignore, env and `.gitkeep` files created; `git status --short` reviewed |

## Verification
Run each acceptance command above and confirm output.
