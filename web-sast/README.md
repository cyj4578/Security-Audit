# Web SAST — Web 前端静态安全扫描

> [English version](README_EN.md)

<p align="center">
  <strong>🛡️ 只需一个 URL，自动完成 4 阶段安全扫描，输出 HTML 格式分级审计报告</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-2.0.0-blue" alt="version">
  <img src="https://img.shields.io/badge/license-MIT-green" alt="license">
  <img src="https://img.shields.io/badge/standard-agentSkills.io-orange" alt="standard">
</p>

---

## 📖 简介

**Web SAST** 是一个 AI Agent Skill，用于对 Web 前端页面进行静态安全扫描（SAST）。输入一个 URL，自动执行 4 阶段扫描流程，输出一份包含分级漏洞、修复建议和等保影响分析的 **结构化 HTML 安全审计报告**（浏览器打印即可导出 PDF）。

### 它能做什么

| 能力 | 说明 |
|------|------|
| 🔍 **HTTP 安全头分析** | 检测 CSP、HSTS、X-Frame-Options 等 6 类安全头 |
| 🔐 **SSL/TLS 证书检查** | 验证证书有效期、颁发机构、SAN 覆盖 |
| 📦 **前端源码提取** | 自动下载 HTML + 外部 JS/CSS 资源 |
| 🧪 **15 条漏洞规则扫描** | 覆盖硬编码密钥、XSS、CSRF、PII 泄露等 |
| 📊 **分级审计报告** | 9 大章节 HTML 报告，含修复代码示例 |

### 它不能做什么

- ❌ 后端渗透测试（SQL 注入、命令注入等需交互式探测）
- ❌ DDoS / 网络层测试
- ❌ 需要登录才能访问的页面（除非提供认证信息）
- ❌ 替代 OWASP ZAP / Burp Suite 等专业安全工具
- ❌ 对目标发起攻击性行为（只读分析，零副作用）

---

## 🚀 快速开始

### 在 AI 工具中使用

直接将 `SKILL.md` 安装到支持的 AI 工具中：

**WorkBuddy / Claude Code / Cursor**
```bash
# 将整个目录放入 skills 文件夹
cp -r web-sast/ ~/.workbuddy/skills/
# 或 Cursor: cp -r web-sast/ ~/.cursor/skills/
```

**其他支持 agentSkills.io 规范的工具**
```bash
# 下载仓库
git clone https://github.com/chen-mingsong/web-sast.git
# 将 web-sast/ 目录放入工具的 skills 目录中
```

### 命令行使用

直接运行自动化扫描脚本（仅依赖系统自带工具：`curl`、`openssl`、`grep`）：

```bash
# 赋予执行权限
chmod +x references/audit.sh

# 扫描目标
./references/audit.sh https://example.com/login
```

### 在对话中使用

安装后，在 AI 对话中直接说：

> "帮我扫描一下 https://example.com"
>
> "这个网站有安全漏洞吗？"
>
> "检查这个页面的 XSS 和 CSRF"

AI 会自动加载 Skill 并执行 4 阶段扫描。

---

## 🧩 扫描流程

```
用户给 URL → Phase 1: 表面扫描 → Phase 2: 源码提取 → Phase 3: 深度扫描 → Phase 4: 报告生成
```

| 阶段 | 内容 | 耗时 |
|------|------|------|
| **Phase 1** | HTTP 响应头 + SSL 证书检查 | ~10s |
| **Phase 2** | HTML 下载 + JS/CSS 资源提取 | ~15s |
| **Phase 3** | 15 条检测规则模式匹配 | ~30s |
| **Phase 4** | HTML 报告生成（浏览器打印导出 PDF） | ~5s |

---

## 📊 检测规则

### Critical 级别（5 条）
- `CRIT-01` 硬编码密钥 / 万能密码
- `CRIT-02` Math.random() 用于安全场景
- `CRIT-03` 客户端验证码生成
- `CRIT-04` PII 在 URL 参数中传输
- `CRIT-05` 所有安全头缺失

### High 级别（6 条）
- `HIGH-01` eval() / new Function()
- `HIGH-02` innerHTML 赋值（XSS sink）
- `HIGH-03` 表单无 CSRF Token
- `HIGH-04` console.log 打印敏感数据
- `HIGH-05` Server 版本暴露
- `HIGH-06` 敏感页面的第三方追踪器

### Medium 级别（4 条）+ Low 级别（2 条）
完整规则列表见 `references/rules.md`

---

## 📄 报告输出

扫描完成后生成 HTML 格式安全审计报告，包含 **9 大章节**：

1. **执行摘要** — 四色统计卡片 + 总体评级
2. **目标信息** — URL、扫描时间、技术栈
3. **扫描流程回顾** — 每步状态 + 说明
4. **HTTP 安全头与 SSL** — 完整状态表
5. **漏洞详细分析** — 按严重度分组，含代码片段 + 修复方案
6. **修复优先级与等保影响** — P0/P1/P2 排序 + 工时估算
7. **与专业工具对比** — vs OWASP ZAP / Burp Suite
8. **建议与后续行动** — 编号建议表
9. **审计局限性声明** — 方法、范围、误报说明

> 报告使用 print-friendly CSS，浏览器 `Cmd+P` →「另存为 PDF」一键导出。

---

## 🛠️ 文件结构

```
web-sast/
├── README.md                          # 中英双语说明文档
├── README_EN.md                       # 纯英文说明文档
├── SKILL.md                           # 核心 Skill 文件（符合 agentSkills.io 规范）
└── references/
    ├── rules.md                       # 15 条检测规则完整 grep 模式库
    ├── international-standards.md     # agentSkills.io 国际规范论述
    └── audit.sh                       # 一键运行自动化扫描脚本
```

---

## 🔧 兼容性

| AI 工具 / 平台 | 支持状态 |
|---------------|---------|
| WorkBuddy | ✅ 原生支持 |
| Claude Code | ✅ 支持（agentSkills.io 规范） |
| Cursor | ✅ 支持（skills 目录） |
| 其他 agentSkills.io 兼容工具 | ✅ 通用格式兼容 |
| 命令行（bash） | ✅ `references/audit.sh` |

### 系统依赖

- `curl` — 系统自带（macOS / Linux）
- `openssl` — 系统自带
- `grep` — 系统自带
- `bash` — 系统自带

---

## 🆚 与专业工具对比

| 维度 | Web SAST | OWASP ZAP | Burp Suite Pro |
|------|----------|-----------|----------------|
| **类型** | AI 驱动静态分析 | 自动化 DAST + 代理 | 交互式 DAST + 代理 |
| **速度** | 30s ~ 2min | 5 ~ 30min | 无上限 |
| **安装** | 零安装（curl + grep） | 需 Java 环境 | 需 Java + 付费许可 |
| **检测深度** | 源码模式匹配 | 主动探测 + 被动爬取 | 完整渗透测试套件 |
| **适用场景** | 快速初筛、自查 | CI/CD 安全门禁 | 合规测评、红队 |

---

## 📝 与 Mini Program SAST 的关系

| | Web SAST | Mini Program SAST |
|------|----------|-------------------|
| **目标** | Web 页面（H5 / SPA） | 微信小程序 .wxapkg 包 |
| **输入** | URL | 小程序 AppID |
| **扫描方式** | HTTP 请求 + 源码下载 | 本地缓存文件 strings 提取 |
| **检测规则** | 15 条 Web 前端规则 | 17 条小程序专用规则 |

---

## ⚠️ 免责声明

本工具仅用于合法授权的安全审计和自查目的。使用者应确保：
- 对扫描目标拥有合法授权
- 不利用发现的漏洞攻击第三方服务
- 遵守当地法律法规

作者不对滥用行为承担任何责任。

---

## 📄 开源协议

MIT License — 详见 [LICENSE](LICENSE) 文件。

---

## 🙋 FAQ

**Q: 扫描结果准确吗？会不会有误报？**
A: 基于模式匹配，存在一定误报率。每条发现都附带上下文代码片段，需要结合业务逻辑判断是否构成真实风险。

**Q: 扫描会对目标网站造成影响吗？**
A: 不会。只执行 `curl` GET 请求读取公开页面，零副作用。

**Q: 能扫描需要登录的页面吗？**
A: 默认不支持。如果提供 Cookie 或 Token，可以通过修改 audit.sh 中的 curl 命令添加认证头来实现。

**Q: 发现漏洞后怎么修？**
A: 每条漏洞发现都附带具体的前后端修复代码示例。
