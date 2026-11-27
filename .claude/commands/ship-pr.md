---
description: Commit, push, merge the PR, return to main
argument-hint: <id> "<commit message>"
allowed-tools: Bash(git add:*), Bash(git commit:*), Bash(git push:*), Bash(git switch:*), Bash(git pull:*), Bash(git branch:*), mcp__github
---

`$0` is the feature id. The quoted text after it in `$ARGUMENTS` is the commit message.

1. `git add -A`
2. `git commit -m "<commit message>"`
3. `git push origin feature/$0`
4. With the GitHub MCP server, create a PR from `feature/$0` into `main` titled with the commit message, and merge it once its checks pass.
5. `git switch main` (git cannot delete the branch you are on).
6. `git pull origin main`
7. `git branch -d feature/$0`

Do not explain. Do not ask questions. Do only these steps, then stop.