# 🎮 FreezeHost 自动续期助手

基于 Playwright + GitHub Actions 优化的 FreezeHost 免费服务器自动续期脚本。本项目在原版的基础上，完善了并发逻辑与防风控机制。

## ✨ 核心特性

- 🛡️ **Token 登录**：直接通过 Token 绕过 Discord 登录拦截与人机验证。
- 🔄 **多账号隔离**：并发执行多个独立账号的巡检，互不干扰。
- 🤖 **智能读取**：自动抓取由于面板内服务器名与您的 Discord 真实昵称。
- 💰 **余额监控**：自动提取账号里的可用金币（AVAILABLE BALANCE）。
- ⏳ **精确计算**：自动换算续期倒计时，倒推至精准的时分显示。
- ⚡ **环境缓存**：依赖 Actions 缓存提速与故障自动重试机制。

## ⚙️ 配置步骤

### 1️⃣ Fork 此仓库

### 2️⃣ 添加 Secrets
进入仓库 → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**：

| 🔑 Secret 名称 | 📝 格式 | ✅ 必填 |
|---|---|---|
| `DISCORD_TOKEN` | `TokenA,TokenB` （多个用**英文逗号** `,` 隔开，加不加空格均可） | ✅ |
| `TG_BOT` | `chat_id,bot_token` | ✅ |
| `GOST_PROXY` | `socks5://host:port` 或 `http://host:port` | 可选 |

> **💡 自定义账号名称（可选）**
> 脚本默认会自动抓取您的 Discord 昵称。如需覆盖，可在 Token 前加 `名字:` 或 `名字#`。
> **示例**：`张三:TokenA, 李四#TokenB, TokenC`

> **⚠️ 严重警告：保护您的 Discord Token！**
> Token 代表您的最高控制权。**绝不要发给任何人，绝不可提交在公开的代码仓库里，只能保存在 Private Secret 中！** 
> 一旦不慎泄露，请立即修改 Discord 密码（此行为会自动作废旧 Token）。

> **💡 如何获取 Discord Token？**
> 1. 在电脑浏览器登录 [Discord 网页版](https://discord.com/app)。
> 2. 按 `F12` 打开开发者工具，切换至 **Network (网络)** 面板。
> 3. 点击 **Fetch/XHR** 过滤。
> 4. 随便点击其它的 Discord 频道，抓取名为 `science` 或 `messages` 的短请求。
> 5. 在该请求右侧的 **Request Headers (请求标头)** 中，复制 `Authorization` 字段的值即为 Token。

### 3️⃣ 启用 Actions
进入 **Actions** 标签页，点击绿色按钮 **I understand my workflows, go ahead and enable them**。手动执行一次 **Run workflow** 即可初步测试。

## 🕐 运行机制
- 服务器剩余时间 `<=` 7 天时才可续期。脚本会自动识别并在满足条件时点击。
- 脚本默认执行频率为 **每 3 天** 自动运行一次，完美契合机制并防止频繁刷新触发防刷风控。
- *(如需调整时间，请修改 `.github/workflows/freeze.yml` 的 `cron` 表达式)*

## 📊 续期通知

您将在 Telegram 收到类似如下清爽、直观的多服务器监控汇报：

```text
🎮 FreezeHost 续期报告
🕐 时间: 2026-03-26 11:30
========================
👤 DiscordUser | 💰 1,024
  📦 Minecraft_Node_A
  ├─ 状态: ✅ 续期成功
  └─ 剩余: 14天 0小时 0分钟

  📦 Test_Bot_01
  ├─ 状态: 无需续期
  └─ 剩余: 12天 13小时 15分钟

🌐 官网入口: https://free.freezehost.pro
```

### 📝 可能收到的状态说明
- **无需续期**：剩余时间 >7 天。您的机器依然处于绝对安全的存活区间，脚本只做查询，静默越过，绝不乱碰按钮。
- ✅ **续期成功**：脚本已成功点击，为您满血重置了机器（通常显示 14 天倒计时）。
- ⚠️ **余额不足**：当前账号自带的金币不足以续费该台服务器本月账单。
- ⏰ **未到条件**：尽管理论时间进入 <=7 窗口，但面板内的 Renew 按钮仍然锁定。忽略它，等脚本下一次定时循环去触发即可。
- ❌ **异常报错**：一般由于官方增加新规人机验证，或网页大改版。建议去 Actions 查看详细报错截图排查。
- ❓ **结果未知**：脚本一脚踢中了续期按钮，但被面板自动弹到了不知名的网页。建议有空上线核实。
