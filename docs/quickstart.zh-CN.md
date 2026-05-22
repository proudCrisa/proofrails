# 快速开始

在 10 分钟内为你的项目设置 harness。

## 第一步：验证前置条件

```bash
git --version          # >= 2.0
echo $SHELL            # bash、zsh 或 dash
```

## 第二步：安装 harness

**选项 A：克隆仓库**

```bash
git clone https://github.com/agentic-dev-harness/harness.git /tmp/harness
cp -r /tmp/harness/{skill,scripts,templates,docs,examples} ./
```

**选项 B：仅复制技能文件**

```bash
# Claude Code
mkdir -p ~/.claude/skills/agentic-dev-harness
cp harness/skill/SKILL.md ~/.claude/skills/agentic-dev-harness/SKILL.md

# OpenClaw / Hermes 风格 Markdown 技能
mkdir -p ~/.openclaw/workspace/skills/agentic-dev-harness
cp harness/skill/SKILL.md ~/.openclaw/workspace/skills/agentic-dev-harness/SKILL.md
```

其他 Markdown 技能型代理：将 `skill/SKILL.md` 复制到该工具的技能目录。

**选项 C：直接使用引导脚本**

如果你已经克隆了此仓库，直接运行引导脚本：

```bash
./scripts/bootstrap-harness
```

## 第三步：引导你的项目

```bash
# 先试运行——查看将要创建的内容而不修改文件
./scripts/bootstrap-harness --dry-run

# 正式执行
./scripts/bootstrap-harness
```

预期输出：

```
[harness] Target: /path/to/your-project
[harness] 已创建 .agentic/changes/
[harness] 已创建 .agentic/runs/
[harness] 已创建 planwithfile/
[harness] 已创建 .evidence/
[harness] 已写入 AGENTS.md（来自模板）
[harness] 已写入 CLAUDE.md（来自模板）
[harness] 完成。运行 ./scripts/harness-check 验证。
```

## 第四步：验证

```bash
./scripts/harness-check
```

预期输出：

```
[harness-check] 验证已安装的 harness...
[harness-check] 目标: /path/to/your-project

[harness-check]   .agentic/changes/: 正常
[harness-check]   .agentic/runs/: 正常
[harness-check]   planwithfile/: 正常
[harness-check]   .evidence/: 正常
[harness-check]   CLAUDE.md: 正常
[harness-check]   AGENTS.md: 正常

[harness-check] 所有目标检查通过。
```

## 第五步：使用技能

在 AI 编程工具中调用技能：

```
/agentic-dev-harness 在 ./ 中修复登录超时 bug。从存量发现开始。编辑代码前等待批准。成功标准：回归测试通过。
```

## 第六步：定期 Lint

```bash
./scripts/harness-lint
```

扫描常见问题：缺失必需文件和禁止术语。

## 下一步

- 阅读[概念](./concepts.zh-CN.md)了解架构。
- 阅读[最佳实践](./recipes.zh-CN.md)了解常见工作流。
- 如有问题查阅[故障排除](./troubleshooting.zh-CN.md)。
