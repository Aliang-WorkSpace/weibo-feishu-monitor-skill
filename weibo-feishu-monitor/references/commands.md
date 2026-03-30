# Command Cookbook

Replace the shell variables below for the recipient's machine before using any command:

```bash
PROJECT_ROOT=/absolute/path/to/repo
LIVE_ROOT=/absolute/path/to/live-monitor
MONITOR_SCRIPT="${PROJECT_ROOT}/lanxi_weibo_monitor.py"
TEST_SCRIPT="${PROJECT_ROOT}/scripts/test_weibo_monitor_delivery.sh"
PROFILE_DIR="${LIVE_ROOT}/.weibo_profile"
OPENAPI_ENV_FILE="${LIVE_ROOT}/.env.feishu.openapi"
WEBHOOK_ENV_FILE="${LIVE_ROOT}/.env.feishu"
RECEIVE_ID_TYPE=chat_id
RECEIVE_ID=oc_xxx
```

## Typical Commands

### Run unit tests

```bash
python3 -m unittest "${PROJECT_ROOT}/tests/test_lanxi_weibo_monitor.py"
```

### Sync source script into live deployment

```bash
cp "${MONITOR_SCRIPT}" "${LIVE_ROOT}/lanxi_weibo_monitor.py"
```

### Send one manual test card to Feishu

```bash
"${TEST_SCRIPT}"
```

### Send a manual test card for a specific account

```bash
"${TEST_SCRIPT}" 5872251960 老talk
```

### Inspect recent Weibo posts in the live browser session

```bash
/usr/bin/python3 - <<'PY'
from pathlib import Path
import os
import sys
sys.path.insert(0, os.environ["LIVE_ROOT"])
import lanxi_weibo_monitor as m

uid = '1560906700'
posts = m.fetch_latest_posts_via_browser(uid, 5, Path(os.environ["PROFILE_DIR"]), 'chrome')
for p in posts:
    print('---')
    print(p.created_at)
    print(p.url)
    print((p.text or '').split('\n')[0][:120])
PY
```

### Send one specific post to Feishu

Use this pattern when the user names a timestamp or URL.

```bash
/usr/bin/python3 - <<'PY'
from argparse import Namespace
from pathlib import Path
import os
import sys
sys.path.insert(0, os.environ["LIVE_ROOT"])
import lanxi_weibo_monitor as m

uid = '1560906700'
name = '阑夕'
target_time = '2026-03-30 13:03'
posts = m.fetch_latest_posts_via_browser(uid, 10, Path(os.environ["PROFILE_DIR"]), 'chrome')
target = next(post for post in posts if str(post.created_at).strip() == target_time)
target.user_name = name
target.uid = uid
args = Namespace(
    browser_channel='chrome',
    receive_id_type=os.environ["RECEIVE_ID_TYPE"],
    receive_id=os.environ["RECEIVE_ID"],
    openapi_env_file=os.environ["OPENAPI_ENV_FILE"],
    webhook_env_file=os.environ["WEBHOOK_ENV_FILE"],
    no_queue=False,
    test_badge='TEST',
)
print(m.send_post_to_feishu(target, args, delivery_state={}))
PY
```

### Send one historical retweet post to Feishu

Use this pattern when the user wants to verify retweet quote styling and original-author display.

```bash
/usr/bin/python3 - <<'PY'
from argparse import Namespace
from pathlib import Path
import os
import sys
sys.path.insert(0, os.environ["LIVE_ROOT"])
import lanxi_weibo_monitor as m

uid = '1987026237'
name = '奶爸兄地'
target_time = '2026-03-30 14:32'
posts = m.fetch_latest_posts_via_browser(uid, 25, Path(os.environ["PROFILE_DIR"]), 'chrome')
target = next(post for post in posts if post.is_retweet and str(post.created_at).strip() == target_time)
target.user_name = name
target.uid = uid
args = Namespace(
    browser_channel='chrome',
    receive_id_type=os.environ["RECEIVE_ID_TYPE"],
    receive_id=os.environ["RECEIVE_ID"],
    openapi_env_file=os.environ["OPENAPI_ENV_FILE"],
    webhook_env_file=os.environ["WEBHOOK_ENV_FILE"],
    no_queue=False,
    test_badge='TEST',
)
print(m.send_post_to_feishu(target, args, delivery_state={}))
PY
```

## Scheduler Operations

The exact service name differs by machine. Treat `<launchd-service-name>` as a placeholder.

### Check live scheduler job

```bash
launchctl print gui/$(id -u)/<launchd-service-name>
```

### Trigger one manual scheduler run

```bash
launchctl kickstart -k gui/$(id -u)/<launchd-service-name>
```

## Logs And State

### Tail live stdout log

```bash
tail -n 80 "${LIVE_ROOT}/logs/lanxi_weibo_monitor.out.log"
```

### Tail live stderr log

```bash
tail -n 40 "${LIVE_ROOT}/logs/lanxi_weibo_monitor.err.log"
```

### Inspect monitor state

```bash
cat "${LIVE_ROOT}/runtime/lanxi_weibo_monitor_state.json"
```

### Inspect delivery dedupe state

```bash
cat "${LIVE_ROOT}/runtime/lanxi_weibo_delivery_state.json"
```

## Behavior Notes

- Formal monitoring sends one long image per post.
- Long posts are expanded via detail-page fetch before rendering.
- Trailing UI text such as `收起` is stripped during text normalization.
- Retweet cards show the original author name in the quote header, for example `转发原文 · @原作者`.
- Test sends add a `TEST` badge and do not participate in formal delivery dedupe.
- Formal sends record delivery fingerprints in `lanxi_weibo_delivery_state.json`.
