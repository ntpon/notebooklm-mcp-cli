---
status: active
last-reviewed: 2026-05-19
one-liner: "Vendored fork of jacob-bd/notebooklm-mcp-cli (NotebookLM CLI `nlm` + MCP server, ~35 tools). Installed on DGX, registered as Claude Code MCP server, auth: art.testinggame@gmail.com."
successor: null
predecessor: null
tech: "Python ≥3.11, Chromium + CDP for auth cookies, MCP server (stdio via fastmcp)"
upstream: "https://github.com/jacob-bd/notebooklm-mcp-cli"
fork: "https://github.com/ntpon/notebooklm-mcp-cli"
installed-version: "v0.6.10"
upstream-pull-policy: "manual-only — review CHANGELOG before each pull; no auto-update"
auth-account: "art.testinggame@gmail.com (per account-consolidation memory, 2026-05-15)"
chromium-binary: "~/.local/bin/chromium → Playwright chromium-1224 (NOT snap chromium)"
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

## Auth (DONE 2026-05-19)
- Account: `art.testinggame@gmail.com` (consolidated AI service account)
- Profile: `default` at `~/.notebooklm-mcp-cli/profiles/default/cookies.json`
- 42 cookies extracted, CSRF token captured, auto-refresh enabled
- Verified: `nlm login --check` ✓, `nlm notebook list` returns valid JSON

### How auth was completed (workaround for snap chromium)
The DGX has snap chromium at `/snap/bin/chromium`, which **does NOT work** with `nlm login`:
- Snap chromium architecture forces single-instance per user (SingletonLock conflict)
- Zygote startup fails with "Broken pipe" under nlm's launch args
- These are fundamental snap confinement issues, not fixable via flags

**Fix:** symlinked `~/.local/bin/chromium` → Playwright's bundled chromium
```bash
ln -sf /home/ntpon/.cache/ms-playwright/chromium-1224/chrome-linux/chrome ~/.local/bin/chromium
```
Because `~/.local/bin` precedes `/snap/bin` in PATH, `nlm` now picks the Playwright binary and launches cleanly. Playwright keeps chromium-1224 updated as part of its own update cycle — no extra maintenance.

### Login flow (for next time / different machine)
1. Need X session: DGX has GNOME on `:1`, accessible via x11vnc → SSH tunnel from MacBook (`ssh -L 5900:localhost:5900 ntpon@dgx`, then `open vnc://localhost:5900`).
2. From DGX shell with `DISPLAY=:1 XAUTHORITY=/run/user/1000/gdm/Xauthority`, run `nlm login` — Chromium spawns on display :1, user signs in via VNC view.
3. Cookies stable for ~1 week, auto-refresh via stored Chrome profile.

## Claude Code MCP integration
```
~/.claude.json → mcpServers.notebooklm-mcp = {type: stdio, command: "notebooklm-mcp"}
```
`claude mcp list` → `✓ Connected`. Tools (notebook_list, source_add, audio creation, etc.) become available in next Claude Code session.
