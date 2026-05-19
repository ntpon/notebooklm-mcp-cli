---
status: experimental
last-reviewed: 2026-05-19
one-liner: "Vendored fork of jacob-bd/notebooklm-mcp-cli (NotebookLM CLI `nlm` + MCP server, ~35 tools). Saved copy for ai-factory orchestration. No local modifications yet."
successor: null
predecessor: null
tech: "Python ≥3.10, Playwright (browser automation against NotebookLM web UI), MCP server (stdio)"
upstream: "https://github.com/jacob-bd/notebooklm-mcp-cli"
fork: "https://github.com/ntpon/notebooklm-mcp-cli"
synced-at-tag: "v0.6.10"
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
uv tool install notebooklm-mcp-cli      # from PyPI (upstream)
# or, to use this fork:
cd ~/code/works/tools/apzilon-notebooklm-mcp-cli && uv tool install .
```
