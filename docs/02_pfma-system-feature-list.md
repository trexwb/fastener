# PFMA 管理系统功能清单

> PFMA = Process Failure Mode Analysis（过程失效模式分析）  
> 版本：v1.0 | 日期：2026-04-07 | 作者：趋势研究员

---

## 一、系统定位与范围

本系统针对**紧固件生产与装配全过程**建立结构化的失效数据库，实现：
- 失效模式的录入、分类、检索
- 风险优先级数（RPN）自动评估
- 改进措施的追踪闭环
- 与标准数据库的联动（关联至 `products` 表的具体标准）

---

## 二、核心功能模块

### 模块 A：失效模式库（Failure Mode Database）

**功能描述**：结构化录入和管理已知失效模式，支撑全公司知识积累。

| 功能点 | 说明 |
|---|---|
| A-1 失效模式录入 | 按过程步骤录入：失效模式、失效影响、失效原因 |
| A-2 过程步骤分类 | 覆盖：材料来料 → 冷镦成型 → 搓丝/滚丝 → 热处理 → 表面处理 → 装配 → 出货检验 |
| A-3 标准关联 | 每条失效记录可关联至 `products` 表（具体螺栓型号/标准），复用现有产品数据库 |
| A-4 失效严重度分级 | S（Severity）：1-10 分，参考 AIAG PFMEA 评级表 |
| A-5 批量导入 | 支持 Excel 模板导入历史失效数据 |
| A-6 全文检索 | 按关键词、失效类型、产品标准、过程步骤检索失效案例 |

**数据结构（新建数据表 `pfma_failure_modes`）**：

```sql
CREATE TABLE pfma_failure_modes (
  id            INT PRIMARY KEY AUTO_INCREMENT,
  process_step  VARCHAR(100),  -- 过程步骤（如：热处理-淬火）
  product_id    INT,           -- 关联 products.id（可为空，表示通用失效）
  failure_mode  TEXT,          -- 失效模式（如：氢脆断裂）
  failure_effect TEXT,         -- 失效影响（如：服役中断裂，导致安全事故）
  failure_cause  TEXT,         -- 失效原因（如：酸洗后未及时除氢）
  severity      TINYINT,       -- 严重度 S (1-10)
  category      VARCHAR(50),   -- 失效分类（尺寸/力学/材料/表面/装配）
  tags          JSON,          -- 标签（便于跨维度检索）
  source        VARCHAR(200),  -- 数据来源（客诉/内审/行业标准）
  status        TINYINT DEFAULT 1,
  created_at    DATETIME,
  updated_at    DATETIME
);
```

---

### 模块 B：风险评估（Risk Assessment - RPN Calculation）

**功能描述**：对每条失效模式进行量化风险评估，自动计算 RPN，生成风险排行榜。

| 功能点 | 说明 |
|---|---|
| B-1 RPN 自动计算 | RPN = S（严重度）× O（发生频度）× D（探测度），三项均为 1-10 |
| B-2 评估录入界面 | 每条失效模式可录入多次评估记录（支持版本对比） |
| B-3 风险优先队列 | 按 RPN 降序排列，高风险项（RPN > 100）自动高亮预警 |
| B-4 风险矩阵图 | 以 S × O 二维热力图展示风险分布 |
| B-5 趋势对比 | 同一失效模式前后两次评估的 RPN 变化趋势（评估改进效果） |
| B-6 批量评估 | 支持对同一过程步骤下的所有失效模式批量评分 |

**RPN 评估参考维度**：

| 维度 | 1分（低） | 5分（中） | 10分（高） |
|---|---|---|---|
| **S 严重度** | 外观轻微缺陷 | 功能降级，客户不满 | 安全失效，人身伤害 |
| **O 发生频度** | <1/100,000 次 | 1/1,000 次 | >1/10 次 |
| **D 探测度** | 100% 可检出 | 50% 可检出 | 无法检出 |

**数据结构（新建数据表 `pfma_assessments`）**：

```sql
CREATE TABLE pfma_assessments (
  id           INT PRIMARY KEY AUTO_INCREMENT,
  failure_id   INT,            -- 关联 pfma_failure_modes.id
  severity     TINYINT,        -- S (1-10)
  occurrence   TINYINT,        -- O (1-10)
  detection    TINYINT,        -- D (1-10)
  rpn          SMALLINT,       -- RPN = S × O × D（自动计算）
  assessor_id  INT,            -- 评估人（关联 secrets/users 表）
  assessment_date DATE,
  notes        TEXT,
  version      VARCHAR(20),    -- 评估版本号（如 v1.0/v2.0）
  created_at   DATETIME
);
```

---

### 模块 C：改进措施跟踪（Corrective Action Tracking）

**功能描述**：对高风险失效（RPN > 阈值）触发改进任务，追踪措施落实情况直至关闭。

| 功能点 | 说明 |
|---|---|
| C-1 改进措施录入 | 针对高RPN失效，录入：预防措施、探测措施、负责人、计划完成日期 |
| C-2 任务状态管理 | 状态流转：待开始 → 进行中 → 验证中 → 已关闭 |
| C-3 有效性验证 | 改进后重新评估 RPN，系统自动对比改进前后变化 |
| C-4 逾期预警 | 改进任务超过计划日期未关闭时，自动推送站内提醒 |
| C-5 改进历史归档 | 所有改进记录归档，支持审计查询 |
| C-6 措施模板库 | 积累常用改进措施模板，可复用（如"酸洗后8小时内完成除氢"） |

**数据结构（新建数据表 `pfma_actions`）**：

```sql
CREATE TABLE pfma_actions (
  id              INT PRIMARY KEY AUTO_INCREMENT,
  assessment_id   INT,           -- 关联 pfma_assessments.id
  action_type     TINYINT,       -- 措施类型：1预防 2探测
  description     TEXT,          -- 改进措施描述
  responsible_id  INT,           -- 负责人
  plan_date       DATE,          -- 计划完成日期
  actual_date     DATE,          -- 实际完成日期
  status          TINYINT,       -- 1待开始 2进行中 3验证中 4已关闭
  verification    TEXT,          -- 有效性验证记录
  post_rpn        SMALLINT,      -- 改进后 RPN
  created_at      DATETIME,
  updated_at      DATETIME
);
```

---

### 模块 D：知识库联动（Knowledge Linkage）

**功能描述**：PFMA数据与现有标准知识库双向打通，让失效分析有标准依据，标准查询有失效案例参考。

| 功能点 | 说明 |
|---|---|
| D-1 失效→标准跳转 | 在失效详情页，一键跳转至关联的 `products` 详情（如：氢脆失效 → 跳转到对应表面处理标准） |
| D-2 标准→失效案例 | 在现有 books 前端产品详情页，新增"相关失效案例"侧边栏模块 |
| D-3 关键词图谱 | 将失效模式的 `tags` 字段与标准的 `extension` 字段共享标签体系，构成初步知识网络 |
| D-4 AI辅助分类 | （中期）对新录入的失效描述文本，调用 AI 接口自动推荐：过程分类、严重度评分、关联标准 |

---

## 三、系统架构方案

### 3.1 后端扩展

在现有 `interface/standards` 的基础上，新增 PFMA 模块：

```
src/app/
├── model/
│   ├── pfmaFailureModes.js   ← 新增
│   ├── pfmaAssessments.js    ← 新增
│   └── pfmaActions.js        ← 新增
├── controller/
│   └── pfma.js               ← 新增（含 list/detail/save/assess/action 方法）
├── helper/
│   └── pfma.js               ← 新增（数据查询与 RPN 自动计算逻辑）
```

**RPN 自动计算逻辑（helper）**：

```javascript
// src/app/helper/pfma.js
function calculateRPN(severity, occurrence, detection) {
  return severity * occurrence * detection;
}

async function assessmentSave(data) {
  data.rpn = calculateRPN(data.severity, data.occurrence, data.detection);
  return await pfmaAssessmentsModel.save(data);
}
```

### 3.2 前端扩展

新增路由 `/standards/pfma`，在现有 Vue 3 + Element Plus 框架下：

```
views/standards/pfma/
├── index.vue                  ← 失效模式库（列表+搜索）
├── detail.vue                 ← 失效详情 + 评估历史 + 改进跟踪
├── assessment.vue             ← RPN 评估录入界面
├── action.vue                 ← 改进措施管理
├── dashboard.vue              ← 风险仪表盘（RPN排行榜 + 趋势图 + 热力矩阵）
└── components/
    ├── RpnMatrix.vue          ← 风险矩阵热力图（echarts）
    ├── ActionTimeline.vue     ← 改进措施时间线
    └── FailureSearchBar.vue   ← 高级搜索
```

---

## 四、功能优先级排期建议

| 优先级 | 功能模块 | 工期估算 |
|---|---|---|
| P0（MVP） | A-1~A-4 失效录入 + B-1~B-3 RPN计算 + 基础列表 | 2-3周 |
| P1 | C-1~C-4 改进措施跟踪 + 仪表盘 | 2周 |
| P2 | D-1~D-3 知识库联动 + 批量导入 | 2周 |
| P3 | D-4 AI辅助分类 + 高级统计报表 | 4周 |

---

*文档结束*
