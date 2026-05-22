# 快速开始

在 10 分钟内为你的项目设置 ProofRails。

## 第一步：验证前置条件

```bash
git --version          # >= 2.0
echo $SHELL            # bash、zsh 或 dash
```

## 第二步：安装 ProofRails

**选项 A：克隆仓库**

```bash
git clone https://github.com/proofrails/proofrails.git /tmp/proofrails
cp -r /tmp/proofrails/{skill,scripts,templates,docs,examples} ./
```

**选项 B：仅复制技能文件**

```bash
# Claude Code
mkdir -p ~/.claude/skills/proofrails
cp proofrails/skill/SKILL.md ~/.claude/skills/proofrails/SKILL.md

# OpenClaw / Hermes 风格 Markdown 技能
mkdir -p ~/.openclaw/workspace/skills/proofrails
cp proofrails/skill/SKILL.md ~/.openclaw/workspace/skills/proofrails/SKILL.md
```

其他 Markdown 技能型代理：将 `skill/SKILL.md` 复制到该工具的技能目录。

**选项 C：直接使用引导脚本**

如果你已经克隆了此仓库，直接运行引导脚本：

```bash
./scripts/proofrails-bootstrap
```

## 第三步：引导你的项目

```bash
# 先试运行——查看将要创建的内容而不修改文件
./scripts/proofrails-bootstrap --dry-run

# 正式执行
./scripts/proofrails-bootstrap
```

预期输出：

```
[proofrails] Target: /path/to/your-project
[proofrails] 已创建 .proofrails/changes/
[proofrails] 已创建 .proofrails/runs/
[proofrails] 已创建 planwithfile/
[proofrails] 已创建 .evidence/
[proofrails] 已写入 AGENTS.md（来自模板）
[proofrails] 已写入 CLAUDE.md（来自模板）
[proofrails] 完成。运行 ./scripts/proofrails-check 验证。
```

## 第四步：验证

```bash
./scripts/proofrails-check
```

预期输出：

```
[proofrails-check] 验证已安装的 ProofRails...
[proofrails-check] 目标: /path/to/your-project

[proofrails-check]   .proofrails/changes/: 正常
[proofrails-check]   .proofrails/runs/: 正常
[proofrails-check]   planwithfile/: 正常
[proofrails-check]   .evidence/: 正常
[proofrails-check]   CLAUDE.md: 正常
[proofrails-check]   AGENTS.md: 正常

[proofrails-check] 所有目标检查通过。
```

## 第五步：使用技能

在 AI 编程工具中调用技能：

```
/proofrails 在 ./ 中修复登录超时 bug。从存量发现开始。编辑代码前等待批准。成功标准：回归测试通过。
```

## 第六步：定期 Lint

```bash
./scripts/proofrails-lint
```

扫描常见问题：缺失必需文件和禁止术语。

## 下一步

- 阅读[概念](./concepts.zh-CN.md)了解架构。
- 阅读[最佳实践](./recipes.zh-CN.md)了解常见工作流。
- 如有问题查阅[故障排除](./troubleshooting.zh-CN.md)。
