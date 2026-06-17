# Mini Program SAST — 微信小程序前端静态安全扫描

> [English version](README_EN.md)

<p align="center">
  <strong>📱 输入小程序 AppID，自动定位本地缓存 .wxapkg 包，提取代码并输出分级安全审计报告</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-1.0.0-blue" alt="version">
  <img src="https://img.shields.io/badge/license-MIT-green" alt="license">
  <img src="https://img.shields.io/badge/standard-agentSkills.io-orange" alt="standard">
</p>

---

## 📖 简介

**Mini Program SAST** 是一个 AI Agent Skill，用于对微信小程序前端进行静态安全扫描。用户提供小程序 AppID，Skill 自动定位电脑微信本地缓存中的 `.wxapkg` 包文件，提取可读字符串并进行模式匹配漏洞扫描，最终输出一份包含分级漏洞和修复建议的 **结构化 HTML 安全审计报告**（浏览器打印即可导出 PDF）。

### 它能做什么

| 能力 | 说明 |
|------|------|
| 📦 **自动定位缓存包** | 支持 macOS 官网版/AppStore 版/Windows 微信缓存路径 |
| 🔓 **代码提取** | 从二进制 .wxapkg 包中提取可读字符串（strings） |
| 🧪 **17 条专用规则** | 覆盖 setStorageSync 明文存储、login code 持久化、AppSecret 硬编码等 |
| 🔗 **API 端点分析** | 自动提取并列出所有 API 端点 URL |
| 📊 **分级审计报告** | 10 大章节 HTML 报告，含修复代码示例 |

### 它不能做什么

- ❌ 扫描未在本地打开过的小程序（缓存中无 .wxapkg 文件）
- ❌ 穿透小程序后端服务做渗透测试
- ❌ 绕过微信加密传输层抓包
- ❌ 解密经过自定义加密的 .wxapkg 包
- ❌ 替代微信官方「小程序安全扫描」服务

---

## ⚠️ 前置条件

| 条件 | 说明 | 如何验证 |
|------|------|----------|
| **① 小程序已在本地打开过** | 在 Mac/Windows 微信客户端中至少打开过一次该小程序 | 打开微信 → 搜索小程序 → 进入任意页面即可 |
| **② 本地缓存未被清理** | 微信缓存目录中存在 .wxapkg 文件 | Skill 自动检测 |
| **③ 提供正确的 AppID** | 小程序的唯一标识，通常为 `wx` 开头的 16 位字符串 | 见下方获取方式 |
| **④ Mac / Windows 电脑** | 需要访问本地文件系统 | — |

### AppID 获取方式

- **方式一（后台查看）**：登录微信公众平台 → 开发管理 → 开发设置 → AppID
- **方式二（微信内查看）**：点击小程序 → 右上角 `···` →「小程序主页」→ 简介页面可看到 AppID

### 缓存文件位置

| 操作系统 | 缓存路径 |
|---------|---------|
| **macOS（官网版微信）** | `~/Library/Containers/com.tencent.xinWeChat/Data/.wxapplet/packages/{AppID}/` |
| **macOS（AppStore 版微信）** | `~/Library/Containers/com.tencent.xinWeChat/Data/Documents/app_data/radium/users/*/applet/packages/{AppID}/` |
| **Windows** | `%USERPROFILE%\Documents\WeChat Files\Applet\{AppID}\` |

---

## 🚀 快速开始

### 在 AI 工具中使用

**WorkBuddy / Claude Code / Cursor**
```bash
cp -r mini-program-sast/ ~/.workbuddy/skills/
# 或 Cursor: cp -r mini-program-sast/ ~/.cursor/skills/
```

**其他支持 agentSkills.io 规范的工具**
```bash
git clone https://github.com/chen-mingsong/mini-program-sast.git
# 将 mini-program-sast/ 目录放入工具的 skills 目录
```

### 命令行使用

```bash
chmod +x references/audit.sh
./references/audit.sh wx1234567890abcdef
```

### 在对话中使用

安装后，在 AI 对话中直接说：

> "帮我扫描一下小程序 wx1234567890abcdef"
>
> "检查这个小程序有没有密钥泄露"
>
> "审计一下这个小程序的安全性"

---

## 🧩 扫描流程

```
AppID → Phase 0: 前置检查 → Phase 1: 定位提取 → Phase 2: 深度扫描 → Phase 3: 报告生成
```

| 阶段 | 内容 | 耗时 |
|------|------|------|
| **Phase 0** | 前置条件检查（缓存目录定位） | ~5s |
| **Phase 1** | wxapkg 定位 + strings 字符串提取 | ~10s |
| **Phase 2** | 17 条检测规则模式匹配 | ~30s |
| **Phase 3** | HTML 报告生成（浏览器打印导出 PDF） | ~5s |

---

## 📊 检测规则

### Critical 级别（5 条）
- `CRIT-01` wx.setStorageSync 明文存储凭证（token/密码）
- `CRIT-02` wx.login code 被持久化存储
- `CRIT-03` openId / unionId 通过 URL 参数传递
- `CRIT-04` AppSecret / 支付密钥硬编码在前端代码
- `CRIT-05` Mock 模式在生产环境残留

### High 级别（6 条）
- `HIGH-01` console.log 打印敏感数据
- `HIGH-02` API 请求参数含明文敏感信息
- `HIGH-03` 支付签名使用 MD5
- `HIGH-04` 非 HTTPS 的 API 请求
- `HIGH-05` 不安全的页面跳转（user-controlled URL）
- `HIGH-06` 硬编码第三方 appId / corpId

### Medium 级别（4 条）+ Low 级别（4 条）
完整规则列表见 `references/rules.md`

---

## 📄 报告输出

扫描完成后生成 HTML 格式安全审计报告，包含 **10 大章节**：

1. **执行摘要** — 四色统计卡片 + 加密/受限提示
2. **目标信息** — AppID、扫描时间、缓存路径、分包信息
3. **扫描流程回顾** — Phase 0→3 状态
4. **包体详细分析** — 文件名、大小、魔数、版本号、熵值
5. **安全检测覆盖** — 规则 × 包体命中矩阵
6. **发现与评估** — 逐条 finding，含 tag 标签
7. **风险评级** — 总体评级 + 加密受限声明
8. **与其他小程序对比** — 特征对比表
9. **建议与后续行动** — 编号建议表
10. **审计局限性声明** — 方法、范围说明

> 报告使用 print-friendly CSS，浏览器 `Cmd+P` →「另存为 PDF」一键导出。

---

## 🛠️ 文件结构

```
mini-program-sast/
├── README.md                    # 中英双语说明文档
├── README_EN.md                 # 纯英文说明文档
├── SKILL.md                     # 核心 Skill 文件（符合 agentSkills.io 规范）
└── references/
    ├── rules.md                 # 17 条检测规则完整 grep 模式库
    └── audit.sh                 # 一键运行自动化扫描脚本（仅支持 macOS）
```

---

## 🔧 兼容性

| AI 工具 / 平台 | 支持状态 |
|---------------|---------|
| WorkBuddy | ✅ 原生支持 |
| Claude Code | ✅ 支持（agentSkills.io 规范） |
| Cursor | ✅ 支持（skills 目录） |
| 其他 agentSkills.io 兼容工具 | ✅ 通用格式兼容 |
| 命令行（bash） | ✅ `references/audit.sh`（macOS 主要支持） |

### 系统依赖

- `strings` — 系统自带（macOS / Linux）
- `grep` — 系统自带
- `find` — 系统自带
- `bash` — 系统自带
- **微信客户端**（macOS / Windows）— 用于缓存 .wxapkg 包

---

## 🆚 与微信官方工具对比

| 维度 | Mini Program SAST | 微信官方安全扫描 |
|------|-------------------|-----------------|
| **类型** | AI 驱动静态字符串分析 | 自动化扫描服务 |
| **速度** | 1 ~ 3 分钟 | 5 ~ 30 分钟 |
| **需要上传源码** | 不需要（本地缓存包） | 需要（miniprogram-ci） |
| **检测深度** | 字符串模式匹配 + AI 语义 | API 调用链分析 |
| **覆盖范围** | 所有本地缓存过的任意小程序 | 仅限自己有上传权限的小程序 |
| **适用场景** | 快速初筛、第三方审计、应急排查 | 发版前安全门禁 |

---

## 📝 与 Web SAST 的关系

| | Web SAST | Mini Program SAST |
|------|----------|-------------------|
| **目标** | Web 页面（H5 / SPA） | 微信小程序 .wxapkg 包 |
| **输入** | URL | 小程序 AppID |
| **扫描方式** | HTTP 请求 + 源码下载 | 本地缓存文件 strings 提取 |
| **检测规则** | 15 条 Web 前端规则 | 17 条小程序专用规则 |
| **GitHub** | [web-sast](https://github.com/chen-mingsong/web-sast) | [mini-program-sast](https://github.com/chen-mingsong/mini-program-sast) |

---

## ⚠️ 免责声明

本工具仅用于合法授权的安全审计和自查目的。特别注意：

- 可以扫描第三方小程序（只要在电脑微信中打开过），但扫描结果应用于安全研究和个人自查
- 不应利用发现的安全漏洞攻击第三方服务
- 如发现严重漏洞，建议通过微信安全应急响应中心（WSRC）报告
- 遵守当地法律法规

作者不对滥用行为承担任何责任。

---

## 📄 开源协议

MIT License — 详见 [LICENSE](LICENSE) 文件。

---

## 🙋 FAQ

**Q: 为什么需要「在电脑上打开过」这个前置条件？**
A: .wxapkg 由微信客户端在首次打开小程序时下载并缓存到本地。Skill 不通过微信服务器直接下载（微信对包下载做了签名验证），所以需要利用本地缓存。这意味着你只能扫描自己使用过的小程序。

**Q: 扫出漏洞后怎么修？**
A: .wxapkg 是编译产物，不能直接修改本地缓存副本。修复需要在**源码层面**进行（微信开发者工具中修改后重新上传）。Skill 提供的修复代码示例都是源码层面的，可直接拷贝使用。

**Q: 这个扫描会被开发者或微信平台感知吗？**
A: 不会。扫描过程完全离线——只读取本地缓存文件，不向微信服务器或小程序后端发送任何网络请求。

**Q: 第三方小程序（不是我开发的）可以扫描吗？**
A: 技术上可以。但请用于安全研究和自查，不得攻击第三方服务。

**Q: 能分析小程序的云开发（CloudBase）配置吗？**
A: 初始化代码（`wx.cloud.init`）和环境 ID 如果在主包中，可以被提取。但云函数源码存储在云端，无法通过本 Skill 分析。
