# weibo-feishu-monitor-skill

Shareable Codex skill for operating a Weibo-to-Feishu monitoring workflow.

This repository contains a reusable skill package:

- `weibo-feishu-monitor/`

The skill helps Codex:

- monitor Weibo accounts
- send polished single-image Weibo cards to Feishu
- validate specific posts or historical retweets
- troubleshoot launchd runs, logs, dedupe state, and delivery failures
- diagnose browser-fallback misses caused by older pinned or historical cards appearing before newer posts

## What The Skill Assumes

This repository ships the skill only. It does not include:

- production monitor source code
- Weibo browser login state
- Feishu credentials
- launchd plist files

The recipient must map the skill onto their own machine and deployment paths.

## Install

Copy the skill folder into your Codex skills directory:

```bash
mkdir -p ~/.codex/skills
cp -R weibo-feishu-monitor ~/.codex/skills/
```

After that, reopen Codex or start a new session.

## Read Next

- [INSTALL.md](INSTALL.md)
- [weibo-feishu-monitor/SKILL.md](weibo-feishu-monitor/SKILL.md)
- [weibo-feishu-monitor/references/distribution.md](weibo-feishu-monitor/references/distribution.md)
- [weibo-feishu-monitor/references/commands.md](weibo-feishu-monitor/references/commands.md)

## Example Prompts

- `Use $weibo-feishu-monitor to check the monitor status`
- `Use $weibo-feishu-monitor to send one Feishu test card`
- `Use $weibo-feishu-monitor to send a specific Weibo post by timestamp`
- `Use $weibo-feishu-monitor to verify retweet rendering`
