# Security Audit — AI Agent 安全审计技能集

[English](#english) | 中文

一套面向 AI 编程助手的**安全审计技能（Skills）**，兼容 agentSkills.io 规范，可安装到 WorkBuddy、Claude Code、Cursor 等主流 AI 工具中。

---

## 包含技能

| 技能 | 描述 |
|------|------|
| [**web-sast**](./web-sast/) | Web 前端安全审计：页面源码扫描、安全头检测、SSL/TLS 配置、硬编码密钥、XSS/CSRF 等 |
| [**mini-program-sast**](./mini-program-sast/) | 微信小程序安全审计：wxapkg 反编译分析、敏感数据泄露、API 端点暴露、客户端逻辑绕过等 |

---

## 快速安装

### WorkBuddy
```bash
git clone https://github.com/cyj4578/Security-Audit.git
cp -r Security-Audit/web-sast ~/.workbuddy/skills/
cp -r Security-Audit/mini-program-sast ~/.workbuddy/skills/
```

### Claude Code / Cursor
```bash
cp -r Security-Audit/web-sast ~/.cursor/skills/
cp -r Security-Audit/mini-program-sast ~/.cursor/skills/
```

### 其他兼容 agentSkills.io 的工具
将 `web-sast/` 和/或 `mini-program-sast/` 目录复制到对应工具的 skills 文件夹即可。

---

## 使用示例

```
# Web 安全审计
"帮我扫描一下 https://example.com 的安全性"

# 小程序安全审计
"帮我检查小程序 wx1234567890abcdef 有没有安全问题"
```

---

## License

MIT

---

## English

A collection of **AI agent security audit skills** compatible with the agentSkills.io specification. Installable on WorkBuddy, Claude Code, Cursor, and other AI tools.

| Skill | Description |
|-------|-------------|
| [**web-sast**](./web-sast/) | Web front-end security audit: page source scan, security headers, SSL/TLS, hardcoded secrets, XSS/CSRF |
| [**mini-program-sast**](./mini-program-sast/) | WeChat Mini Program security audit: wxapkg decompile, sensitive data leaks, API exposure, client-side bypass |
