# 概念

ProofRails 的结构以及每个部分存在的原因。

## 架构

```text
proofrails/
├── skill/             # 主 SKILL.md，协调一切
├── scripts/           # POSIX shell 脚本，用于设置和验证
├── templates/         # 用户可自定义的参考模板
├── docs/              # EN/ZH/FR 文档
├── examples/          # 常见场景的完整示例
└── .evidence/         # 实施证据（本项目自身的证据）
```

### 在你的项目中

```text
your-project/
├── CLAUDE.md          # AI agent 项目指令（真相来源）
├── AGENTS.md          # 多 agent 协作规则
├── .proofrails/          # ProofRails 工作空间目录（仅用于 bootstrap 模式回退）
│   ├── changes/       # bootstrap 模式下的变更草稿（openspec/ 尚未初始化时使用）
│   └── runs/          # 评审报告和验证证据
├── planwithfile/      # 活动变更的工作目录
│   └── <id>/
│       ├── brief.md
│       ├── findings.md
│       ├── capability-map.md   # 起草任何变更规范前必备
│       └── …
├── .evidence/         # 不可变证据记录
└── openspec/          # 必装：Fission-AI/OpenSpec 布局
    ├── changes/<id>/  # proposal、design、tasks、delta 规范
    └── specs/<cap>/   # 能力基线规范（SSOT）
```

## 状态机

ProofRails 将 AI 辅助开发建模为 11 阶段状态机：

```
文档接收 -> 探测 -> 模式检测 -> 发现 -> 规范 -> 质疑
  -> 批准 -> 实施 -> 评审 -> 验证 -> 归档
```

每个阶段在磁盘上产出制品。不跳过任何阶段。阶段可以循环回退（例如，评审发现规范偏差 -> 返回规范阶段）。

### 阶段制品

| 阶段 | 制品 | 可逆？ |
|---|---|---|
| 接收 | `planwithfile/<id>/brief.md` | 是，随时更新 |
| 探测 | （终端输出，工具分类表） | N/A |
| 模式 | `planwithfile/<id>/mode.md` | 是，需确认 |
| 发现 | `planwithfile/<id>/findings.md` | 是，建议追加 |
| 能力地图 | `planwithfile/<id>/capability-map.md` | 是，规范起草前 |
| 规范 | `openspec/changes/<id>/{proposal,design,tasks}.md`、delta `specs/<cap>/spec.md`，本次新建的 `openspec/specs/<cap>/spec.md` 基线 | 是，批准前 |
| 质疑 | `planwithfile/<id>/decisions.md` | 是，需说明理由 |
| 批准 | （用户确认，无文件） | 否 — 门禁已过 |
| 实施 | `planwithfile/<id>/progress.md`、`evidence.md` | 是，通过 git revert |
| 评审 | `.proofrails/runs/<id>/review-report.md` | 是，重新评审 |
| 验证 | `evidence.md`（更新） | 否 — 证据不可变 |
| 归档 | `openspec/changes/archive/<dated-id>/`（Fission-AI）或 `.proofrails/changes/<id>/`（bootstrap 回退） | 否 — 终态 |

## 模式

### 新建 (Greenfield)

新项目、新仓库、新模块、新功能。ProofRails 强制要求：

- 规范编写前需要产品/设计/技术文档或结构化访谈。
- 架构决策记录在 design.md 中。
- 垂直切片规划 — 首先要构建的最小完整功能是什么？

### 存量 (Brownfield)

Bug 修复、重构、性能优化、迁移、遗留系统现代化。ProofRails 强制要求：

- 任何编辑前的只读发现。
- 变更前的特征测试或黄金证据。
- 影响范围分析 — 还有哪些可能出问题？
- 最小变更面 — 只触碰必须改的。

### 混合 (Hybrid)

现有项目中的新子系统。结合：

- 新子系统的新建规范。
- 现有集成点的存量发现。
- 新旧之间的明确接口合约。

### ProofRails 引导 (ProofRails-bootstrap)

设置 ProofRails。创建：

- 项目指令文件（CLAUDE.md、AGENTS.md）。
- 目录结构（.proofrails、planwithfile、.evidence）。
- 不触碰业务代码。

## 工具和回退方案

ProofRails 检测可用工具并适配。三件套必装；其余均为可选项。

| 工具 | 状态 | 用途 | 缺失时 |
|---|---|---|---|
| git | 必需 | 版本控制、diff、log | 退出 |
| OpenSpec（Fission-AI） | **必装** | 规范生命周期（`openspec/changes/`、`openspec/specs/`） | BLOCKED — `npm install -g @fission-ai/openspec@latest` |
| gstack | **必装** | 规划/评审/QA/质疑技能 | BLOCKED — 按 gstack 文档安装 |
| Superpowers | **必装** | TDD 和执行纪律 | BLOCKED — 安装 Superpowers 技能包 |
| CodeGraph | 可选 | 代码图谱（`codegraph` CLI 与 `codegraph_*` MCP 工具） | `grep -r` / `find` |
| gbrain | 可选 | 持久记忆 | 无需也能正常工作 |

## 风险边界

风险边界定义了修改前需要明确用户批准的合约。它们记录在 `CLAUDE.md` 的 **Risk boundaries** 部分，包括：

```markdown
# 风险边界

## 公共 API
- `POST /api/v1/users` — 外部调用方合约
- `GET /api/v1/orders` — 移动端依赖

## 数据模式
- `users` 表 — 与数据分析管道共享
- `orders` 表 — 适用合规保留策略

## 消息队列
- `order.events` 主题 — 被计费服务消费
- `user.actions` 主题 — 被推荐引擎消费

## 认证
- OAuth2 令牌验证 — 必须保持向后兼容
- Session cookie 格式 — 与旧版前端共享

## 部署
- `docker-compose.prod.yml` — 生产环境拓扑
- `terraform/` — 基础设施即代码
```

## 证据

证据是 ProofRails 对"信任但要验证"的回答。每个验证门禁产出或引用证据：

- **构建输出** — 构建命令的 stdout/stderr。
- **测试结果** — 通过/失败计数、覆盖率报告。
- **Lint/类型检查输出** — 零警告或有文档记录的例外。
- **运行时冒烟测试** — 应用启动、API 返回 200、关键流程正常。
- **手动证据** — 截图、日志摘录、用户确认（当无法自动化时）。

证据写入 `.evidence/`（不可变，仅追加）或 `planwithfile/<id>/evidence.md`（活动变更）。

## 归档

归档是**终态**。变更归档后：

- 其规范和证据被冻结。
- 不要原地修补同一变更。
- 后续变更是具有自己 change-id 的**新**变更。
- 这防止范围蔓延，保持每个变更的证据链完整。
