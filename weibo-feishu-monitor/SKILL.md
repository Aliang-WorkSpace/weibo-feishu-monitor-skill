---
name: weibo-feishu-monitor
description: "Monitor Weibo accounts and send rendered Weibo card images to Feishu chats. Use when Codex needs to run, test, tune, or troubleshoot the automated Weibo-to-Feishu workflow, including checking monitored accounts, sending a real test card to Feishu, adjusting card layout or text-cleaning rules, managing the launchd monitor job, or diagnosing delivery failures, dedupe behavior, retweet rendering, and browser-profile issues."
---

# Weibo Feishu Monitor

Use this skill for a repo-based Weibo monitoring pipeline plus a separate live deployment directory.

## Quick Start

- Read [references/distribution.md](references/distribution.md) first to map this skill onto the recipient's machine.
- Read [references/commands.md](references/commands.md) for command templates.
- Treat `PROJECT_ROOT` as the editable source workspace.
- Treat `LIVE_ROOT` as the live deployment.
- After changing `MONITOR_SCRIPT` in the source workspace, sync it into the live deployment copy before validating real delivery behavior.

## Required Local Variables

Define these values for the target machine before using the command templates:

- `PROJECT_ROOT`: source repo root that contains `lanxi_weibo_monitor.py`
- `LIVE_ROOT`: live deployment directory used by launchd or other schedulers
- `MONITOR_SCRIPT`: usually `${PROJECT_ROOT}/lanxi_weibo_monitor.py`
- `TEST_SCRIPT`: usually `${PROJECT_ROOT}/scripts/test_weibo_monitor_delivery.sh`
- `PROFILE_DIR`: browser profile directory used for logged-in Weibo access
- `OPENAPI_ENV_FILE`: Feishu app credential env file
- `WEBHOOK_ENV_FILE`: Feishu webhook env file when used as fallback
- `RECEIVE_ID` and `RECEIVE_ID_TYPE`: target Feishu chat/user identifiers

## Default Behavior

- Monitor Weibo accounts with browser fallback when the public API is unavailable.
- When browser fallback is used, keep scrolling to collect more posts instead of trusting only the first screen.
- Render each Weibo into one long card image.
- Keep the monitored user's avatar in the card.
- Fetch full text for long posts instead of truncating at `展开全文`.
- Strip trailing UI text like `收起`.
- Use a more distinct quote style for retweets and show the original author as `@用户名`.
- Re-sort browser-fetched posts by parsed publish time so older pinned or historical cards do not crowd out newer posts.
- Dedupe formal deliveries so the same post is not re-sent to the same Feishu chat.
- Mark manual test sends with a `TEST` badge.

## Workflow

1. Confirm whether the user wants source changes, a live test, or both.
2. Inspect the current source files in `PROJECT_ROOT`.
3. If behavior changes are needed, edit the source copy first.
4. Run the monitor unit tests before touching live delivery.
5. Sync the updated monitor script into the live deployment copy under `LIVE_ROOT`.
6. For delivery checks, prefer the dedicated test script so formal state is not polluted.
7. For scheduler issues, inspect the live agent and its logs rather than assuming the repo template reflects runtime state.

## Validation Rules

- Use the dedicated test-send flow for safe validation of Feishu rendering.
- When the user wants a specific Weibo post, identify the exact post first, then send only that post.
- When the user wants to verify retweet rendering, prefer a historical retweet post instead of waiting for a new one.
- When validating launchd, check both job state and the latest stdout/stderr logs.
- When debugging missing content, verify browser fetch output before blaming rendering.
- When a monitored account seems to be missing, compare the fetched post timestamps before assuming the account is absent from the watch list.
- Be careful with pinned posts: page-top historical cards can appear before newer posts during browser fallback, so verify with a deeper fetch.

## Files To Know

- Source monitor script: `${PROJECT_ROOT}/lanxi_weibo_monitor.py`
- Source tests: `${PROJECT_ROOT}/tests/test_lanxi_weibo_monitor.py`
- Test-send script: `${PROJECT_ROOT}/scripts/test_weibo_monitor_delivery.sh`
- Live monitor script: `${LIVE_ROOT}/lanxi_weibo_monitor.py`
- Live state: `${LIVE_ROOT}/runtime/lanxi_weibo_monitor_state.json`
- Live delivery dedupe state: `${LIVE_ROOT}/runtime/lanxi_weibo_delivery_state.json`
- Live logs:
  `${LIVE_ROOT}/logs/lanxi_weibo_monitor.out.log`
  `${LIVE_ROOT}/logs/lanxi_weibo_monitor.err.log`

## References

- Distribution and setup: [references/distribution.md](references/distribution.md)
- Command cookbook: [references/commands.md](references/commands.md)
