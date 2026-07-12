# OMS

面向多企业、多店铺、多库存组织的订单管理与运营平台。

项目聚焦订单审核、库存占用、预售策略、渠道库存、履约编排、ERP/WMS 集成、文件安全导入和业务审计。OMS 负责订单运营与履约协调，不替代 ERP 的库存权威能力，也不扩展为完整 WMS。

## 当前阶段

当前处于需求分析与架构设计阶段，已完成库存中心第一阶段需求冻结，包括：

- OMS 独立库存模式
- ERP 库存模式
- 预售策略与到货监测
- 安全库存、渠道库存、共享库存池、冻结库存
- 库存流水、审计和长期保留
- 可售库存统一计算
- 库存占用、释放、超时和补偿机制
- 文件安全网关与统一数据导入中心

## 文档入口

1. [CURRENT_STATUS.md](CURRENT_STATUS.md)
2. [NEXT_TASK.md](NEXT_TASK.md)
3. [需求索引](requirements/README.md)
4. [架构决策索引](adr/README.md)
5. [库存中心设计](module-design/inventory/README.md)
6. [会话决策记录](conversation/2026-07-12-requirements-session.md)
7. [需求追踪矩阵](traceability/REQUIREMENT_TRACEABILITY.md)

## 协作原则

- GitHub 是项目事实来源。
- 每次只推进一个明确的小任务。
- 已确认需求不得遗漏。
- 被否定或弃用的需求不得删除，必须标记为 `REJECTED` 或 `DEPRECATED`。
- 所有实现应形成 `REQ -> Design -> ADR -> Task -> Code -> Test` 的追踪链。
