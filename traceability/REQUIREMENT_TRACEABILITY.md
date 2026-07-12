# 需求追踪矩阵

更新时间：2026-07-12

|需求范围|需求文件|设计文件|ADR|状态|
|---|---|---|---|---|
|文件安全|requirements/REQ-SECURITY-IMPORT.md|security/FILE_SECURITY_AND_IMPORT.md|ADR-013|CONFIRMED|
|统一导入中心|requirements/REQ-SECURITY-IMPORT.md|security/FILE_SECURITY_AND_IMPORT.md|ADR-014|CONFIRMED|
|OMS 独立库存|requirements/REQ-INVENTORY.md|module-design/inventory/README.md|ADR-012|CONFIRMED|
|ERP 库存模式|requirements/REQ-INVENTORY.md|module-design/inventory/README.md|ADR-011、ADR-015|CONFIRMED|
|预售策略|requirements/REQ-INVENTORY.md|module-design/inventory/README.md|ADR-016、ADR-017、ADR-025|CONFIRMED|
|库存流水|requirements/REQ-INVENTORY.md|module-design/inventory/README.md|ADR-018、ADR-019、ADR-021|CONFIRMED|
|统一可售计算|requirements/REQ-INVENTORY.md|module-design/inventory/README.md|ADR-022、ADR-023、ADR-024|CONFIRMED|
|库存占用与补偿|requirements/REQ-INVENTORY.md|module-design/inventory/README.md|ADR-026 至 ADR-030|CONFIRMED|
|临期和批次管理|requirements/REQ-INVENTORY.md|尚未形成详细设计|尚未形成|DISCUSSING|

## 后续追踪要求

每个进入开发的需求必须补齐：

`REQ -> Design -> ADR -> Implementation Task -> Commit/PR -> Test Case`

没有需求编号的实现不得直接进入主分支。
