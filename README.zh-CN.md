# ProofRails

**10 分钟构建 AI 开发护栏。**

一个单入口技能，将 AI 辅助编程转变为可复现的工程工作流。指向任何项目——新建或存量——即可获得规范、计划、风险边界、验证门禁和证据跟踪，无需手动组装十几个工具。

[English](./README.md) | [中文](./README.zh-CN.md) | [Français](./README.fr.md)

---

## 为什么存在

AI 编程助手功能强大但不可预测。它们跳过发现阶段、未经批准就编辑、未通过测试就合并、不留下任何证据。团队最终花费大量时间编写临时规则、复制 CLAUDE.md 模板、手动执行门禁——这些工作与实际产品毫无关系。

ProofRails 通过一条命令为你提供这套基础设施。它不是一个框架，不是 SaaS，也不是专有平台。它是一个**技能**——一个在你现有 AI 编程工具中运行的结构化工作流——加上一组脚本和模板，让你的项目保持在已知的良好状态。

## 10 分钟内你会得到什么

```text
your-project/
├── CLAUDE.md                    # AI agent 项目指令
├── AGENTS.md                    # 多 agent 协作规则
├── .proofrails/
│   ├── changes/                 # 变更提案和设计文档
│   └── runs/                    # 评审报告和证据
├── planwithfile/                # 活动变更的工作目录
└── .evidence/                   # 不可变证据记录
```

## 工作原理

```
文档接收 -> 探测 -> 模式检测 -> 发现 -> 规范 -> 质疑 -> 批准 -> 实施 -> 评审 -> 验证 -> 归档
```

1. **文档接收** — 收集目标、约束、成功标准。只问缺失的内容。
2. **探测** — 检测可用工具（git、OpenSpec、gstack 等）。映射降级模式回退方案。
3. **模式检测** — 分类工作类型：新建、存量、混合或 ProofRails 引导。
4. **发现** — 只读探索。找到入口点、合约、风险。暂不编辑。
5. **规范** — 编写提案、设计、任务和验收场景（兼容 OpenSpec）。
6. **质疑** — 从业务、风险、实施和历史角度对规范进行压力测试。
7. **批准** — 呈现范围、风险和计划。在任何源代码编辑之前要求明确批准。
8. **实施** — 以小型、可评审的切片方式实施，先写测试。
9. **评审** — 与规范对比 diff。标记偏差。要求解决。
10. **验证** — 构建、测试、lint、冒烟测试。无证据则不算完成。
11. **归档** — 更新持久文档。记录经验教训。此变更的最终状态。

## 快速开始

### 前置条件

- git（任意较新版本）
- POSIX 兼容 shell（bash、zsh、dash）
- 支持技能的 AI 编程工具（Claude Code，或任何支持 Markdown 技能加载的工具）

### 安装

```bash
# 将 ProofRails 克隆到目标项目之外
git clone https://github.com/proudCrisa/proofrails.git /tmp/proofrails

# Claude Code
mkdir -p ~/.claude/skills/proofrails
cp /tmp/proofrails/skill/SKILL.md ~/.claude/skills/proofrails/SKILL.md

# OpenClaw / Hermes 风格 Markdown 技能
mkdir -p ~/.openclaw/workspace/skills/proofrails
cp /tmp/proofrails/skill/SKILL.md ~/.openclaw/workspace/skills/proofrails/SKILL.md

# 其他 Markdown 技能型代理：将 /tmp/proofrails/skill/SKILL.md 复制到该工具的技能目录。
```

### 引导项目

```bash
# 从克隆的 ProofRails 仓库显式指定目标项目：
/tmp/proofrails/scripts/proofrails-bootstrap /path/to/your-project

# 或者先试运行查看将要创建的内容：
/tmp/proofrails/scripts/proofrails-bootstrap --dry-run /path/to/your-project

# 检查 ProofRails 健康状态：
/tmp/proofrails/scripts/proofrails-check --target /path/to/your-project

# Lint 检查 ProofRails 仓库自身：
/tmp/proofrails/scripts/proofrails-lint
```

ProofRails 脚本是唯一推荐入口：`proofrails-bootstrap`、`proofrails-check` 和 `proofrails-lint`。仅复制 skill 会安装 AI 工作流入口，但不会安装项目脚本；除非你有意复制脚本，否则继续使用克隆的 ProofRails 仓库中的脚本。

Bootstrap 只创建 ProofRails 工作流文件；不会编辑应用代码或源代码。

### 使用技能

```
/proofrails 在 ./my-project 中修复支付超时 bug。
从存量发现开始。编辑代码前等待批准。
成功标准：回归测试通过，模块测试绿色。
```

请使用 `/proofrails` 作为标准 skill 调用入口。

## 模式

| 模式 | 何时使用 | 关键约束 |
|---|---|---|
| **新建** (Greenfield) | 新项目、新仓库、新模块 | 需要产品/设计/技术文档或访谈 |
| **存量** (Brownfield) | Bug 修复、重构、迁移、性能优化 | 编辑前先发现，特征测试 |
| **混合** (Hybrid) | 现有项目中的新子系统 | 结合新建规范 + 存量发现 |
| **ProofRails 引导** | 设置 ProofRails | 不触碰业务代码 |

## 兼容性

ProofRails 适用于任何能读取 Markdown 技能文件的 AI 编程工具。它要求一组必装三件套，外加若干可选集成。

### 必装（任何非 bootstrap 运行）

| 集成 | 用途 | 安装 |
|---|---|---|
| [OpenSpec](https://github.com/Fission-AI/OpenSpec)（Fission-AI） | 规范生命周期 CLI（`openspec/changes/`、`openspec/specs/`） | `npm install -g @fission-ai/openspec@latest` |
| [gstack](https://github.com/gstack) | 规划、挑战、评审、QA、上线技能 | 按仓库说明 |
| Superpowers | TDD 和执行纪律 | 把 Superpowers 技能包安装到 `~/.claude/skills/superpowers/` |

任一必装项缺失，ProofRails 会以 `NEEDS_CONTEXT` 停止并打印安装命令。唯一例外是 `proofrails-bootstrap` 模式，它可以在三件套未安装的全新仓库上运行。

### 可选

| 集成 | 用途 | 没有它 |
|---|---|---|
| CodeGraph | 代码图谱（`codegraph` CLI 与 `codegraph_*` MCP 工具） | grep/find 回退 |
| gbrain | 持久记忆和代码搜索 | 无需它也能工作 |

**ProofRails 核心依赖：git + POSIX shell。** 三件套用于代码工作；CodeGraph 与 gbrain 仅为可选项。

## 安全原则

1. **批准前不编辑。** 发现和规范编写始终允许。源代码更改、依赖更改和 CI/CD 更改需要明确用户批准。
2. **证据优于声明。** 每个验证门禁需要构建输出、测试结果或明确用户接受的例外。
3. **高风险合约需要确认。** 公共 API、数据模式、消息队列、认证、部署和生产配置在修改前需要用户批准。
4. **归档是终态。** 变更归档后，不要原地修补。开始新的变更。
5. **单入口。** 用户调用一个技能。ProofRails 按需路由到子技能。用户永远不需要手动选择规划、评审、QA 或发布技能。

## 示例

- [最小化 ProofRails 设置](./examples/minimal/README.md) — 在空仓库上引导
- [存量 Bug 修复](./examples/brownfield/README.md) — 在现有项目上的发现、规范、修复、验证
- [新建服务](./examples/greenfield/README.md) — 从零开始的完整 ProofRails 新服务

## 文档

| 文档 | EN | ZH | FR |
|---|---|---|---|
| 快速开始 | [en](./docs/quickstart.md) | [zh](./docs/quickstart.zh-CN.md) | [fr](./docs/quickstart.fr.md) |
| 概念 | [en](./docs/concepts.md) | [zh](./docs/concepts.zh-CN.md) | [fr](./docs/concepts.fr.md) |
| 最佳实践 | [en](./docs/recipes.md) | [zh](./docs/recipes.zh-CN.md) | [fr](./docs/recipes.fr.md) |
| 故障排除 | [en](./docs/troubleshooting.md) | [zh](./docs/troubleshooting.zh-CN.md) | [fr](./docs/troubleshooting.fr.md) |

## 参与贡献

欢迎贡献。提交 PR 前：

1. 运行 `./scripts/proofrails-lint` 并修复所有问题。
2. 运行 `./scripts/proofrails-check` 并验证所有检查通过。
3. 为任何新行为在 `.evidence/` 中添加证据。
4. 如果更改工作流，更新文档。

## 许可证

Apache License 2.0 — 详见 [LICENSE](./LICENSE)。

---

**为追求 AI 速度与工程严谨性的团队而构建。**
