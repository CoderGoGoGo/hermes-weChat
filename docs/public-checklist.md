# Public release desensitization checklist

发布任何与 Hermes + Weixin / 微信接入相关的公开内容前，先过一遍这个检查单。

## 1. 绝对不能公开的内容

以下内容不要上传到 GitHub、博客、截图平台或聊天群：

- `WEIXIN_TOKEN`
- `WEIXIN_ACCOUNT_ID`
- `~/.hermes/.env`
- `~/.hermes/weixin/accounts/*.json`
- 任何二维码原始图片或可恢复二维码 payload 的截图
- 含 token 的终端输出
- 含真实联系人、群聊、用户 ID 的日志
- 含个人对话内容的截图

## 2. 发仓库前先跑这几个检查

### 检查当前变更

```bash
git status --short
```

### 检查已跟踪文件里是否有敏感关键词

```bash
git grep -nE 'WEIXIN_TOKEN|WEIXIN_ACCOUNT_ID|GATEWAY_ALLOW_ALL_USERS|Authorization|Bearer ' || true
```

### 检查是否错误加入了 env 或 json

```bash
git ls-files | grep -E '\.env$|weixin/accounts|\.json$' || true
```

注意：这个命令会把普通 json 也列出来，所以要人工判断。但如果看见 `weixin/accounts`，基本就是不该提交的内容。

## 3. 文档脱敏规则

公开文档里建议这样写：

- 用 `<your-user>` 代替真实用户名
- 用 `<account_id>` 代替真实 account id
- 用 `...` 代替日志里的敏感字段
- 用示例域名代替真实域名
- 用示例路径代替个人目录

示例：

```text
~/.hermes/weixin/accounts/<account_id>.json
```

而不是：

```text
~/.hermes/weixin/accounts/123456789_real_id.json
```

## 4. 截图检查

发截图前确认：

- 没有二维码
- 没有 token
- 没有联系人昵称
- 没有群名
- 没有个人头像
- 没有私人消息内容
- 没有终端里的真实 home 路径和账号信息

## 5. 如果已经误提交了怎么办

如果只是工作区未提交：

```bash
git restore --staged <file>
rm <file>
```

如果已经 commit 但未 push：

```bash
git reset --soft HEAD~1
```

然后移除敏感文件，重新提交。

如果已经 push 到 GitHub：

- 立刻删除仓库中的敏感文件
- 立即轮换相关 token / 凭证
- 视情况重写 git 历史
- 不要假设“删掉最新版文件”就等于安全

## 6. 发布前最后 30 秒检查

在 push 前最后看一遍：

```bash
git diff --cached
```

重点确认：

- 没有 `.env`
- 没有 account json
- 没有二维码截图
- 没有真实日志
- 没有真实用户身份信息

## 7. 最保守的原则

如果你不确定某个文件能不能公开：

不要发。

先复制一份，做脱敏，再发布。
