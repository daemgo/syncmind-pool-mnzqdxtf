> **版本**：1.0.0 | **状态**：初稿 | **更新时间**：2026-05-06
>
> **来源方案**：normal 场景 v1.0.0

---

#### 约定说明

本文档使用以下标准值：
- **布局类型**: `list` / `detail` / `form` / `dashboard` / `steps` / `custom`
- **区块类型**: `table` / `form` / `card` / `cards` / `chart` / `tabs` / `steps` / `timeline` / `description` / `statistic` / `custom`
- **字段类型**: `text` / `textarea` / `number` / `money` / `date` / `select` / `multiselect` / `switch` / `upload` 等

---

#### 一、产品概述

### 1.1 项目背景

天霖云 2023 年成立，做工程行业数字化，主要基于钉钉+宜搭平台提供项目管理、流程协同等服务。公司 50 人以下，SaaS 订阅和项目定制两条腿走路，近百个行业定制项目落地。

最近两年业务扩张快，销售团队也在扩，问题开始变突出：客户信息散在每个人手里，没有统一管理；商机跟进过程不透明，老板看不到进展；合同回款靠记忆，容易遗漏。

天霖云想以钉钉宜搭为底座，快速搭一套 CRM，把客户档案、商机跟进、合同回款都管起来，先解决信息散和过程黑盒两个最痛的问题。

### 1.2 产品目标

- 客户档案统一管理，杜绝因人员流动导致的客户流失
- 商机跟进全流程透明，管理者实时掌握团队商机状态
- 合同集中归档，到期自动提醒，降低续约流失风险
- 分期回款可视化，加快资金回笼
- 销售在钉钉内完成所有核心操作，无需切换工具

### 1.3 目标用户

| 角色 | 描述 | 核心诉求 |
|------|------|----------|
| 销售人员 | 直接使用 CRM 记录客户、商机和拜访 | 录入方便、移动端可用、不增加负担 |
| 销售管理者 | 关注团队整体情况和关键数据 | 实时看到销售漏斗、合同到期、回款进度 |
| 企业主/老板 | 最终决策者，需要全局视图 | 一眼看全局，无需逐个追问 |

### 1.4 范围定义

**本期包含：**

- 客户档案管理（分类分级、标签）
- 商机跟进（阶段管理、销售漏斗）
- 拜访计划与执行记录
- 合同台账（到期提醒）
- 回款跟踪（分期节点）
- 续约预警与客户健康度
- 钉钉深度集成（扫码登录、消息通知）
- 基础权限隔离
- 移动端优先体验

**本期不含：**

- 复杂 BI 报表与自定义图表（P2 看板视图本期仅做简化版阶段看板）
- 多项目关联管理（P2，优先级低）
- 与外部财务系统或 ERP 的数据打通

---

#### 二、信息架构

### 2.1 站点地图

- 客户管理（icon: team）
  - 客户列表 → `/customers`
  - 新建客户 → `/customers/new`
  - 客户详情 → `/customers/:id`
  - 编辑客户 → `/customers/:id/edit`
- 商机管理（icon: trending_up）
  - 商机列表 → `/opportunities`
  - 新建商机 → `/opportunities/new`
  - 商机详情 → `/opportunities/:id`
  - 编辑商机 → `/opportunities/:id/edit`
- 拜访管理（icon: calendar）
  - 拜访列表 → `/visits`
  - 新建拜访 → `/visits/new`
  - 拜访详情 → `/visits/:id`
- 合同管理（icon: folder_open）
  - 合同台账 → `/contracts`
  - 新建合同 → `/contracts/new`
  - 合同详情 → `/contracts/:id`
  - 编辑合同 → `/contracts/:id/edit`
- 系统（icon: settings）
  - 钉钉集成 → `/settings/dingtalk`
  - 权限设置 → `/settings/permissions`

### 2.2 导航结构

| 一级菜单 | 二级菜单 | 路由 | 说明 |
|----------|----------|------|------|
| 客户管理 | 客户列表 | `/customers` | 客户档案列表，支持筛选 |
| 商机管理 | 商机列表 | `/opportunities` | 商机列表，支持按阶段筛选 |
| 拜访管理 | 拜访记录 | `/visits` | 拜访计划与执行记录列表 |
| 合同管理 | 合同台账 | `/contracts` | 合同列表，支持到期状态筛选 |
| 系统 | 钉钉集成 | `/settings/dingtalk` | 钉钉登录和消息通知配置 |
| 系统 | 权限设置 | `/settings/permissions` | 角色权限配置 |

---

#### 三、功能模块

### 3.1 客户管理

> 将散落在各销售人员手中的客户信息统一归档，支持分类分级，避免人员流动导致的客户流失。

#### 3.1.1 客户列表

**路由**：`/customers`
**布局**：`list`
**描述**：展示所有客户档案，支持按分类、等级、负责人等维度筛选。

##### 筛选条件（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 客户名称 | name | text | 否 | 模糊搜索 |
| 客户分类 | category | select | 否 | 选项来源: dict-customer-category |
| 客户等级 | level | select | 否 | 选项来源: dict-customer-level |
| 负责人 | ownerId | select | 否 | 钉钉用户列表 |
| 客户健康度 | healthStatus | select | 否 | 选项来源: dict-customer-health |

##### 数据列表（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 客户名称 | name | text | 是 |  |
| 客户分类 | category | select | 是 | dict-customer-category |
| 客户等级 | level | select | 是 | dict-customer-level |
| 所属行业 | industry | text | 是 |  |
| 负责人 | ownerName | text | 是 | 关联钉钉用户 |
| 最近拜访 | lastVisitDate | date | 是 | 最近一条拜访记录日期 |
| 客户健康度 | healthStatus | tag | 否 | dict-customer-health |
| 操作 | actions | custom | 否 | 查看、编辑 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 新建客户 | primary | 页面顶部右侧 | 跳转 `/customers/new` | 销售/管理员 |
| 导出 | default | 工具栏右侧 | 导出当前筛选结果为 Excel | 管理员 |
| 查看 | link | 操作列 | 跳转 `/customers/:id` | 所有人 |
| 编辑 | link | 操作列 | 跳转 `/customers/:id/edit` | 负责人/管理员 |

##### 业务规则

- 列表默认按最近更新时间倒序排列
- 销售人员仅可见自己负责的客户；管理员可见全部
- 健康度由系统根据拜访间隔和商机状态自动计算

#### 3.1.2 新建/编辑客户

**路由**：`/customers/new` | `/customers/:id/edit`
**布局**：`form`
**描述**：新建或编辑客户档案信息。

##### 基本信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 客户名称 | name | text | 是 |  |
| 客户分类 | category | select | 是 | dict-customer-category |
| 客户等级 | level | select | 是 | dict-customer-level |
| 所属行业 | industry | text | 否 |  |
| 公司规模 | companySize | select | 否 | dict-company-size |
| 公司地址 | address | text | 否 |  |
| 公司简介 | description | textarea | 否 |  |

##### 联系人信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 联系人姓名 | contactName | text | 否 |  |
| 职务 | contactTitle | text | 否 |  |
| 手机号 | contactPhone | text | 否 |  |
| 邮箱 | contactEmail | text | 否 |  |
| 主要决策人 | isDecisionMaker | switch | 否 |  |

##### 归属信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 负责人 | ownerId | select | 是 | 钉钉用户列表 |
| 客户标签 | tags | multiselect | 否 | 自定义标签列表 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 保存 | primary | 表单底部 | 保存后跳转客户详情页 | 销售/管理员 |
| 取消 | default | 表单底部 | 返回客户列表页 | 所有人 |

##### 业务规则

- 保存时自动同步负责人到钉钉组织架构
- 客户名称不允许重复（按核心名称去重）

#### 3.1.3 客户详情

**路由**：`/customers/:id`
**布局**：`detail`
**描述**：客户档案详情页，聚合基本信息、商机、拜访、合同等关联数据。

##### 基本信息（description）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 客户名称 | name | text |  |
| 客户分类 | category | select | dict-customer-category |
| 客户等级 | level | select | dict-customer-level |
| 所属行业 | industry | text |  |
| 公司规模 | companySize | select | dict-company-size |
| 负责人 | ownerName | text |  |
| 客户标签 | tags | multiselect |  |
| 客户健康度 | healthStatus | tag | dict-customer-health |
| 创建时间 | createdAt | date |  |
| 更新时间 | updatedAt | date |  |

##### 联系人（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 姓名 | contactName | text | 否 |  |
| 职务 | contactTitle | text | 否 |  |
| 手机号 | contactPhone | text | 否 |  |
| 主要决策人 | isDecisionMaker | switch | 否 |  |

##### 关联商机（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 商机名称 | name | text | 是 |  |
| 销售阶段 | stage | select | 是 | dict-opportunity-stage |
| 预计金额 | expectedAmount | money | 是 |  |
| 预计成交日期 | expectedCloseDate | date | 是 |  |
| 负责人 | ownerName | text | 是 |  |

##### 拜访记录（timeline）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 拜访日期 | visitDate | date |  |
| 拜访类型 | visitType | select | dict-visit-type |
| 拜访人 | visitorName | text |  |
| 拜访内容摘要 | summary | text |  |
| 下次拜访计划 | nextVisitPlan | text |  |

##### 操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 编辑 | default | 页面顶部 | 跳转编辑页 | 负责人/管理员 |
| 新建拜访 | primary | 关联区域 | 跳转 `/visits/new?customerId=:id` | 销售/管理员 |
| 新建商机 | default | 关联区域 | 跳转 `/opportunities/new?customerId=:id` | 销售/管理员 |
| 新建合同 | default | 关联区域 | 跳转 `/contracts/new?customerId=:id` | 销售/管理员 |

---

### 3.2 商机管理

> 统一管理商机跟进全过程，实现销售阶段透明化，让管理者实时掌握团队商机状态，提升商机转化率。

#### 3.2.1 商机列表

**路由**：`/opportunities`
**布局**：`list`
**描述**：展示所有商机，支持按阶段、负责人、时间等维度筛选和漏斗视图。

##### 筛选条件（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 商机名称 | name | text | 否 | 模糊搜索 |
| 所属客户 | customerId | select | 否 | 关联客户列表 |
| 销售阶段 | stage | multiselect | 否 | dict-opportunity-stage |
| 负责人 | ownerId | select | 否 | 钉钉用户列表 |
| 预计成交月份 | expectedCloseMonth | date | 否 | 筛选当月到期商机 |

##### 数据列表（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 商机名称 | name | text | 是 |  |
| 所属客户 | customerName | text | 是 | 关联客户名称 |
| 销售阶段 | stage | tag | 是 | dict-opportunity-stage，带颜色 |
| 预计金额 | expectedAmount | money | 是 |  |
| 预计成交日期 | expectedCloseDate | date | 是 |  |
| 负责人 | ownerName | text | 是 |  |
| 最近更新时间 | updatedAt | date | 是 |  |
| 操作 | actions | custom | 否 | 查看、编辑 |

##### 阶段漏斗（statistic）

| 指标 | fieldKey | 说明 |
|------|----------|------|
| 线索总量 | totalCount | 当前所有商机数量 |
| 各阶段数量 | stageCount | 每个阶段商机的数量分布 |
| 阶段金额汇总 | stageAmount | 每个阶段的金额合计 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 新建商机 | primary | 页面顶部右侧 | 跳转 `/opportunities/new` | 销售/管理员 |
| 漏斗视图 | default | 工具栏 | 切换为阶段看板视图 | 所有人 |

#### 3.2.2 商机阶段看板（Kanban）

**路由**：`/opportunities/kanban`
**布局**：`custom`
**描述**：以看板形式展示商机在各阶段的分布，便于直观管理销售漏斗。

##### 看板列（tabs）

每个阶段一列，列标题显示阶段名称和商机数量。

##### 商机卡片（cards）

| 字段 | fieldKey | 说明 |
|------|----------|------|
| 商机名称 | name | 卡片标题 |
| 所属客户 | customerName | 客户名称 |
| 预计金额 | expectedAmount | 金额，带单位 |
| 预计成交日期 | expectedCloseDate | 截止日期，距今天数 |
| 负责人头像 | ownerAvatar | 钉钉头像 |

##### 看板操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 新建商机 | primary | 页面顶部 | 跳转新建页 | 销售/管理员 |
| 拖拽卡片 | - | 看板卡片 | 拖动卡片到其他阶段列，自动更新商机阶段 | 负责人/管理员 |

##### 业务规则

- 拖拽卡片变更阶段时，系统自动记录阶段变更时间和操作人
- 拖入"已成交"或"已失败"列时，弹出确认对话框

#### 3.2.3 新建/编辑商机

**路由**：`/opportunities/new` | `/opportunities/:id/edit`
**布局**：`form`
**描述**：新建或编辑商机，关联客户并填写跟进信息。

##### 基本信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 商机名称 | name | text | 是 |  |
| 所属客户 | customerId | select | 是 | 关联客户档案 |
| 销售阶段 | stage | select | 是 | dict-opportunity-stage |
| 商机来源 | source | select | 否 | dict-opportunity-source |
| 负责人 | ownerId | select | 是 | 钉钉用户列表 |

##### 商务信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 预计金额 | expectedAmount | money | 是 |  |
| 预计成交日期 | expectedCloseDate | date | 是 |  |
| 产品/服务描述 | productDescription | textarea | 否 |  |
| 竞争对手 | competitors | multiselect | 否 | 文本输入多个竞争对手 |

##### 跟进信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 跟进状态 | followUpStatus | select | 否 | dict-followup-status |
| 跟进备注 | followUpNotes | textarea | 否 | 最新跟进情况 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 保存 | primary | 表单底部 | 保存后跳转商机详情页 | 销售/管理员 |
| 取消 | default | 表单底部 | 返回商机列表页 | 所有人 |

##### 业务规则

- 阶段变更时，自动在商机详情页记录变更时间和操作人
- 预计成交日期到期未成交，系统自动发送钉钉消息提醒负责人

#### 3.2.4 商机详情

**路由**：`/opportunities/:id`
**布局**：`detail`
**描述**：商机详情，聚合基本信息、阶段变更历史和关联客户信息。

##### 基本信息（description）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 商机名称 | name | text |  |
| 所属客户 | customerName | text | 关联客户，可点击跳转 |
| 销售阶段 | stage | tag | dict-opportunity-stage，带颜色 |
| 商机来源 | source | select | dict-opportunity-source |
| 预计金额 | expectedAmount | money |  |
| 预计成交日期 | expectedCloseDate | date |  |
| 负责人 | ownerName | text |  |
| 创建时间 | createdAt | date |  |
| 更新时间 | updatedAt | date |  |

##### 阶段变更历史（timeline）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 变更时间 | changedAt | date |  |
| 变更前阶段 | fromStage | select | dict-opportunity-stage |
| 变更后阶段 | toStage | select | dict-opportunity-stage |
| 操作人 | operatorName | text |  |
| 变更说明 | note | text |  |

##### 业务规则

- 阶段变更记录自动生成，不可删除
- 每次变更触发钉钉消息通知相关人员（负责人和上级）

---

### 3.3 拜访管理

> 沉淀完整的客户沟通历史，关联拜访计划与执行记录，提升客户体验，减少跟进脱节。

#### 3.3.1 拜访列表

**路由**：`/visits`
**布局**：`list`
**描述**：展示所有拜访记录，支持按客户、拜访类型、日期范围筛选。

##### 筛选条件（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 拜访客户 | customerId | select | 否 | 关联客户列表 |
| 拜访类型 | visitType | select | 否 | dict-visit-type |
| 拜访日期 | visitDateRange | date | 否 | 起止日期范围 |
| 负责人 | ownerId | select | 否 | 钉钉用户列表 |

##### 数据列表（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 拜访日期 | visitDate | date | 是 |  |
| 拜访客户 | customerName | text | 是 | 关联客户名称 |
| 拜访类型 | visitType | select | 是 | dict-visit-type |
| 拜访人 | visitorName | text | 是 |  |
| 拜访内容摘要 | summary | text | 否 | 截取前50字 |
| 下次拜访计划 | nextVisitPlan | text | 否 | 有计划时显示 |
| 操作 | actions | custom | 否 | 查看、编辑 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 新建拜访 | primary | 页面顶部右侧 | 跳转 `/visits/new` | 销售/管理员 |
| 日历视图 | default | 工具栏 | 切换为日历视图 | 所有人 |

#### 3.3.2 新建/编辑拜访

**路由**：`/visits/new` | `/visits/:id/edit`
**布局**：`form`
**描述**：新建或编辑拜访记录，支持关联客户和填写跟进计划。

##### 拜访信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 拜访客户 | customerId | select | 是 | 关联客户档案，支持搜索 |
| 拜访日期 | visitDate | date | 是 |  |
| 拜访类型 | visitType | select | 是 | dict-visit-type |
| 拜访人 | visitorId | select | 是 | 钉钉用户列表 |

##### 拜访内容（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 拜访目的 | purpose | textarea | 否 |  |
| 本次拜访内容 | content | textarea | 是 |  |
| 客户反馈 | feedback | textarea | 否 |  |
| 下次拜访计划 | nextVisitPlan | textarea | 否 | 下次拜访时间及目标 |

##### 业务规则

- 保存后自动更新客户的"最近拜访日期"字段
- 商机负责人收到拜访完成通知

---

### 3.4 合同管理

> 合同集中归档，到期自动提醒，降低因遗忘导致的续约流失风险。

#### 3.4.1 合同台账

**路由**：`/contracts`
**布局**：`list`
**描述**：展示所有合同，支持按状态、到期时间、客户等维度筛选。

##### 筛选条件（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 合同名称 | name | text | 否 | 模糊搜索 |
| 所属客户 | customerId | select | 否 | 关联客户列表 |
| 合同状态 | status | multiselect | 否 | dict-contract-status |
| 到期提醒 | expiryAlert | select | 否 | dict-expiry-alert |
| 签订日期 | signDateRange | date | 否 | 起止日期范围 |

##### 数据列表（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 合同名称 | name | text | 是 |  |
| 所属客户 | customerName | text | 是 | 关联客户名称 |
| 合同金额 | amount | money | 是 |  |
| 已回款金额 | receivedAmount | money | 是 | 已收款合计 |
| 合同状态 | status | tag | 是 | dict-contract-status，带颜色 |
| 签订日期 | signDate | date | 是 |  |
| 到期日期 | expiryDate | date | 是 | 距今天数 |
| 负责人 | ownerName | text | 是 |  |
| 操作 | actions | custom | 否 | 查看、编辑 |

##### 合同到期看板（statistic）

| 指标 | fieldKey | 说明 |
|------|----------|------|
| 已到期 | expiredCount | 已过到期日的合同数量 |
| 30天内到期 | expiring30DaysCount | 30天内到期的合同数量 |
| 90天内到期 | expiring90DaysCount | 90天内到期的合同数量 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 新建合同 | primary | 页面顶部右侧 | 跳转 `/contracts/new` | 销售/管理员 |
| 导出 | default | 工具栏右侧 | 导出当前筛选结果为 Excel | 管理员 |

##### 业务规则

- 到期前 30 天、7 天各触发一次钉钉消息提醒负责人
- 合同状态为"已终止"时不发送到期提醒

#### 3.4.2 新建/编辑合同

**路由**：`/contracts/new` | `/contracts/:id/edit`
**布局**：`form`
**描述**：新建或编辑合同，关联客户并填写合同关键信息。

##### 基本信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 合同名称 | name | text | 是 |  |
| 所属客户 | customerId | select | 是 | 关联客户档案 |
| 关联商机 | opportunityId | select | 否 | 关联商机，可不填 |
| 负责人 | ownerId | select | 是 | 钉钉用户列表 |

##### 合同信息（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 合同金额 | amount | money | 是 |  |
| 签订日期 | signDate | date | 是 |  |
| 合同开始日期 | startDate | date | 是 |  |
| 合同到期日期 | expiryDate | date | 是 |  |
| 合同状态 | status | select | 是 | dict-contract-status |
| 合同扫描件 | attachment | upload | 否 | 支持 PDF、图片上传 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 保存 | primary | 表单底部 | 保存后跳转合同详情页 | 销售/管理员 |
| 取消 | default | 表单底部 | 返回合同列表页 | 所有人 |

#### 3.4.3 合同详情

**路由**：`/contracts/:id`
**布局**：`detail`
**描述**：合同详情，聚合基本信息、回款计划和关联数据。

##### 基本信息（description）

| 字段 | fieldKey | 类型 | 说明 |
|------|----------|------|------|
| 合同名称 | name | text |  |
| 所属客户 | customerName | text | 关联客户名称，可点击跳转 |
| 合同金额 | amount | money |  |
| 已回款金额 | receivedAmount | money |  |
| 待回款金额 | pendingAmount | money | 自动计算：合同金额-已回款金额 |
| 合同状态 | status | tag | dict-contract-status，带颜色 |
| 签订日期 | signDate | date |  |
| 合同开始日期 | startDate | date |  |
| 合同到期日期 | expiryDate | date |  |
| 距到期天数 | daysToExpiry | number | 自动计算，显示倒计时 |
| 负责人 | ownerName | text |  |
| 合同扫描件 | attachment | upload | 附件预览/下载 |

##### 回款计划（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 期次 | periodNo | number | 否 | 第1期、第2期... |
| 计划回款日期 | planDate | date | 是 |  |
| 计划回款金额 | planAmount | money | 是 |  |
| 已回款金额 | receivedAmount | money | 是 |  |
| 回款状态 | paymentStatus | tag | 是 | dict-payment-status |
| 实际到账日期 | actualDate | date | 否 | 实际回款日期 |

##### 回款操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 确认回款 | primary | 回款行操作列 | 弹出确认回款表单，填写实际到账日期和金额 | 负责人/管理员 |
| 添加回款计划 | default | 回款计划区域 | 新增一行回款计划 | 负责人/管理员 |

##### 业务规则

- 合同金额自动汇总所有回款计划金额，超出时提示校验
- 到期前 30 天、7 天自动发送钉钉消息给负责人

---

### 3.5 续约预警

> 基于合同到期时间和客户健康度，提前预警高流失风险客户，帮助销售主动跟进。

#### 3.5.1 续约预警列表

**路由**：`/renewals`
**布局**：`list`
**描述**：展示需要续约关注的客户合同，支持按紧迫程度和客户等级筛选。

##### 筛选条件（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 紧迫程度 | urgency | multiselect | 否 | dict-renewal-urgency |
| 客户等级 | customerLevel | select | 否 | dict-customer-level |
| 负责人 | ownerId | select | 否 | 钉钉用户列表 |

##### 数据列表（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 客户名称 | customerName | text | 是 | 关联客户名称 |
| 合同名称 | contractName | text | 是 | 关联合同名称 |
| 合同金额 | contractAmount | money | 是 |  |
| 到期日期 | expiryDate | date | 是 | 距今天数 |
| 紧迫程度 | urgency | tag | 是 | dict-renewal-urgency，带颜色 |
| 客户健康度 | healthStatus | tag | 是 | dict-customer-health |
| 负责人 | ownerName | text | 是 |  |
| 操作 | actions | custom | 否 | 查看合同、发起拜访 |

##### 业务规则

- 紧迫程度自动计算：距到期 <= 30 天为"紧急"，<= 90 天为"预警"，> 90 天为"关注"
- 系统每周一自动推送钉钉消息给负责人

---

### 3.6 钉钉集成

> 无缝接入钉钉生态，员工无需切换工具，降低使用门槛，提升 adoption 率。

#### 3.6.1 钉钉集成配置

**路由**：`/settings/dingtalk`
**布局**：`form`
**描述**：配置钉钉扫码登录、消息通知推送规则。

##### 登录配置（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 启用钉钉扫码登录 | dingtalkSsoEnabled | switch | 否 | 开启后支持钉钉扫码进入系统 |
| 钉钉应用 AppKey | dingtalkAppKey | text | 否 | 钉钉开放平台应用 AppKey |
| 钉钉应用 AppSecret | dingtalkAppSecret | text | 否 | 钉钉开放平台应用 AppSecret（加密存储） |

##### 消息通知配置（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 商机状态变更通知 | notifyOnStageChange | switch | 否 | 商机阶段变更时通知负责人 |
| 合同到期提醒 | notifyOnContractExpiry | switch | 否 | 合同到期前30天、7天自动提醒 |
| 回款节点提醒 | notifyOnPaymentDue | switch | 否 | 回款计划日期到达时提醒 |
| 续约预警通知 | notifyOnRenewalWarning | switch | 否 | 每周一推送续约预警汇总 |
| 通知接收人规则 | notifyRecipientRule | select | 否 | 仅负责人 / 负责人+上级 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 保存配置 | primary | 表单底部 | 保存后生效 | 管理员 |
| 测试连接 | default | 钉钉应用配置区 | 发送测试消息验证配置正确性 | 管理员 |

##### 业务规则

- 钉钉 AppKey 和 AppSecret 加密存储，不在界面上明文显示
- 所有钉钉消息通过钉钉开放平台机器人或工作通知发送
- 消息内容包含关键数据字段，点击消息可跳转到对应详情页

---

### 3.7 权限管理

> 基于角色的数据隔离，确保销售人员只能看到自己负责的客户数据，敏感信息可控可见。

#### 3.7.1 角色配置

**路由**：`/settings/permissions`
**布局**：`list` + `form`
**描述**：配置系统角色及对应的模块权限和数据可见范围。

##### 角色列表（table）

| 列名 | fieldKey | 列类型 | 可排序 | 说明 |
|------|----------|--------|--------|------|
| 角色名称 | roleName | text | 否 |  |
| 角色类型 | roleType | text | 否 | 管理员 / 销售人员 |
| 描述 | description | text | 否 |  |
| 操作 | actions | custom | 否 | 编辑权限 |

##### 操作

| 按钮 | 类型 | 位置 | 行为 | 权限 |
|------|------|------|------|------|
| 新建角色 | primary | 页面顶部右侧 | 弹出角色配置表单 | 管理员 |
| 编辑 | link | 操作列 | 弹出角色配置表单 | 管理员 |

##### 角色配置表单

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 角色名称 | roleName | text | 是 |  |
| 角色类型 | roleType | select | 是 | 管理员 / 销售人员 |
| 描述 | description | textarea | 否 |  |

##### 模块权限配置（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 客户管理 | permCustomer | multiselect | 否 | 查看 / 新建 / 编辑 / 删除 |
| 商机管理 | permOpportunity | multiselect | 否 | 查看 / 新建 / 编辑 / 删除 |
| 拜访管理 | permVisit | multiselect | 否 | 查看 / 新建 / 编辑 / 删除 |
| 合同管理 | permContract | multiselect | 否 | 查看 / 新建 / 编辑 / 删除 |
| 系统设置 | permSettings | multiselect | 否 | 查看 / 编辑 |

##### 数据可见范围配置（form）

| 字段 | fieldKey | 类型 | 必填 | 说明 |
|------|----------|------|------|------|
| 数据可见范围 | dataScope | select | 是 | 仅本人 / 本部门 / 全部 |
| 允许查看全部数据 | viewAllData | switch | 否 | 开启后不受数据范围限制（管理员） |

##### 业务规则

- 销售人员默认数据范围为"仅本人"，不可修改
- 管理员可查看所有数据
- 权限变更即时生效，无需重新登录

---

### 3.8 移动端适配

> 核心功能在移动端可用，支持外出场景快速操作，提升外勤效率。

#### 3.8.1 移动端优先功能

以下功能必须完整支持移动端操作：

| 功能 | 页面 | 移动端说明 |
|------|------|-----------|
| 客户列表查看 | `/customers` | 支持筛选、搜索、查看 |
| 客户详情查看 | `/customers/:id` | 支持查看基本信息、商机、拜访 |
| 商机列表查看 | `/opportunities` | 支持筛选、查看 |
| 商机阶段变更 | `/opportunities/:id` | 支持在详情页变更阶段 |
| 拜访记录新建 | `/visits/new` | 移动端优先设计，支持快速录入 |
| 合同列表查看 | `/contracts` | 支持筛选、查看 |
| 合同回款确认 | `/contracts/:id` | 支持确认回款操作 |
| 钉钉消息推送 | - | 关键事件自动推送，点击直达详情 |

#### 3.8.2 移动端体验原则

- 表单输入字段精简，核心必填字段不超过 5 个
- 拜访记录支持语音转文字录入
- 列表页优先展示关键信息，详情折叠次要字段
- 所有操作按钮在移动端足够大，便于点击

---

#### 四、全局规则

### 4.1 角色权限

| 角色 | 描述 | 模块权限 |
|------|------|----------|
| 管理员 | 系统管理员，可管理所有数据和配置 | 全部模块读写、系统配置、权限管理 |
| 销售人员 | 主要使用人员，负责客户开拓和跟进 | 本人客户/商机/拜访/合同的读写 |
| 访客 | 仅查看，被动接收通知 | 本人关联数据的查看 |

### 4.2 数据字典

#### 4.2.1 客户分类（dict-customer-category）

| 值 | 显示 | 颜色 |
|----|------|------|
| prospect | 潜在客户 | #909399 |
| active | 活跃客户 | #409EFF |
| inactive | 非活跃客户 | #E6A23C |
| churned | 流失客户 | #F56C6C |

#### 4.2.2 客户等级（dict-customer-level）

| 值 | 显示 | 颜色 |
|----|------|------|
| A | A级（战略客户） | #67C23A |
| B | B级（重点客户） | #409EFF |
| C | C级（普通客户） | #909399 |
| D | D级（观察客户） | #E6A23C |

#### 4.2.3 客户健康度（dict-customer-health）

| 值 | 显示 | 颜色 |
|----|------|------|
| healthy | 健康 | #67C23A |
| attention | 需要关注 | #E6A23C |
| risk | 高风险 | #F56C6C |
| unknown | 未知 | #909399 |

#### 4.2.4 商机阶段（dict-opportunity-stage）

| 值 | 显示 | 颜色 |
|----|------|------|
| lead | 线索 | #909399 |
| qualified | 意向 | #E6A23C |
| proposal | 方案 | #409EFF |
| negotiation | 谈判 | #85CE61 |
| closed_won | 已成交 | #67C23A |
| closed_lost | 已失败 | #F56C6C |

#### 4.2.5 合同状态（dict-contract-status）

| 值 | 显示 | 颜色 |
|----|------|------|
| draft | 草稿 | #909399 |
| active | 执行中 | #409EFF |
| expiring | 即将到期 | #E6A23C |
| expired | 已到期 | #F56C6C |
| terminated | 已终止 | #909399 |

#### 4.2.6 回款状态（dict-payment-status）

| 值 | 显示 | 颜色 |
|----|------|------|
| pending | 待回款 | #E6A23C |
| partial | 部分回款 | #409EFF |
| received | 已到账 | #67C23A |
| overdue | 已逾期 | #F56C6C |

#### 4.2.7 拜访类型（dict-visit-type）

| 值 | 显示 | 颜色 |
|----|------|------|
| first | 首次拜访 | #409EFF |
| followup | 日常拜访 | #67C23A |
| demo | 产品演示 | #E6A23C |
| negotiation | 商务谈判 | #85CE61 |
| contract | 合同洽谈 | #F56C6C |
| other | 其他 | #909399 |

#### 4.2.8 紧迫程度（dict-renewal-urgency）

| 值 | 显示 | 颜色 |
|----|------|------|
| urgent | 紧急（30天内） | #F56C6C |
| warning | 预警（90天内） | #E6A23C |
| attention | 关注（90天以上） | #409EFF |

#### 4.2.9 公司规模（dict-company-size）

| 值 | 显示 |
|----|------|
| startup | 初创（10人以下） |
| small | 小型（10-50人） |
| medium | 中型（50-200人） |
| large | 大型（200人以上） |

#### 4.2.10 商机来源（dict-opportunity-source）

| 值 | 显示 |
|----|------|
| inbound | 主动咨询 |
| outbound | 主动拓展 |
| referral | 客户推荐 |
| event | 展会活动 |
| partner | 渠道合作 |
| other | 其他 |

#### 4.2.11 跟进状态（dict-followup-status）

| 值 | 显示 |
|----|------|
| active | 跟进中 |
| stalled | 停滞 |
| won | 成交 |
| lost | 流失 |

### 4.3 状态流转

#### 4.3.1 商机阶段流转

**状态定义：**

| 状态 | 显示 | 颜色 | 说明 |
|------|------|------|------|
| lead | 线索 | #909399 | 获取到潜在客户信息 |
| qualified | 意向 | #E6A23C | 确认有真实需求 |
| proposal | 方案 | #409EFF | 已提供方案或报价 |
| negotiation | 谈判 | #85CE61 | 进入商务谈判阶段 |
| closed_won | 已成交 | #67C23A | 合同签订 |
| closed_lost | 已失败 | #F56C6C | 商机失败/放弃 |

**流转规则：**

| 当前状态 | 操作 | 目标状态 | 条件 |
|----------|------|----------|------|
| lead | 确认意向 | qualified | 负责人手动变更 |
| qualified | 提交方案 | proposal | 负责人手动变更 |
| proposal | 开始谈判 | negotiation | 负责人手动变更 |
| negotiation | 签订合同 | closed_won | 关联合同创建后自动变更 |
| negotiation | 标记失败 | closed_lost | 负责人手动变更，可填失败原因 |
| (非终态) | 退回前一阶段 | 对应前一阶段 | 负责人手动变更，记录退回原因 |

#### 4.3.2 合同状态流转

**状态定义：**

| 状态 | 显示 | 颜色 | 说明 |
|------|------|------|------|
| draft | 草稿 | #909399 | 合同拟定中 |
| active | 执行中 | #409EFF | 合同已生效执行 |
| expiring | 即将到期 | #E6A23C | 距到期 30 天内 |
| expired | 已到期 | #F56C6C | 已过到期日 |
| terminated | 已终止 | #909399 | 提前终止合同 |

**流转规则：**

| 当前状态 | 操作 | 目标状态 | 条件 |
|----------|------|----------|------|
| draft | 确认签订 | active | 负责人手动变更，确认签订 |
| active | 到期前30天 | expiring | 系统自动判断日期变更 |
| active | 到期 | expired | 系统自动判断日期变更 |
| active | 提前终止 | terminated | 管理员手动变更，填写终止原因 |
| terminated | - | - | 终态，不可变更 |

#### 4.3.3 回款状态流转

**状态定义：**

| 状态 | 显示 | 颜色 | 说明 |
|------|------|------|------|
| pending | 待回款 | #E6A23C | 计划回款日期未到 |
| partial | 部分回款 | #409EFF | 部分回款已到账 |
| received | 已到账 | #67C23A | 全额回款已到账 |
| overdue | 已逾期 | #F56C6C | 计划日期已过但未全额回款 |

**流转规则：**

| 当前状态 | 操作 | 目标状态 | 条件 |
|----------|------|----------|------|
| pending | 部分到账 | partial | 确认回款金额 < 计划金额 |
| pending | 全额到账 | received | 确认回款金额 = 计划金额 |
| pending | 逾期 | overdue | 计划日期到达时未全额回款 |
| partial | 继续回款 | partial | 再次部分到账 |
| partial | 全额到账 | received | 累计回款金额 = 计划金额 |
| overdue | 部分到账 | partial | 逾期后部分回款 |
| overdue | 全额到账 | received | 逾期后全额到账 |

---

#### 附录

### A. 变更记录

| 版本 | 日期 | 变更内容 |
|------|------|----------|
| 1.0.0 | 2026-05-06 | 初始版本，基于 normal 场景方案 v1.0.0 生成 |
