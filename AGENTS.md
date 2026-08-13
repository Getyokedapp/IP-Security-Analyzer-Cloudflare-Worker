# IP Security Analyzer — Agent Guidelines

## Repo Rules

AI review outcomes:

- `NEUTRAL` is merge-allowed, including when providers are unavailable. A provider outage means the review is incomplete, so retry when practical; it does not block the merge by itself.
- A missing, skipped, or cancelled review is not a completed review. Do not treat it as `NEUTRAL` or as evidence that the PR was reviewed.

- Use a feature branch and pull request. Do not push directly to `main` or rewrite another contributor's branch. Using `--no-verify` is allowed only when the user explicitly requests it for that specific action.
- Force-push is allowed on non-default branches. Never force-push `main`.
- Keep `Worker.js` readable and do not add credentials, API tokens, or user IP data to the repository.
- Wait for repository CI and the internal `AI Code Review` check. Fix valid blocking findings, push a fix, and wait for that check again.
- Do not wait for CodeRabbit, Codex, or another external AI reviewer. If it has already reported feedback, assess it, fix valid defects, and resolve its posted threads. A pending, failed, or unavailable external review does not block merge.
- Human approval is not required. A human `CHANGES_REQUESTED` review still blocks merging. Never use an admin bypass.
- Enable GitHub auto-merge directly for every eligible non-draft PR. Use `gh pr merge --auto --squash --delete-branch`; never add or require an `auto-merge` label.
- Work is not complete until GitHub reports the PR as merged.
- Deploy only when the merged change must be made live in a running service.
  Documentation, agent instructions, workflow policy, tests, and other
  non-runtime changes do not need a deployment. Use staging only when changed
  runtime behavior needs verification or exposure. Never deploy to production
  without an explicit user request.
- Use the Cloudflare dashboard or Terraform-owned infrastructure only when the user explicitly asks for a deployment or configuration change.
