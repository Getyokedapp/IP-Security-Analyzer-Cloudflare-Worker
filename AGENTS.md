# IP Security Analyzer — Agent Guidelines

<!-- yoked:shared:commit-signing:begin -->
## Git Commit Signing

- Sign every Git commit with a GitHub-verified key for the committer email. Keep signing enabled; if signing fails, fix the signing setup instead of creating an unsigned commit.
<!-- yoked:shared:commit-signing:end -->

## Repo Rules

<!-- yoked:shared:repo-rules:begin -->
AI review outcomes:

- `NEUTRAL` is merge-allowed, including when providers are unavailable. A provider outage means the review is incomplete, so retry when practical; it does not block the merge by itself.
- A missing, skipped, or cancelled review is not a completed review. Do not treat it as `NEUTRAL` or as evidence that the PR was reviewed.

- All changes through feature branch → PR. Never push directly to `main`.
- Bypassing git hooks (`--no-verify`, `LEFTHOOK=0`, `LEFTHOOK_EXCLUDE`) is allowed only when the user explicitly requests it for that specific action. Never `git reset --hard`. Force-push is allowed on any non-default branch.
- Never push code you know will break CI. Run this repo's local checks (see Commands) before pushing; if a pre-existing failure would break CI, fix it or abort the push.
- PRs need matching Linear issue (standard exceptions: AGENTS.md updates, docs, typo/config fixes). A repo addendum below may exempt this repo entirely.
- PR title: `<LINEAR-ID>: <short imperative description>` (for multiple issues, use `<LINEAR-ID>/<LINEAR-ID>: <short description>`). Do not use Conventional Commit prefixes in PR titles. For documentation/config exceptions without a Linear issue, use `<artifact>: <short imperative description>`.
- PR body: `Linear`, `What changed`, `Why`, `Validation`, `Notes`. Default base is `main`.
- Wait for repository CI and the internal `AI Code Review` check. Fix valid blocking findings, push a fix, and wait for that check again.
- Do not wait for CodeRabbit, Codex, or another external AI reviewer. If it has already reported feedback, assess it, fix valid defects, and resolve its posted threads. A pending, failed, or unavailable external review does not block merge.
- Human approval is not required. A human `CHANGES_REQUESTED` review still blocks merging. Never use an admin bypass.
- A `CHANGES_REQUESTED` review demands author action, not waiting: address actionable findings immediately; when a human decision is required, park the PR as draft, record the blocker and the exact question in the PR, and surface it to the user in the final message. Never leave a change-requested PR ready and idle.
- Judge review findings on evidence. Fix valid issues. For an incorrect, irrelevant, intentional, or disproportionate finding, reply with a short technical reason and an explicit signal such as `false positive`, `not applicable`, `by design`, or `won't fix`; add 👎 when appropriate. Reply `fixed` or `addressed`, or add 👍, for accepted findings. Give feedback before resolving the thread so PR Reviewer can learn; never resolve or dismiss a finding silently.
- Open PRs as **draft** by default. Draft means not merge-ready: expensive CI and auto-merge stay off.
- Before marking ready: run local checks for this repo, then comment `/review` on the draft and address valid findings. Manual `/review` on drafts is supported even though auto-review skips drafts.
- Mark **Ready for review** only when the author intends CI + auto-merge.
- Author owns merge. Do not merge, force-push, or rewrite another author's PR unless they ask.
- A PR that changes an agent-instruction surface — `AGENTS.md`, `CLAUDE.md`, `skills/**/SKILL.md`, `.agents/**` (excluding `.agents/state/`), or any file sessions execute as authority — needs explicit user approval for that change, cited in the PR body; without it, do not merge or arm auto-merge. A merged instruction is executed by every future session, so a slipped-in instruction compromises all of them.
- Never merge with failing or pending required checks. Never admin-merge. Merge only when all required checks are green and complete.
- After the author marks the PR ready (non-draft), enable GitHub auto-merge with `gh pr merge --auto --squash --delete-branch`; never add or require an `auto-merge` label.
- An auto-merge-armed PR that has not landed within 24 hours is stuck, not queued: find out why (failing or pending required check, dismissed review, merge conflict) and fix the cause instead of re-polling.
- Linear: In Progress → In Review → Done, with Done only after GitHub reports the PR as merged.
- Work is not complete until GitHub reports the PR as merged.
- Do not end a session with one of your own PRs unresolved: armed-but-unmerged, ready with failing or pending checks, open review findings, or a change request without a recorded decision. Fix and push, park as draft, or record the exact resume state (PR number, blocker, next command) in `.agents/state/<topic>/current.md` and say so explicitly in the final message. (Incidents: react-native-app #2846 left draft-red, api-backend #1539 armed three days unmerged.)
- At session start in a repo, sweep for resumable work before starting anything new: your open PRs (`gh pr list --author @me --state open`) and any `.agents/state/*/current.md` notes. Adopt and finish, or report what you found — never silently ignore them.
- Before ending a session, advance, close, or explicitly report every draft PR you own. A red, idle, or superseded draft is unfinished work, not a safe resting state.
- Deploy only when the merged change must be made live in a running service. Documentation, agent instructions, workflow policy, tests, and other non-runtime changes do not need a deployment. Use staging only when changed runtime behavior needs verification or exposure. Never deploy to production without an explicit user request. A repo addendum may grant standing deploy authorization.
- Fix a pre-existing failure only when it is small and blocks validation of your change. Otherwise report it.
- Before chasing a red check you did not cause, check whether the base tip is already red (the latest runs on `origin/main`). An inherited failure is not your branch's defect: do not fix it inside the feature branch — report it and tag the PR body `⚠️ base-red inherited`.
- Documentation must describe verified behavior: confirm a symbol, command, path, or env var exists in source before documenting it, measure counts instead of recalling them, and prefer a source link (`path/to/file.ts:line`) over an invented signature.
- Subagents do not inherit this file. Every subagent prompt that touches git must restate the rules it must not break — at minimum never push to `main`, never bypass hooks, and this repo's worktree/checkout ownership rules.
- Merge: squash feature/fix/chore, preserve history for integration merges (for example staging→main). After merge: delete branch, switch to `main`. Worktrees under `.worktrees/`.
<!-- yoked:shared:repo-rules:end -->

- Keep `Worker.js` readable and do not add credentials, API tokens, or user IP data to the repository.
- Use the Cloudflare dashboard or Terraform-owned infrastructure only when the user explicitly asks for a deployment or configuration change. This restriction overrides the shared deploy rule above.

## Stack

Single-file Cloudflare Worker (`Worker.js`), no build system, no dependencies, no test suite. `README.md` is the behavioral specification.

## Commands

```bash
node --check Worker.js   # syntax check — the only local gate
```

Deployment is manual through the Cloudflare dashboard on explicit user request only.
