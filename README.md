# hermes-weChat

A minimal public guide for connecting Hermes Agent to Weixin / WeChat using Hermes native support.

## What this repo is

This repository documents the method used to connect Hermes to Weixin on macOS without relying on an external OpenClaw Weixin installer.

It focuses on:

- using Hermes native Weixin support
- doing the first QR login once
- keeping login state persisted locally
- running Hermes afterward without needing a visible WeChat app window every time

## What this repo does not contain

This repository does not include any private credentials or personal account data.

Never commit any of the following:

- `WEIXIN_TOKEN`
- `WEIXIN_ACCOUNT_ID`
- `~/.hermes/.env`
- `~/.hermes/weixin/accounts/*.json`
- QR login session data

## Key conclusion

For Hermes, the correct path is Hermes native Weixin support.

Do not use:

- `npx -y @tencent-weixin/openclaw-weixin-cli@latest install`

That installer is for OpenClaw-related workflows and is not the right drop-in setup path for Hermes native gateway support.

## Verified setup model

Goal:

- scan QR once
- persist login state locally
- Hermes uses the saved Weixin account state afterward

Persisted locations used by Hermes:

- `~/.hermes/.env`
- `~/.hermes/weixin/accounts/<account_id>.json`

Important environment variables written by setup typically include:

- `WEIXIN_ACCOUNT_ID`
- `WEIXIN_TOKEN`
- `WEIXIN_BASE_URL`
- `WEIXIN_CDN_BASE_URL`
- `WEIXIN_DM_POLICY`
- `WEIXIN_GROUP_POLICY`
- optionally `WEIXIN_HOME_CHANNEL`

## Recommended setup flow

See [docs/weixin-setup.md](docs/weixin-setup.md).

Short version:

1. Verify Hermes already has built-in Weixin support.
2. Run native Weixin QR login.
3. Scan the QR code with the phone.
4. Confirm `WEIXIN_*` variables exist in `~/.hermes/.env`.
5. Confirm account state exists under `~/.hermes/weixin/accounts/`.
6. Restart Hermes gateway and verify Weixin is connected.
7. If messages arrive but are denied, configure access policy.

## Common issue

If login succeeds but Hermes rejects incoming Weixin messages with an unauthorized error, one practical fix is to configure the gateway policy correctly.

A fast open-access option is:

- set `GATEWAY_ALLOW_ALL_USERS=true` in `~/.hermes/.env`
- restart the gateway

Use this only if it matches your intended security model.

## Repository layout

- `README.md` — public overview
- `docs/weixin-setup.md` — detailed setup instructions
- `docs/troubleshooting.md` — common failure cases
- `.gitignore` — protects against credential leaks
- `LICENSE` — project license

## Publish to GitHub

Basic flow:

```bash
git init
git add .
git commit -m "Initial public docs for Hermes Weixin setup"
gh repo create coderGoGoGo/hermes-weChat --public --source=. --remote=origin --push
```

If the repository already exists:

```bash
git remote add origin git@github.com:coderGoGoGo/hermes-weChat.git
git branch -M main
git push -u origin main
```

## Security checklist before every push

Before pushing, make sure none of these are tracked:

```bash
git status --short
```

Double-check that no secrets appear in commits:

- `.env`
- account JSON files
- copied QR images
- terminal logs containing tokens

## License

MIT
