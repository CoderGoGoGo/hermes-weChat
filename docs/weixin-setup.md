# Hermes native Weixin setup

This document describes the public, repeatable setup flow for connecting Hermes Agent to Weixin / WeChat on macOS using Hermes native support.

## 1. Do not use the OpenClaw Weixin installer for this path

Do not use:

```bash
npx -y @tencent-weixin/openclaw-weixin-cli@latest install
```

Reason:

- it is designed for OpenClaw-related workflows
- it checks for `openclaw`
- it is not the correct installer for Hermes native Weixin support

## 2. Verify Hermes exists and native support is available

Check Hermes CLI:

```bash
command -v hermes
```

Then verify the Python modules Hermes uses for Weixin support.

Example:

```bash
/Users/<your-user>/.hermes/hermes-agent/venv/bin/python - <<'PY'
import sys
sys.path.insert(0, '/Users/<your-user>/.hermes/hermes-agent')
from gateway.platforms.weixin import check_weixin_requirements
from gateway.platforms.api_server import check_api_server_requirements
print('weixin_requirements', check_weixin_requirements())
print('api_server_requirements', check_api_server_requirements())
PY
```

Expected outcome:

- Weixin requirements are available
- API server requirements are available

## 3. Preferred login method: native QR login

A direct QR login is more reliable than trying to automate the full interactive menu.

Example:

```bash
/Users/<your-user>/.hermes/hermes-agent/venv/bin/python - <<'PY'
import asyncio, sys
sys.path.insert(0, '/Users/<your-user>/.hermes/hermes-agent')
from gateway.platforms.weixin import qr_login
print(asyncio.run(qr_login('/Users/<your-user>/.hermes')))
PY
```

Then:

- scan the QR code immediately
- confirm the login on the phone

This is the one manual step that cannot be skipped.

## 4. Verify credentials were persisted

After successful login, verify both locations.

### Environment variables

Check:

```bash
grep '^WEIXIN_' ~/.hermes/.env
```

Typical values written by setup:

- `WEIXIN_ACCOUNT_ID`
- `WEIXIN_TOKEN`
- `WEIXIN_BASE_URL`
- `WEIXIN_CDN_BASE_URL`
- `WEIXIN_DM_POLICY`
- `WEIXIN_GROUP_POLICY`
- optionally `WEIXIN_HOME_CHANNEL`

### Account persistence

Check:

```bash
ls ~/.hermes/weixin/accounts/
```

Expected persisted file layout:

```text
~/.hermes/weixin/accounts/<account_id>.json
```

Important note:

The correct account directory is:

```text
~/.hermes/weixin/accounts/
```

Not:

```text
~/.hermes/accounts/
```

## 5. Restart and verify gateway status

Useful checks:

```bash
~/.local/bin/hermes gateway status
```

Inspect state if needed:

```bash
cat ~/.hermes/gateway_state.json
```

A healthy result should show Weixin connected.

## 6. If login works but incoming messages are rejected

A common failure mode is:

- QR login succeeds
- gateway is up
- incoming Weixin messages are denied as unauthorized

Typical log symptoms:

```text
Unauthorized user: ... on weixin
No user allowlists configured...
```

One fast fix when open access is acceptable:

```bash
printf '\nGATEWAY_ALLOW_ALL_USERS=true\n' >> ~/.hermes/.env
```

Then restart the gateway and verify again.

Use a stricter allowlist policy if you do not want open access.

## 7. Optional policy fields

Interactive setup may also configure message policies.

Typical choices include:

### DM policy

- `pairing`
- `open`
- `allowlist`
- `disabled`

### Group policy

- `disabled`
- `open`
- `allowlist`

## 8. Troubleshooting notes

### Stale process output can mislead

If you used a background setup process, old watch output can make it look like setup is still active even after the process already exited.

Do not trust banner text alone.

Use these as source of truth:

- `~/.hermes/.env`
- `~/.hermes/weixin/accounts/*.json`
- gateway status

### WhatsApp state can remain stale after removal

If the machine previously used WhatsApp, `~/.hermes/gateway_state.json` may still show stale WhatsApp state even after cleanup.

Treat launchd state and `.env` as the real source of truth.

## 9. Security rules for public documentation

Never publish:

- live token values
- account JSON contents
- real user identifiers
- screenshots containing QR payloads
- copied logs containing credentials
