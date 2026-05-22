# 最佳实践

常见工作流及如何使用 harness 执行。

## 修复 Bug（存量模式）

### 1. 调用 harness

```
/agentic-dev-harness 在 ./my-project 中修复零数量订单导致库存同步崩溃的问题。
从存量发现开始。不要触碰部署配置。
成功标准：回归测试通过，订单模块测试绿色。
```

### 2. Harness 执行的操作

1. 探测环境（git、可用工具）。
2. 分类为存量模式。
3. 运行只读发现：
   - 找到订单处理入口点。
   - 追踪到库存同步的调用链。
   - 找到 `order/` 模块中的现有测试。
   - 识别风险边界（订单模式、库存队列）。
4. 编写规范：提案、设计、任务、验收场景。
5. 质疑规范（数量为负怎么办？部分退款怎么办？）。
6. 呈现计划并请求批准。
7. 批准后：编写复现零数量崩溃的特征测试。
8. 实施修复（零数量守卫子句）。
9. 运行测试套件，验证修复。
10. 对比规范评审 diff。
11. 记录证据。
12. 归档变更。

### 3. 你需要做什么

- 回答 harness 无法仅从代码中得出的发现问题。
- 在批准门禁处批准或修改规范。
- 提供任何环境特定的上下文（如测试数据库凭证）。

## 启动新服务（新建模式）

### 1. 调用 harness

```
/agentic-dev-harness 在 /path/to/new-service 中启动用户通知服务。
无现有代码。技术栈偏好：TypeScript、PostgreSQL、Redis。
成功标准：提案、设计、任务、首个垂直切片计划。
暂不编写实现。
```

### 2. Harness 执行的操作

1. 探测环境。
2. 分类为新建模式。
3. 引导结构化访谈（产品、设计、工程、运维）。
4. 编写 proposal.md、design.md、tasks.md。
5. 识别最小垂直切片（例如"用户注册时发送邮件"）。
6. 质疑设计。
7. 呈现计划并请求批准。
8. 批准后：生成项目骨架、首个垂直切片测试。
9. 实施切片。
10. 评审、验证、归档。

### 3. 你需要做什么

- 回答访谈问题。
- 如有产品需求文档请提供。
- 在质疑门禁处批准架构决策。

## 重构模块（存量模式）

### 1. 调用 harness

```
/agentic-dev-harness 在 ./my-project 中重构支付模块使用新网关 API。
保持现有公共接口。所有现有测试必须通过。
成功标准：相同测试通过率，无 API 合约变更，性能在 5% 以内。
```

### 2. 关键 harness 行为

- 发现识别支付模块的所有调用方。
- 特征测试在重构前捕获当前行为。
- 规范定义 seam/adapter 策略。
- 实施使用抽象分支。
- 验证运行完整测试套件并比较性能。

## 在现有项目上引导 harness

### 1. 调用 harness

```
/agentic-dev-harness 在 ./my-project 中设置 AI 开发 harness。
不要修改业务代码。只创建 harness 文件。
成功标准：harness-check 通过，harness-lint 干净。
```

### 2. Harness 执行的操作

1. 探测项目：语言、构建系统、测试命令、现有 CLAUDE.md。
2. 分类为 harness 引导。
3. 从 package.json、Makefile 等发现现有构建/测试/lint 命令。
4. 创建或更新包含项目特定指令的 CLAUDE.md。
5. 创建多 agent 规则的 AGENTS.md。
6. 创建 .agentic/ 和 planwithfile/ 目录。
7. 运行 harness-check 和 harness-lint 验证。
8. 记录证据。

### 3. 你需要做什么

- 确认 harness 对你构建/测试命令的理解。
- 根据团队约定审查和自定义 CLAUDE.md。

## 在降级模式下运行 harness

当可选工具（gstack、OpenSpec CLI、Superpowers、GitNexus、gbrain）未安装时，harness 使用内置回退方案：

| 缺失工具 | 降级行为 |
|---|---|
| gstack | 跳过 gstack 特定质疑技能。使用内置四视角质疑。 |
| OpenSpec CLI | 将规范写入 `.agentic/changes/` 而非 `.openspec/`。格式相同。 |
| Superpowers | 手动模拟 TDD、系统调试、小任务。 |
| GitNexus | 使用 `grep -r` 和 `find` 进行代码搜索。 |
| gbrain | 跳过记忆同步。磁盘上的制品就是记忆。 |

Harness 在降级模式下始终可用。它会询问一次是否要安装缺失工具，然后使用可用的任何工具继续。

## 自定义模板

`templates/` 中的所有模板都是起点。为团队自定义：

### CLAUDE.md

添加团队约定、首选技术栈和项目特定规则：

```markdown
# 附加项目规则

## 技术栈
- 后端：Go 1.22、chi router、pgx
- 前端：React 18、TypeScript、Vite
- 数据库：PostgreSQL 16

## 约定
- 错误使用 fmt.Errorf("%w", err) 包装
- HTTP 处理器返回 error，绝不直接写状态码
- 测试使用 testify/assert
```

### AGENTS.md

定义多个 AI agent 如何协作：

```markdown
# Agent 角色
- lead：协调、运行 harness、寻求批准
- reviewer：独立 diff 评审，不实施
- tester：编写和运行测试，绝不编辑源代码
```
