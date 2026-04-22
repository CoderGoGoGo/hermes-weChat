# hermes-weChat

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Last Commit](https://img.shields.io/github/last-commit/CoderGoGoGo/hermes-weChat)](https://github.com/CoderGoGoGo/hermes-weChat/commits/main)
[![Stars](https://img.shields.io/github/stars/CoderGoGoGo/hermes-weChat?style=social)](https://github.com/CoderGoGoGo/hermes-weChat/stargazers)

A public setup guide for connecting Hermes Agent to Weixin / WeChat using Hermes native support.

一个公开说明仓库：记录如何使用 Hermes 原生能力接入微信 / WeChat，而不是依赖外部 OpenClaw Weixin 安装器。

## 中文说明

这个仓库主要解决的是：

- 如何让 Hermes 接入微信
- 如何只做一次二维码登录
- 如何把登录态保存在本地
- 如何在后续运行中继续复用该登录态
- 如何避免把敏感凭证错误上传到 GitHub

这不是一个微信插件二进制仓库，也不是一个账号凭证仓库。

它只是把可公开分享的方法、路径、踩坑点和排错方式整理出来。

## TL;DR

核心结论：

- 对 Hermes 来说，正确路径是 Hermes 原生 Weixin 支持
- 不要把 OpenClaw 的 Weixin installer 当成 Hermes 的安装方式
- 首次扫码登录后，Hermes 会把关键信息保存在本地
- 如果登录成功但消息仍被拒绝，通常是权限策略没有配好

不要使用：

```bash
npx -y @tencent-weixin/openclaw-weixin-cli@latest install
```

这个安装器适用于 OpenClaw 相关流程，不是 Hermes 原生微信接入的正确入口。

## Directory

- [中文说明](#中文说明)
- [TL;DR](#tldr)
- [What this repo is](#what-this-repo-is)
- [What this repo does not contain](#what-this-repo-does-not-contain)
- [Recommended setup flow](#recommended-setup-flow)
- [Risk notice](#risk-notice)
- [Common FAQ](#common-faq)
- [Repository layout](#repository-layout)
- [Security checklist before every push](#security-checklist-before-every-push)
- [License](#license)

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

## Risk notice

在公开分享这类方法时，最大的风险不是“配不起来”，而是“把不该公开的东西公开了”。

请特别注意：

- 不要提交 `.env`
- 不要提交任何 `WEIXIN_TOKEN`
- 不要提交 `~/.hermes/weixin/accounts/*.json`
- 不要上传包含二维码原始内容的截图
- 不要上传带 token 的终端日志
- 不要把真实用户 ID、群聊 ID、私人对话记录写进文档

如果你准备把自己的接入过程整理成博客或仓库，建议先做一遍脱敏检查。

更完整的发布前检查见：

- [docs/public-checklist.md](docs/public-checklist.md)

## Common FAQ

### 1. 为什么不用 OpenClaw 的 Weixin installer？

因为它不是 Hermes 原生微信接入的正确安装路径。

这个 installer 面向的是 OpenClaw 相关流程，不是 Hermes 自带 gateway 的原生 Weixin setup。

### 2. 首次扫码后，登录态保存在哪里？

通常在这两个位置：

- `~/.hermes/.env`
- `~/.hermes/weixin/accounts/<account_id>.json`

### 3. 为什么已经登录成功了，但消息还是被 Hermes 拒绝？

最常见原因不是登录失败，而是授权策略没有配好。

典型日志症状：

- `Unauthorized user: ... on weixin`
- `No user allowlists configured...`

这时需要检查 allowlist 或者网关开放策略。

### 4. 最快的排障办法是什么？

先确认三件事：

1. `~/.hermes/.env` 里是否已有 `WEIXIN_*`
2. `~/.hermes/weixin/accounts/` 里是否已有 account json
3. gateway status 是否显示 weixin connected

### 5. 如果我只是想快速测试通不通？

一种简单但更开放的做法是：

- 在 `~/.hermes/.env` 里设置 `GATEWAY_ALLOW_ALL_USERS=true`
- 重启 gateway

但这只适合你明确接受开放策略的场景。

### 6. 哪个目录是对的？

正确的是：

```text
~/.hermes/weixin/accounts/
```

不是：

```text
~/.hermes/accounts/
```

更多问题见 [docs/troubleshooting.md](docs/troubleshooting.md)。

## Repository layout

- `README.md` — public overview
- `docs/weixin-setup.md` — detailed setup instructions
- `docs/troubleshooting.md` — common failure cases
- `docs/public-checklist.md` — pre-release desensitization checklist
- `.gitignore` — protects against credential leaks
- `LICENSE` — project license

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
