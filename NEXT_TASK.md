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

- `docs/requirements/OMS_DETAILED_REQUIREMENTS.md`
- `docs/superpowers/plans/2026-07-14-oms-master-development-plan.md`
- `CODEX_WORKFLOW.md`
- `CURRENT_STATUS.md`
- `requirements/README.md`
- `adr/README.md`

## Codex 首个任务提示

只执行开发总计划中的 Task 0.1：技术栈评估和 ADR-031。不要创建业务代码，不要初始化后端或前端，不要引入依赖。完成后提交技术评估、ADR、状态文件更新和下一任务。
