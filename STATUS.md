---
status: active
last-reviewed: 2026-05-19
one-liner: "Vendored fork of jacob-bd/notebooklm-mcp-cli (NotebookLM CLI `nlm` + MCP server, ~35 tools). Installed on DGX, registered as Claude Code MCP server. Auth pending."
successor: null
predecessor: null
tech: "Python ≥3.11, Chromium + CDP for auth cookies, MCP server (stdio via fastmcp)"
upstream: "https://github.com/jacob-bd/notebooklm-mcp-cli"
fork: "https://github.com/ntpon/notebooklm-mcp-cli"
installed-version: "v0.6.10"
upstream-pull-policy: "manual-only — review CHANGELOG before each pull; no auto-update"
---

# Status notes

## Why we forked
- Upstream uses internal/undocumented NotebookLM APIs → real risk of Google C&D or maintainer abandonment. Fork = saved copy.
- Future integration target for `ai-factory` orchestration (NotebookLM as source library, atoms in local KB — pattern observed from YouTube "ลงทุน Diary" video `nnD1w2hlAdk`).

## Local conventions
- `origin` = `ntpon/notebooklm-mcp-cli` (our fork)
- `upstream` = `jacob-bd/notebooklm-mcp-cli` (fetch-only; push URL set to `DISABLED_PUSH_TO_UPSTREAM` to prevent accidental push)
- Only file diverging from upstream so far: this `STATUS.md`.

## Pulling upstream updates
```bash
git fetch upstream
git rebase upstream/main      # replays STATUS.md on top
# or
git merge upstream/main       # merge commit
```

## When to integrate / rename
- Hold until Soul Warden ships (2026-06-10). Then re-evaluate as part of ai-factory work.
- Possible future: rebrand commands `nlm` → `notebooklm`, fold into `apzilon-ai-studio` bundle, swap Playwright → BrowserOS adapter.

## Install (for reference)
```bash
# Installed via: cd ~/code/works/tools/apzilon-notebooklm-mcp-cli && uv tool install .
# Binaries: /home/ntpon/.local/bin/{nlm, notebooklm-mcp}
# MCP registered: ~/.claude.json → mcpServers.notebooklm-mcp (user scope)
# Verified: `claude mcp list` shows ✓ Connected
```

## Security audit (2026-05-19, v0.6.10)
- URLs hit: only google.com / notebooklm.google.com / pypi.org (version probe). No third-party endpoints.
- No telemetry libs (posthog/sentry/mixpanel/etc).
- No `exec`/`eval` of dynamic code. `subprocess.run` is only for invoking other CLIs (claude/codex/pbcopy).
- Auth: stored at `~/.notebooklm-mcp-cli/{auth.json, profiles/<name>/cookies.json}`, mode 0o700 (user-only).
- Deps: httpx, pydantic, typer, rich, fastmcp, websocket-client — all mainstream.

## Auth (NOT YET DONE)
DGX is headless (no DISPLAY). Three paths:
1. **SSH X11 from MacBook** — run `nlm login` on DGX, Chrome paints on MacBook
2. **Login on Mac/MacBook, copy `~/.notebooklm-mcp-cli/` to DGX** — simplest one-shot
3. **CDP URL pointing to Mac mini BrowserOS Chrome** via SSH tunnel: `nlm login --cdp-url http://127.0.0.1:18800`
