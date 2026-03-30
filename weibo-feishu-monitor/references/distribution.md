# Distribution Guide

Use this guide when packaging the skill for other people.

## What To Share

Share the entire `weibo-feishu-monitor/` folder:

- `SKILL.md`
- `agents/openai.yaml`
- `references/commands.md`
- `references/distribution.md`

Do not bundle personal runtime files, cookies, tokens, or local logs.

## Recipient Setup

The recipient needs three things outside the skill itself:

1. A codebase that contains the actual monitor implementation
2. A live deployment directory used by the scheduler
3. Local secrets and browser login state for Weibo and Feishu

## Minimum Project Layout

This skill assumes the recipient has or will create something equivalent to:

```text
PROJECT_ROOT/
  lanxi_weibo_monitor.py
  tests/test_lanxi_weibo_monitor.py
  scripts/test_weibo_monitor_delivery.sh

LIVE_ROOT/
  lanxi_weibo_monitor.py
  .weibo_profile/
  .env.feishu
  .env.feishu.openapi
  runtime/
  logs/
```

The actual folder names can differ. The skill uses variables instead of hardcoded paths.

## Machine-Specific Inputs

Before using the skill, the recipient must decide:

- `PROJECT_ROOT`
- `LIVE_ROOT`
- `PROFILE_DIR`
- `OPENAPI_ENV_FILE`
- `WEBHOOK_ENV_FILE`
- `RECEIVE_ID_TYPE`
- `RECEIVE_ID`
- scheduler service name, if using launchd

## What Must Be Configured

- A logged-in Weibo browser profile for browser fallback
- Feishu OpenAPI credentials
- Optional Feishu webhook fallback
- A target Feishu chat/user ID

## Sharing Options

### Option 1: Folder share

Ask the recipient to copy the folder into:

```bash
~/.codex/skills/weibo-feishu-monitor
```

### Option 2: Git repo

Put the skill folder in a public or private repository and ask recipients to install or copy it into their Codex skills directory.

## What This Skill Does Not Ship

This skill does not include:

- production Python source code for the monitor
- Playwright or browser dependencies
- Weibo cookies or login state
- Feishu app credentials
- launchd plist files

Those remain deployment-specific and should be documented or provisioned separately by the recipient.
