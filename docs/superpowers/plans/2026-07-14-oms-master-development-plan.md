# OMS Master Development Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 以 GitHub 为事实来源，通过 Codex 小步、可测试、可回滚地完成 OMS 从需求基线、架构骨架、库存 MVP 到订单与履约扩展的开发。

**Architecture:** 采用模块化单体作为第一阶段架构，按组织权限、商品、订单、库存、履约、集成、安全导入、异常与审计划分清晰边界。外部 ERP、WMS 和渠道通过 Adapter 接口隔离；库存余额、占用和流水使用事务一致性；异步集成使用幂等事件和补偿任务。

**Tech Stack:** 技术栈尚未正式冻结。建议默认方案为 FastAPI + SQLAlchemy + Alembic + PostgreSQL，React + TypeScript + Vite，pytest，Vitest/React Testing Library，GitHub Actions。进入代码任务前必须完成 ADR-031 技术栈确认。

## Global Constraints

- GitHub 仓库 `Fyyk-Whua/OMS` 是项目事实来源。
- 每个 Codex 会话只执行一个明确任务或一个紧密相关的小任务组。
- 所有开发必须执行测试先行、最小实现、回归测试、文档同步和独立提交。
- 不得把密钥、数据库密码、Token、客户数据或未脱敏聊天记录提交到仓库。
- OMS 不替代 ERP，也不扩展为完整 WMS。
- ERP 网络超时不得按明确失败处理。
- 现货库存与预售额度分别计算、显示和占用。
- 库存流水长期保留，不做业务归档。
- 所有文件必须通过统一安全网关后才能进入业务解析。

---

## 1. 计划分解原则

本项目包含多个独立子系统，不应由一个超大 Codex 提示词一次完成。执行时必须按以下子计划顺序推进：

1. 项目治理与技术栈。
2. 应用骨架和 CI。
3. 多租户、组织和权限。
4. 商品与店铺商品映射。
5. 库存 MVP。
6. 文件安全和导入中心。
7. 订单中心。
8. 履约与 ERP/WMS Adapter。
9. 预售和库存运营策略。
10. 异常、补偿、审计和报表。
11. 系统集成测试和发布。

每个子系统开始前，都应另建一个 `docs/superpowers/plans/YYYY-MM-DD-<subsystem>.md` 的细化计划。

## 2. 目标仓库结构

```text
OMS/
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── core/
│   │   ├── modules/
│   │   │   ├── identity/
│   │   │   ├── organization/
│   │   │   ├── catalog/
│   │   │   ├── orders/
│   │   │   ├── inventory/
│   │   │   ├── fulfillment/
│   │   │   ├── integrations/
│   │   │   ├── imports/
│   │   │   ├── anomalies/
│   │   │   └── audit/
│   │   └── shared/
│   ├── migrations/
│   ├── tests/
│   ├── pyproject.toml
│   └── .env.example
├── frontend/
│   ├── src/
│   │   ├── app/
│   │   ├── modules/
│   │   ├── shared/
│   │   └── main.tsx
│   ├── tests/
│   ├── package.json
│   └── .env.example
├── docs/
│   ├── requirements/
│   ├── architecture/
│   ├── api/
│   ├── database/
│   └── superpowers/plans/
├── requirements/
├── adr/
├── conversation/
├── traceability/
├── .github/
│   ├── workflows/
│   ├── ISSUE_TEMPLATE/
│   └── pull_request_template.md
├── AGENTS.md
├── CODEX_WORKFLOW.md
├── CURRENT_STATUS.md
├── NEXT_TASK.md
└── README.md
```

## 3. GitHub + Codex 工作流

### 3.1 分支规则

- `main`：始终保持可运行、测试通过。
- 功能分支：`feat/<req-id>-<short-name>`。
- 修复分支：`fix/<issue-id>-<short-name>`。
- 文档分支：`docs/<topic>`。

禁止 Codex 直接在 `main` 上进行大范围开发。

### 3.2 每个任务的标准输入

每次启动 Codex 时，提示词必须包含：

1. 仓库和分支。
2. 唯一任务目标。
3. 对应 REQ 和 ADR。
4. 允许修改的目录和文件。
5. 禁止事项。
6. 需要新增或修改的测试。
7. 验证命令。
8. 完成后必须更新的文档。

### 3.3 每个任务的完成标准

- 测试先失败再通过。
- 所有既有测试通过。
- 无未提交文件。
- 更新 `CURRENT_STATUS.md`、`NEXT_TASK.md` 和追踪矩阵。
- 提交信息包含需求编号，例如：

```bash
git commit -m "feat(inventory): add atomic reservation [REQ-INVENTORY-043]"
```

## 4. 阶段 0：需求和架构基线

### Task 0.1：建立技术栈 ADR

**Files:**
- Create: `adr/ADR-031-technology-stack.md`
- Create: `docs/architecture/TECH_STACK_EVALUATION.md`
- Modify: `adr/README.md`
- Modify: `CURRENT_STATUS.md`

**Deliverable:** 对后端、前端、数据库、缓存、消息、测试和部署方式做明确选择。

- [ ] 比较至少两套方案：Python/FastAPI 与 Java/Spring Boot，说明团队成本、开发效率、并发模型、部署和维护差异。
- [ ] 明确第一阶段是否使用 Redis 和消息队列；若没有真实需求则不引入。
- [ ] 明确数据库版本、Python/Node 版本下限和包管理工具。
- [ ] 编写 ADR-031 并标记 `ACCEPTED`。
- [ ] 提交：`docs: decide OMS technology stack [ADR-031]`。

### Task 0.2：需求全量审计

**Files:**
- Create: `requirements/OMS_REQUIREMENT_AUDIT.md`
- Create: `requirements/UNCONFIRMED_ITEMS.md`
- Create: `traceability/OMS_MASTER_TRACEABILITY.md`
- Modify: `requirements/README.md`

**Deliverable:** 所有现有需求都有状态、优先级、验收入口和对应模块。

- [ ] 审核 `REQ-INVENTORY-001` 至 `REQ-INVENTORY-053`。
- [ ] 审核 `REQ-SECURITY-005` 和 `REQ-IMPORT-001` 至 `REQ-IMPORT-004`。
- [ ] 把 `REQ-INVENTORY-020` 保持为 `DISCUSSING`，不得误纳入 MVP。
- [ ] 识别重复、冲突和缺失需求。
- [ ] 为每条需求标记 MVP、Phase 2 或 Deferred。
- [ ] 提交：`docs: audit OMS requirements baseline`。

### Task 0.3：订单、组织和履约需求补齐

**Files:**
- Create: `requirements/REQ-ORGANIZATION.md`
- Create: `requirements/REQ-CATALOG.md`
- Create: `requirements/REQ-ORDER.md`
- Create: `requirements/REQ-FULFILLMENT.md`
- Create: `requirements/REQ-INTEGRATION.md`

**Deliverable:** 库存依赖的上游对象具有正式需求编号和状态机定义。

- [ ] 定义租户、企业、店铺、库存组织、仓库关系和权限规则。
- [ ] 定义 SKU、组合商品和渠道商品映射。
- [ ] 定义订单主状态、审核状态、支付状态、履约状态和异常状态。
- [ ] 定义拆单、合单、取消和订单修改。
- [ ] 定义四种履约模型及 Adapter 责任。
- [ ] 提交：`docs: add organization order and fulfillment requirements`。

## 5. 阶段 1：项目骨架和工程质量

### Task 1.1：初始化后端骨架

**Files:**
- Create: `backend/pyproject.toml`
- Create: `backend/app/main.py`
- Create: `backend/app/core/config.py`
- Create: `backend/app/core/errors.py`
- Create: `backend/tests/test_health.py`
- Create: `backend/.env.example`

**Interfaces:**
- Produces: `GET /api/health` 返回 `{"status":"ok"}`。

- [ ] 编写失败测试 `backend/tests/test_health.py`。
- [ ] 运行 `pytest backend/tests/test_health.py -v`，确认因应用不存在而失败。
- [ ] 创建最小 FastAPI 应用和 `/api/health`。
- [ ] 运行测试并确认通过。
- [ ] 提交：`chore(backend): initialize application skeleton`。

### Task 1.2：初始化前端骨架

**Files:**
- Create: `frontend/package.json`
- Create: `frontend/src/main.tsx`
- Create: `frontend/src/app/App.tsx`
- Create: `frontend/src/app/App.test.tsx`
- Create: `frontend/vite.config.ts`
- Create: `frontend/.env.example`

**Deliverable:** 前端显示 OMS 基础页面并具备测试环境。

- [ ] 编写失败测试，断言页面显示 `OMS`。
- [ ] 运行 `npm test -- --run`，确认失败。
- [ ] 实现最小页面。
- [ ] 运行测试和生产构建。
- [ ] 提交：`chore(frontend): initialize application skeleton`。

### Task 1.3：建立 CI 和仓库规范

**Files:**
- Create: `.github/workflows/backend-ci.yml`
- Create: `.github/workflows/frontend-ci.yml`
- Create: `.github/pull_request_template.md`
- Create: `AGENTS.md`
- Create: `CODEX_WORKFLOW.md`
- Create: `.gitignore`

**Deliverable:** PR 自动执行后端测试、前端测试和构建。

- [ ] 配置后端依赖安装和 pytest。
- [ ] 配置前端依赖安装、测试和构建。
- [ ] PR 模板要求填写 REQ、ADR、测试和回滚方式。
- [ ] `AGENTS.md` 规定 Codex 可修改范围、测试要求和文档同步规则。
- [ ] 提交：`ci: add quality gates and Codex workflow`。

## 6. 阶段 2：多租户、组织和权限

### Task 2.1：租户与组织领域模型

**Files:**
- Create: `backend/app/modules/organization/models.py`
- Create: `backend/app/modules/organization/schemas.py`
- Create: `backend/app/modules/organization/repository.py`
- Create: `backend/app/modules/organization/service.py`
- Create: `backend/tests/organization/test_organization_model.py`
- Create: `backend/migrations/versions/*_create_organization_tables.py`

**Deliverable:** Tenant、Enterprise、Store、InventoryOrganization、Warehouse 及店铺与库存组织多对多关系。

### Task 2.2：数据权限服务

**Files:**
- Create: `backend/app/modules/identity/data_scope.py`
- Create: `backend/app/modules/identity/dependencies.py`
- Create: `backend/tests/identity/test_data_scope.py`

**Produces:**

```python
class DataScope:
    tenant_id: str
    enterprise_ids: set[str]
    store_ids: set[str]
    inventory_organization_ids: set[str]
```

所有查询服务必须接收 `DataScope`，不能信任前端提交的授权范围。

### Task 2.3：组织管理 API 和页面

**Files:**
- Create: `backend/app/modules/organization/api.py`
- Create: `frontend/src/modules/organization/*`
- Create: `backend/tests/organization/test_organization_api.py`
- Create: `frontend/src/modules/organization/OrganizationPage.test.tsx`

**Deliverable:** 授权范围内的企业、店铺、库存组织和仓库查询与维护。

## 7. 阶段 3：商品中心

### Task 3.1：SKU 和组合商品

**Files:**
- Create: `backend/app/modules/catalog/models.py`
- Create: `backend/app/modules/catalog/service.py`
- Create: `backend/tests/catalog/test_bundle_calculation.py`
- Create: `backend/migrations/versions/*_create_catalog_tables.py`

**Deliverable:** 实际 SKU、组合商品组成明细和最小可组合数量计算。

### Task 3.2：渠道商品映射

**Files:**
- Create: `backend/app/modules/catalog/channel_mapping.py`
- Create: `backend/tests/catalog/test_channel_mapping.py`

**Deliverable:** 渠道商品编码可以稳定映射到内部 SKU 或组合商品。

## 8. 阶段 4：库存 MVP

库存 MVP 必须拆分成单独详细计划：

`docs/superpowers/plans/YYYY-MM-DD-inventory-mvp.md`

至少包含以下可独立验收任务。

### Task 4.1：库存余额和不可变流水

**Files:**
- Create: `backend/app/modules/inventory/balance.py`
- Create: `backend/app/modules/inventory/transactions.py`
- Create: `backend/tests/inventory/test_balance_transaction_atomicity.py`
- Create: `backend/migrations/versions/*_create_inventory_balance_and_transaction.py`

**Acceptance:** 余额和流水同事务更新；已完成流水不可更新或删除；错误使用冲销。

### Task 4.2：统一可售库存服务

**Files:**
- Create: `backend/app/modules/inventory/sellable_service.py`
- Create: `backend/app/modules/inventory/policies.py`
- Create: `backend/tests/inventory/test_sellable_inventory.py`

**Produces:**

```python
@dataclass(frozen=True)
class SellableInventoryResult:
    physical_sellable_qty: Decimal
    presale_sellable_qty: Decimal
    source: str
    snapshot_at: datetime | None
    status: str
```

### Task 4.3：原子库存占用

**Files:**
- Create: `backend/app/modules/inventory/reservations.py`
- Create: `backend/tests/inventory/test_atomic_reservation.py`
- Create: `backend/tests/inventory/test_bundle_reservation_group.py`

**Acceptance:** 并发占用不会超卖；组合商品全部成功或全部失败；重复幂等键返回原结果。

### Task 4.4：ERP 快照和三态结果

**Files:**
- Create: `backend/app/modules/integrations/erp/base.py`
- Create: `backend/app/modules/inventory/erp_snapshot.py`
- Create: `backend/tests/inventory/test_erp_unknown_result.py`

**Produces:** `SUCCESS`、`DEFINITE_FAILURE`、`UNKNOWN`。

### Task 4.5：超时和补偿任务

**Files:**
- Create: `backend/app/modules/inventory/expiration_job.py`
- Create: `backend/app/modules/inventory/compensation.py`
- Create: `backend/tests/inventory/test_reservation_expiration.py`
- Create: `backend/tests/inventory/test_compensation_idempotency.py`

**Acceptance:** 普通过期可释放；ERP UNKNOWN 不得自动释放；超过重试转人工。

### Task 4.6：库存流水和占用查询

**Files:**
- Create: `backend/app/modules/inventory/api.py`
- Create: `frontend/src/modules/inventory/transactions/*`
- Create: `frontend/src/modules/inventory/reservations/*`
- Create: `backend/tests/inventory/test_transaction_query_scope.py`

**Acceptance:** 日期默认上月 1 日至当前日期；企业、店铺和库存组织为空时查询授权范围；服务端分页；全历史可查询。

## 9. 阶段 5：文件安全和导入中心

### Task 5.1：文件安全网关

**Files:**
- Create: `backend/app/modules/imports/file_security.py`
- Create: `backend/app/modules/imports/scanners/base.py`
- Create: `backend/app/modules/imports/storage.py`
- Create: `backend/tests/imports/test_file_signature_validation.py`
- Create: `backend/tests/imports/test_excel_security.py`

**Acceptance:** 扩展名、MIME、Magic Number、大小、Hash 和扫描状态都有明确结果；业务模块不能读取未通过文件。

### Task 5.2：导入任务和错误明细

**Files:**
- Create: `backend/app/modules/imports/tasks.py`
- Create: `backend/app/modules/imports/validators.py`
- Create: `backend/tests/imports/test_import_validation.py`

**Acceptance:** 错误包含行号、字段、原值、类型和信息；校验阶段不写业务表。

### Task 5.3：部分导入确认

**Files:**
- Create: `backend/app/modules/imports/confirmation.py`
- Create: `backend/tests/imports/test_partial_import_confirmation.py`
- Create: `frontend/src/modules/imports/ImportResultPage.tsx`
- Create: `frontend/src/modules/imports/ImportResultPage.test.tsx`

**Acceptance:** 有错误时默认不导入；只有用户明确选择后才导入成功部分。

### Task 5.4：分页数量组件

**Files:**
- Create: `frontend/src/shared/components/EditablePageSize.tsx`
- Create: `frontend/src/shared/components/EditablePageSize.test.tsx`

**Acceptance:** 下拉支持 100/200/300/500/1000/2000/5000；允许输入 6000；非数字、小数和非正数显示明确错误。

## 10. 阶段 6：订单中心

必须先建立订单详细计划：

`docs/superpowers/plans/YYYY-MM-DD-order-center.md`

核心任务：

1. 订单主数据和状态机。
2. 渠道订单幂等接入。
3. 审核工作流。
4. 组合商品拆分。
5. 订单修改与库存增量占用/释放。
6. 取消订单按库存责任方释放。
7. 预售订单类型和等待状态。
8. 订单异常和人工处理。

## 11. 阶段 7：履约和外部集成

核心任务：

1. FulfillmentOrder 和履约明细。
2. 四种履约模型配置。
3. ERP Adapter 合同和 Mock Adapter。
4. WMS Adapter 合同和 Mock Adapter。
5. 外部事件签名、去重和事件日志。
6. ERP 状态查询和补偿。
7. 出库、取消和回传状态机。

外部系统未接入前，必须使用可控 Mock Adapter 完成端到端测试。

## 12. 阶段 8：预售与运营库存策略

核心任务：

1. PreSaleStrategy 状态机。
2. 自动上下架任务。
3. 到货时间窗口净增量监测。
4. 转现货 FIFO 队列。
5. 安全库存规则优先级。
6. 渠道额度和共享库存池。
7. 冻结和解冻。
8. 赠品库存处理。

## 13. 阶段 9：异常、审计和报表

核心任务：

1. 异常中心。
2. 补偿任务列表、详情和人工接管。
3. 高风险操作二次确认。
4. 库存流水、占用、ERP 同步和补偿时间线。
5. 库存余额、流水、占用、差异和策略报表。
6. 规则型异常检测。

第一阶段 AI 只读取分析服务的汇总结果并提供建议，不得直接修改库存。

## 14. 阶段 10：系统测试和发布

### Task 10.1：端到端场景

必须覆盖：

1. OMS 独立库存正常下单、占用、取消和释放。
2. 两个并发订单竞争同一库存，不能超卖。
3. ERP 成功确认并转交责任。
4. ERP 明确失败并释放。
5. ERP 超时进入 UNKNOWN，补偿查询后成功或失败。
6. 组合商品部分库存不足，整组占用失败。
7. 预售到期自动下架。
8. 到货净增量达到阈值，停止新增预售。
9. 有错误 Excel 默认不导入，用户确认后仅导入成功部分。
10. 未授权用户无法查询其他企业、店铺和库存组织流水。

### Task 10.2：发布检查

**Files:**
- Create: `docs/release/RELEASE_CHECKLIST.md`
- Create: `docs/operations/RUNBOOK.md`
- Create: `docs/operations/BACKUP_AND_RECOVERY.md`
- Create: `docs/security/SECURITY_REVIEW.md`

发布门槛：

- 所有 P0 测试通过。
- 数据库迁移可正向执行并有回滚说明。
- 敏感信息扫描通过。
- 权限、文件上传和外部回调安全评审通过。
- 关键业务监控和告警可用。

## 15. 里程碑建议

|里程碑|目标|退出条件|
|---|---|---|
|M0|需求和技术基线|ADR-031、需求审计、MVP 范围确认|
|M1|工程骨架|后端、前端、CI、Codex 工作流可用|
|M2|组织和商品基础|多租户权限和 SKU 模型通过测试|
|M3|库存 MVP|余额、流水、可售、占用、ERP 三态可演示|
|M4|安全导入|库存 Excel 可安全校验、预览和确认导入|
|M5|订单闭环|订单接入、审核、占用、取消闭环|
|M6|履约集成|Mock ERP/WMS 端到端闭环|
|M7|预售和策略|预售、渠道额度、安全库存可用|
|M8|发布候选|E2E、安全、运维和恢复检查通过|

## 16. Codex 单任务提示词模板

```text
仓库：Fyyk-Whua/OMS
分支：feat/<req-id>-<task>

任务：只实现 <唯一目标>。
需求：<REQ 编号>
架构决策：<ADR 编号>

开始前必须读取：
- AGENTS.md
- CURRENT_STATUS.md
- NEXT_TASK.md
- 对应 requirements 文件
- 对应 ADR 文件
- 对应详细实现计划

允许修改：
- <明确目录和文件>

禁止：
- 修改任务范围外模块
- 引入未批准依赖
- 连接真实 ERP/WMS
- 跳过测试
- 提交密钥或本地配置

执行：
1. 先写失败测试。
2. 运行指定测试并确认失败原因正确。
3. 编写最小实现。
4. 运行局部测试和全量回归。
5. 更新 CURRENT_STATUS.md、NEXT_TASK.md 和追踪矩阵。
6. 输出修改文件、测试结果、风险和下一任务。
```

## 17. 计划自检结果

- 已覆盖当前确认的库存、预售、文件安全和导入需求。
- 已明确未完成的订单、组织、商品和履约需求必须先补齐。
- 未把临期批次管理误纳入 MVP。
- 未要求第一阶段连接真实 ERP、WMS 或引入复杂消息基础设施。
- 技术栈尚未确认，因此代码开发前设置 ADR-031 强制门槛。
