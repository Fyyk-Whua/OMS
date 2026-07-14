# Codex + GitHub 协作规范

更新时间：2026-07-14

> 本规范受 `AI_DEVELOPMENT_CHARTER.md` 约束。发生冲突时，项目所有者的明确指令和 AI 开发宪章优先。

## 1. 核心原则

1. GitHub 是需求、设计、代码、测试和状态的事实来源。
2. Codex 每次只执行一个边界清晰的小任务。
3. 所有任务必须关联 REQ；重大技术决策必须关联 ADR。
4. 先写测试，再实现最小代码，再执行回归测试。
5. 每次完成任务必须更新项目状态文档。
6. 不允许为了“顺手优化”修改任务范围之外的模块。
7. 未获得明确授权时，不执行 GitHub 写操作、高风险数据操作或外部系统真实调用。

## 2. 每次会话开始前

Codex 必须依次读取：

1. `AI_DEVELOPMENT_CHARTER.md`。
2. `AGENTS.md`（创建后）。
3. `README.md`。
4. `CURRENT_STATUS.md`。
5. `NEXT_TASK.md`。
6. 当前任务对应的需求文件。
7. 当前任务对应的 ADR。
8. 当前任务对应的 `docs/superpowers/plans/` 计划。
9. 最近相关提交和当前工作区状态。

如果宪章、需求、ADR 和计划存在冲突，停止编码并报告冲突，不得自行选择一种解释。

## 3. 分支和提交

分支命名：

```text
feature/<requirement-id>-<short-name>
fix/<issue-id>-<short-name>
docs/<topic>
chore/<topic>
```

提交信息：

```text
feat(inventory): add atomic reservation [REQ-INVENTORY-043]
fix(import): reject invalid page size [REQ-IMPORT-004]
docs: update ERP unknown-result decision [ADR-027]
```

一个提交只包含一个可独立解释和回滚的改动。

## 4. 标准开发循环

1. 确认任务授权和允许修改范围。
2. 新建功能分支。
3. 编写失败测试。
4. 运行测试，确认失败原因符合预期。
5. 编写通过测试的最小实现。
6. 运行局部测试。
7. 运行全量回归。
8. 检查格式、类型和静态分析。
9. 更新需求追踪和状态文档。
10. 提交。
11. 推送并创建 PR。

## 5. PR 必须说明

- 对应需求编号。
- 对应 ADR。
- 修改范围。
- 测试命令和结果。
- 数据库迁移影响。
- API 影响。
- 安全影响。
- 回滚方式。
- 未完成项。
- 是否符合 `AI_DEVELOPMENT_CHARTER.md`。

## 6. 禁止事项

- 不得违反或绕过 `AI_DEVELOPMENT_CHARTER.md`。
- 不得直接把网络超时处理为 ERP 失败。
- 不得直接修改库存余额而不产生流水。
- 不得物理删除库存占用和已完成流水。
- 不得把现货和预售合并成物理库存。
- 不得跳过后端数据权限校验。
- 不得让业务模块直接解析未经安全检测的文件。
- 不得提交 `.env`、密码、Token、私钥或客户数据。
- 不得连接真实 ERP/WMS，除非有单独批准的集成任务。
- 不得扩大用户授权的文件、目录或操作范围。
- 不得在测试未执行时声称任务完成。

## 7. 完成报告格式

Codex 每次完成后输出：

```markdown
## 任务目标
- ...

## 完成内容
- ...

## 修改文件
- ...

## 未修改范围
- ...

## 测试结果
- 命令：...
- 结果：PASS/FAIL

## 需求与 ADR
- REQ：...
- ADR：...

## 风险和限制
- ...

## 未完成内容
- ...

## 回滚点
- ...

## 唯一下一任务
- ...
```

## 8. 中断恢复

任务中断后不得凭记忆继续。重新读取：

- `AI_DEVELOPMENT_CHARTER.md`。
- 当前分支的 `git status`。
- 最近提交。
- 当前计划中的勾选状态。
- `CURRENT_STATUS.md`。
- `NEXT_TASK.md`。
- 未完成测试结果。

确认工作区、任务授权和文档一致后再继续。
