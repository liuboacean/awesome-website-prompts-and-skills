# Website Skeleton Skill — EdgeOne Pages 全栈网站骨架

> **版本：** 2.1.0 · **日期：** 2026-04-26
> **作者：** 刘博（liuboacean）
> **Demo：** https://geek-mall-demo-4qaxvmeh.edgeone.cool

---

## 这个 Skill 做什么

用户只要说一句需求，AI Agent 会：

1. **确认场景** — 电商 / AI 助手 / SaaS 管理后台（三选一，或自由组合模块）
2. **生成完整代码** — Next.js 前端 + Edge Functions（KV）+ Cloud Functions（MySQL/支付）+ Platform Middleware
3. **自动集成安全设计** — 支付幂等原子锁、订单超卖防护、RT 并发安全
4. **部署上线** — 对接 `edgeone-pages-deploy` Skill，一键部署到 EdgeOne Pages 全球 CDN

---

## 三大场景模板

| 模板 | 适用场景 | 包含模块 |
|------|---------|---------|
| **电商（E-commerce）** | 登录注册、购物车、微信/支付宝支付、订单管理 | Auth + Cart + Payment + Orders + Admin |
| **AI 助手（AI Assistant）** | 登录注册、AI 对话（SSE 流式）、访客留言 | Auth + AI Chat + SSE Streaming |
| **SaaS 管理后台（SaaS Admin）** | 登录注册、RBAC 权限、数据看板、订阅支付 | Auth + Admin RBAC + Stats + Subscription |

---

## 目录结构

```
website-skeleton-skill/
├── README.md                                    ← 你正在看的文件
└── skills/
    └── edgeone-pages-website-skeleton/
        ├── SKILL.md                            ← Skill 入口（frontmatter + 主流程）
        └── references/                         ← 按需加载的详细文档
            ├── auth-module.md                  ← JWT / KV 会话 / bcrypt / RT 轮换
            ├── payment-module.md               ← 支付幂等 / 微信+支付宝 / 回调处理
            ├── ai-chat-module.md               ← SSE 流式 / 历史上下文 / LLM 集成
            ├── kv-storage.md                   ← KV 分层查询策略
            └── templates.md                    ← 三场景模板详细规格
```

---

## 核心安全设计（P0，全部已实现）

| 安全措施 | 实现方案 |
|---------|---------|
| 支付幂等原子锁 | Edge `putIfNotExists` 24h TTL < 微信重试窗口 72h |
| 订单超卖防护 | `SELECT FOR UPDATE` + 乐观锁 + MySQL CHECK 约束 |
| RT 并发安全 | KV version 乐观锁（409 → 客户端重试） |
| 金额安全 | 服务端 MySQL 唯一价格来源，前端不传 price |
| 支付回调隔离 | Platform Middleware 直接 return，跳过 JWT 校验 |
| 密码哈希 | bcrypt cost=12（Cloud Functions 中） |

---

## 与 EdgeOne Pages 深度集成

| 平台能力 | 使用场景 |
|---------|---------|
| **KV Storage** | Auth Session、AI History、幂等锁、购物车（登录态） |
| **Edge Functions** | JWT 校验、KV 读写、限流、商品列表（无密钥） |
| **Cloud Functions** | bcrypt、MySQL 事务、微信/支付宝支付、SSE AI |
| **Platform Middleware** | CORS、CSP、IP 白名单、支付回调路径 |
| **edgeone deploy** | 自动构建 + 上传 + 部署 + 返回 CDN URL |

---

## 安装方式

### 方式一：自然语言安装（推荐）

在 CodeBuddy / Claude Code / Cursor 中：

> 帮我安装这个 skill：https://github.com/liuboacean/website-skeleton-skill

### 方式二：手动安装

把 `skills/edgeone-pages-website-skeleton/` 复制到：
- **CodeBuddy**：`~/.codebuddy/skills/`
- **Claude Code**：`~/.claude/skills/`
- **Cursor**：项目 `.cursor/rules/` 或全局 skills 目录

---

## 触发示例

- "帮我建一个电商网站"
- "做一个带登录的电商站，包含购物车和微信支付宝支付"
- "帮我做一个AI客服站"
- "做个管理后台，带RBAC权限"
- "建一个全栈网站"
- "Build me an e-commerce website"
- "Create an AI chatbot site with login"

---

## 前置依赖

- Node.js ≥ 18
- npm / pnpm / yarn
- **部署阶段**：EdgeOne Pages 账号（免费注册）

---

## License

MIT
