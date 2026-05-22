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
├── .agentic/          # ProofRails 工作空间目录
│   ├── changes/       # 变更提案：proposal、design、tasks、specs
│   └── runs/          # 评审报告和验证证据
├── planwithfile/      # 活动变更的工作目录
├── .evidence/         # 不可变证据记录
└── .openspec/         # 可选：OpenSpec 管理的规范生命周期
```

## 状态机

ProofRails 将 AI 辅助开发建模为 12 阶段状态机：

```
接收 -> 探测 -> 模式 -> 发现 -> 规范 -> 质疑
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
| 规范 | `proposal.md`、`design.md`、`tasks.md`、`specs/` | 是，批准前 |
| 质疑 | `planwithfile/<id>/decisions.md` | 是，需说明理由 |
| 批准 | （用户确认，无文件） | 否 — 门禁已过 |
| 实施 | `planwithfile/<id>/progress.md`、`evidence.md` | 是，通过 git revert |
| 评审 | `.agentic/runs/<id>/review-report.md` | 是，重新评审 |
| 验证 | `evidence.md`（更新） | 否 — 证据不可变 |
| 归档 | `.openspec/` 或 `.agentic/changes/<id>/` | 否 — 终态 |

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
- 目录结构（.agentic、planwithfile、.evidence）。
- 不触碰业务代码。

## 工具和回退方案

ProofRails 检测可用工具并适配：

| 检测到的工具 | 用途 | 缺失时的回退方案 |
|---|---|---|
| git | 版本控制、diff、log | 必需 — 无回退 |
| gstack | 规划/评审/QA/质疑技能 | 内置工作流门禁 |
| OpenSpec CLI | 规范生命周期管理 | `.agentic/changes/` 目录 |
| Superpowers | TDD 和执行纪律 | ProofRails 中模拟的规则 |
| GitNexus | 代码图谱查询 | `grep -r` / `find` |
| gbrain | 持久记忆 | 无需也能正常工作 |

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
