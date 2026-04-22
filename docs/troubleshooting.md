# Troubleshooting

## Symptom: OpenClaw installer fails

Command:

```bash
npx -y @tencent-weixin/openclaw-weixin-cli@latest install
```

Interpretation:

- this is the wrong installer for Hermes native setup
- switch to Hermes native Weixin QR login instead

## Symptom: login appears successful but Hermes denies messages

Look for logs similar to:

```text
Unauthorized user: ... on weixin
No user allowlists configured...
```

Possible fix:

- configure a proper allow policy
- for simple testing, set `GATEWAY_ALLOW_ALL_USERS=true`
- restart the Hermes gateway

## Symptom: cannot find persisted account state

Check the correct path:

```text
~/.hermes/weixin/accounts/
```

Not:

```text
~/.hermes/accounts/
```

Also check `~/.hermes/.env` for `WEIXIN_*` values.

## Symptom: setup output says it is waiting, but nothing changes

Background TUI output may be stale.

Do not trust the old prompt text alone.

Verify instead:

- `~/.hermes/.env`
- `~/.hermes/weixin/accounts/*.json`
- `~/.local/bin/hermes gateway status`

## Symptom: old WhatsApp state confuses gateway status

If WhatsApp used to be configured, stale state can remain in `gateway_state.json`.

Prefer these checks:

- current `.env`
- launchd / active gateway process status
- fresh gateway logs
