# 故障排除

常见问题及解决方法。

## `proofrails-check` 报告缺失文件

**症状：**

```
[proofrails-check]   CLAUDE.md: 缺失
[proofrails-check]   AGENTS.md: 缺失
```

**修复：**

运行引导脚本创建缺失文件：

```bash
./scripts/proofrails-bootstrap
```

已存在的文件不会被覆盖，除非你传入 `--force`。

## `proofrails-lint` 报告禁止术语

**症状：**

```
[proofrails-lint] 禁止：在 docs/concepts.zh-CN.md:42 中发现禁止术语
```

**修复：**

从标记的文件中删除禁止术语。proofrails-lint 脚本检查内置的受限引用列表，这些引用不得出现在开源仓库中。

## 引导脚本覆盖了我的文件

**症状：**

你运行了 `./scripts/proofrails-bootstrap --force`，自定义的 CLAUDE.md 被替换了。

**修复：**

默认情况下，引导脚本**不会**覆盖已存在的文件。只有 `--force` 会。如果需要选择性更新：

1. 先运行 `--dry-run` 查看将要更改的内容。
2. 备份自定义文件。
3. 使用 `--force` 运行后重新应用自定义内容。
4. 考虑将自定义内容维护在单独的文件中，由 CLAUDE.md 引用。

## ProofRails 创建了我不需要的目录

**症状：**

ProofRails 创建了 `.proofrails/`，但你的团队使用 OpenSpec。

**修复：**

`.proofrails/` 是仅用于 bootstrap 的回退目录。ProofRails 在检测到 Fission-AI/OpenSpec 已安装并初始化时会优先使用它。请安装并初始化：

```bash
npm install -g @fission-ai/openspec@latest
openspec init
```

请确认 PATH 上的 `openspec` 是 Fission-AI 那一份（`openspec --help` 应该包含 "spec-driven" 或 "Fission"）。有些机器上存在另一个同名但无关的 `openspec` 工具——请先重命名或卸载它，再运行 `openspec init`。

`openspec init` 之后，ProofRails 会把变更草案写入 `openspec/changes/<id>/`，把能力基线写入 `openspec/specs/<cap>/`。请保留 `.proofrails/runs/`，这样 `proofrails-check` 才能验证已安装的 ProofRails——它仍然是本地运行/证据目录。

## AI 工具无法识别技能

**症状：**

输入 `/proofrails` 没有任何反应。

**修复：**

1. 按你的工具验证技能文件是否位于正确路径：
   ```bash
   # Claude Code
   ls ~/.claude/skills/proofrails/SKILL.md

   # OpenClaw / Hermes 风格 Markdown 技能
   ls ~/.openclaw/workspace/skills/proofrails/SKILL.md
   ```

2. 对于 Claude Code，检查设置中是否启用了技能。

3. 对于其他 AI 工具，查阅其技能加载文档。ProofRails 技能是带 YAML frontmatter 的标准 Markdown 文件——任何支持 Markdown 技能的工具都应该能识别。

4. 作为回退方案，你可以将 `skill/SKILL.md` 的内容直接粘贴到 AI 工具的指令中。

## 在大型代码库上发现耗时太长

**症状：**

ProofRails 在存量发现期间读取了数百个文件。

**修复：**

ProofRails 尽量提高效率，但大型代码库需要指引：

1. 在调用中提供更具体的入口点：
   ```
   /proofrails 在 ./my-project 中修复支付超时。
   入口点：src/payment/processor.ts。现有测试：src/payment/__tests__/。
   ```

2. 如果你的项目有 CLAUDE.md 或 AGENTS.md，ProofRails 会首先阅读它以了解项目结构。

3. 对于超大型 monorepo，考虑将 ProofRails 限定在特定模块或服务中运行。

## 批准门禁被跳过

**症状：**

AI 未经批准就编辑了源代码。

**修复：**

这是 AI 行为问题，而非 ProofRails 问题。ProofRails SKILL.md 明确要求源代码编辑前必须批准。如果 AI 跳过了：

1. 提醒它："你必须在编辑源代码之前呈现批准门禁。"
2. 在 CLAUDE.md 中添加更强的项目规则：
   ```markdown
   # 硬性规则
   绝不在没有明确用户批准的情况下编辑源代码。
   先呈现范围、风险和计划。
   ```
3. 使用 AI 工具的权限系统要求文件写入需要批准。

## 证据目录越来越满

**症状：**

`.evidence/` 中有许多大文件。

**修复：**

证据文件不可变，但可以归档旧文件：

```bash
# 归档 90 天前的证据
find .evidence/ -type f -mtime +90 -exec mv {} .evidence/archive/ \;

# 或使用 git 仅跟踪最近的证据
echo ".evidence/archive/" >> .gitignore
```

`.evidence/` 目录用于当前和最近的证据。旧证据仍应存在于 git 历史记录和归档变更中。
