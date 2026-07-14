# NEXT_TASK

更新时间：2026-07-14

## 当前唯一下一任务

创建并确认：

```text
adr/ADR-031-technology-stack.md
```

该任务必须决定：

1. 后端技术栈。
2. 前端技术栈。
3. 数据库及版本。
4. Python、Node 和包管理工具版本下限。
5. 第一阶段是否使用 Redis。
6. 第一阶段是否使用消息队列。
7. 测试框架。
8. 本地开发和部署方式。

在 ADR-031 确认前，不开始正式业务代码开发。

## 宪章约束

执行任务前必须首先读取并遵守：

```text
AI_DEVELOPMENT_CHARTER.md
```

未经项目所有者明确授权，不得扩大修改范围、安装依赖、初始化业务代码、连接真实数据库或外部系统。

## 后续顺序

1. 执行全量需求审计。
2. 补齐组织、商品、订单、履约和集成需求。
3. 初始化后端、前端和 CI 骨架。
4. 实现多租户、组织和数据权限。
5. 实现商品和 SKU 基础。
6. 按独立详细计划实现库存 MVP。
7. 实现文件安全和导入中心。
8. 实现订单、履约和外部 Adapter。

## 必读文档

- `AI_DEVELOPMENT_CHARTER.md`
- `docs/requirements/OMS_DETAILED_REQUIREMENTS.md`
- `docs/superpowers/plans/2026-07-14-oms-master-development-plan.md`
- `CODEX_WORKFLOW.md`
- `CURRENT_STATUS.md`
- `requirements/README.md`
- `adr/README.md`

## Codex 首个任务提示

只执行开发总计划中的 Task 0.1：技术栈评估和 ADR-031。

要求：

1. 首先读取 `AI_DEVELOPMENT_CHARTER.md`。
2. 不创建业务代码。
3. 不初始化后端或前端。
4. 不安装依赖。
5. 不连接数据库或真实 ERP/WMS。
6. 只修改本任务明确允许的技术评估、ADR、状态和下一任务文件。
7. 完成后提交技术评估、ADR、状态文件更新、测试或验证证据、风险、回滚点和唯一下一任务。
