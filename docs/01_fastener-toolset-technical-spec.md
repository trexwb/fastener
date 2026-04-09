# 紧固件工具集技术方案

> 版本：v1.0 | 日期：2026-04-07 | 作者：趋势研究员

---

## 一、现有代码资产审计摘要

### 1.1 后端数据模型清单（`interface/standards`）

| 模型文件 | 数据表 | 核心字段 | 计算工具可用性 |
|---|---|---|---|
| `products.js` | `{prefix}products` | `parameters`(JSON)、`tolerance`(JSON)、`diameter_length`(JSON)、`formulas`(JSON)、`grade`(String) | ★★★★ 核心入参来源 |
| `formulas.js` | `{prefix}formulas` | `type`(Int)、`shape_id`(Int)、`code`(String)、`columnar`(String)、`remarks`(JSON) | ★★★★ 公式定义库 |
| `variables.js` | `{prefix}variables` | `type`(String)、`code`(String)、`variable`(String) | ★★★ 变量符号库 |
| `shapes.js` | `{prefix}shapes` | `location`(Int)、`names`(JSON)、`abbreviation`(String)、`remarks`(JSON) | ★★★ 形状分类 |
| `categories.js` | `{prefix}categories` | `parent_id`(Int)、`names`(JSON)、`abbreviation`(String) | ★★ 产品分类树 |
| `standards.js` | `{prefix}standards` | `names`(JSON)、`abbreviation`(String)、`total`(Int) | ★★ 标准体系（GB/ISO/DIN） |

### 1.2 关键发现

1. **`products.parameters`（JSON字段）**：存储螺纹规格（M值、螺距）、头部尺寸、扳手尺寸等参数表，**是所有计算工具的原始输入**。
2. **`products.formulas`（JSON字段）**：已在产品层面关联了公式引用，说明公式-产品绑定关系已在数据库层面预埋。
3. **`products.grade`（String字段）**：存储性能等级（4.8/6.8/8.8/10.9/12.9），是强度计算的**关键分路参数**。
4. **`products.tolerance`（JSON字段）**：公差带数据（如 6H/6g），支撑螺纹配合校验计算。
5. **`products.diameter_length`（JSON字段）**：规格-长度组合矩阵，支撑千支重批量计算。
6. **`formulas.columnar`（String字段）**：疑似存储公式字符串（如 `d1 * d2 * 0.785`），**可直接复用为计算引擎的表达式解析输入**。
7. **`variables.variable`（String字段）**：存储变量符号（如 `d`、`p`、`As`），构成公式的符号字典。

### 1.3 现有API接口（可直接复用）

后端通过 `hprose` RPC 暴露以下接口，前端 `/front/standards/` 路由已对接：

```
formulasAll()       → 获取全量公式定义（含 columnar 表达式）
variablesAll()      → 获取全量变量符号定义
productsDetail(id)  → 获取单个产品完整参数（含 parameters/formulas/grade）
productsList(filter) → 按分类/规格筛选产品列表
```

---

## 二、力学计算模块：算法需求与接口设计

### 2.1 千支重计算模块

**物理含义**：1000支紧固件的质量（kg），是采购报价和库存管理的核心指标。

**计算公式逻辑框架**：

```
千支重 = (V_body × ρ × 1000) / 1,000,000   [单位: kg/千支]

其中：
  V_body = 螺杆体积 + 头部体积 + （垫圈/其他附件体积）
  螺杆体积 (V_rod) = π/4 × d² × L_eff  [粗略近似，精确需减去螺纹牙型体积]
  头部体积根据 shape_id 对应形状公式计算

ρ (密度库，g/cm³)：
  碳钢: 7.85
  不锈钢 304: 7.93
  不锈钢 316: 7.98
  铝合金: 2.70
  铜: 8.90
```

**数据来源映射**：

| 计算参数 | 数据来源 | 字段路径 |
|---|---|---|
| 公称直径 `d` | `products.parameters` | `parameters.d` 或 `parameters.nominal_diameter` |
| 有效长度 `L` | `products.diameter_length` | 规格矩阵中的 length 枚举 |
| 头部形状公式 | `formulas.columnar` + `shapes.id` | 通过 `formulas.shape_id` 关联 |
| 材料密度 | 新增密度库（建议加入 `variables` 表，`type='density'`） | `variables.variable` |

**新增后端API建议**：

```javascript
// 新增文件：src/app/controller/calculator.js

async function calcWeight(data, context) {
  // data: { product_id, material, lengths: [] }
  const product = await productsHelper.getId(data.product_id);
  // 从 product.parameters 取 d、p 等参数
  // 从 product.diameter_length 取长度枚举
  // 查询对应 shape_id 下的 formulas.columnar 获取体积公式
  // 执行表达式解析（建议使用 mathjs eval）
  // 叠加密度换算
  return { results: [...] }; // 按长度返回千支重数组
}
```

---

### 2.2 强度校验模块

**涉及公式（基于 ISO 898-1 / GB/T 3098.1）**：

#### 2.2.1 有效截面积（应力截面积）

```
As = π/4 × ((d2 + d3) / 2)²

d2 = 中径 = d - 0.6495 × p
d3 = 小径 = d - 1.2269 × p
p  = 螺距（从 products.parameters 读取）
```

#### 2.2.2 最小破坏拉力

```
F_break = As × Rm_min

性能等级 → Rm_min 映射（MPa）：
  4.8  → 420
  6.8  → 600
  8.8  → 800（d≤16mm） / 830（d>16mm）
  10.9 → 1040
  12.9 → 1220
```

#### 2.2.3 最小破坏扭矩

```
T_break = 0.5 × F_break × d × μ_eff

μ_eff = 综合摩擦系数（通常取 0.10~0.16，与表面处理相关）
```

#### 2.2.4 推荐装配扭矩

```
T_install = K × d × F_clamp

K = 扭矩系数（光滑：0.12~0.13；镀锌：0.13~0.15；磷化：0.12~0.14）
F_clamp = 目标夹紧力 = 0.7 × As × Rp0.2_min（预紧力取屈服强度70%）

Rp0.2_min 映射（MPa）：
  8.8  → 640
  10.9 → 940
  12.9 → 1100
```

**数据来源映射**：

| 计算参数 | 来源 |
|---|---|
| `d`（公称直径）、`p`（螺距） | `products.parameters` |
| `grade`（性能等级） | `products.grade` |
| `μ` / `K`（摩擦系数） | 新增 `variables` 记录（type='friction'）或前端用户输入 |

**新增后端API建议**：

```javascript
// 新增至 src/app/controller/calculator.js

async function calcStrength(data, context) {
  // data: { product_id, friction_coef?, target_clamp_force? }
  // 1. 取 parameters.d, parameters.p, grade
  // 2. 计算 As
  // 3. 根据 grade 查性能等级映射表（建议存入 variables, type='grade_strength'）
  // 4. 返回 { As, F_break, T_break, T_install_recommend, F_clamp }
}

async function calcSafeLoad(data, context) {
  // 螺母保证载荷与螺栓预紧力匹配校验
  // data: { bolt_product_id, nut_product_id }
  // 分别取两者 As，校验 nut.Sp >= bolt.F_clamp
}
```

---

### 2.3 API模块集成方案

在现有路由中间件 `middlewareRoute.controller(server)` 机制下，新增计算路由：

```javascript
// src/middleware/route.js 追加（或新建 src/app/controller/calculator.js）

// 前端调用路径：/front/standards/calcWeight
// 前端调用路径：/front/standards/calcStrength
// 前端调用路径：/front/standards/calcSafeLoad
// 前端调用路径：/front/standards/calcAssemblyTorque
```

无需修改现有 hprose 服务器框架，只需在 route 注册新 controller 方法即可。

---

## 三、前端响应式计算界面设计

### 3.1 基于现有前端架构

当前前端（Vue 3 + TypeScript + Element Plus）的 `views/standards/products/` 已有完整的分类筛选 + 列表展示组件体系，新增计算工具应在此基础上**并列扩展而非侵入改造**。

### 3.2 计算工具页面结构（建议路由：`/standards/calculator`）

```
views/standards/calculator/
├── index.vue               ← 工具集入口（Tab切换：千支重/强度校验/装配扭矩/安全载荷）
├── components/
│   ├── WeightCalc.vue      ← 千支重计算器
│   ├── StrengthCalc.vue    ← 强度校验计算器
│   ├── TorqueCalc.vue      ← 装配扭矩计算器
│   └── SafeLoadCalc.vue    ← 安全载荷匹配校验
└── hooks/
    ├── useWeightCalc.ts    ← 千支重计算逻辑 hook
    ├── useStrengthCalc.ts  ← 强度计算逻辑 hook
    └── useFormulaEngine.ts ← 表达式解析引擎（mathjs）
```

### 3.3 响应式计算交互范式

```vue
<!-- WeightCalc.vue 核心交互逻辑伪代码 -->
<template>
  <!-- Step1: 选产品标准 → 自动拉取 productsDetail -->
  <ProductSelector @selected="onProductSelected" />
  
  <!-- Step2: 选材料密度（从 variables API 读取，type='density'） -->
  <MaterialSelector :materials="densityOptions" v-model="material" />
  
  <!-- Step3: 勾选目标规格长度（从 product.diameter_length 渲染） -->
  <LengthMatrix :lengths="product.diameter_length" v-model:selected="selectedLengths" />
  
  <!-- Step4: 实时计算结果（watch selectedLengths 自动触发） -->
  <ResultTable :results="weightResults" exportable />
</template>
```

**关键原则**：
- 参数全部从 API 读取，**不允许硬编码**材料密度和性能等级到前端
- 计算公式在**后端执行**（安全、版本可控），前端仅传参数、展示结果
- 支持结果**导出为 Excel**（复用现有 `src/assets/*.xlsx` 模板机制）

---

## 四、现有 `formulas.columnar` 字段复用方案

这是最值得关注的存量资产：`columnar` 字段已存储公式字符串，**可直接作为服务端计算引擎的表达式**。

建议方案：

```javascript
// 安装 mathjs
const { evaluate } = require('mathjs');

// 从数据库取 formula.columnar = "pi/4 * ((d2 + d3) / 2)^2"
// 从 variables 表构建符号映射 scope = { d2: 3.14, d3: 2.77, ... }
const result = evaluate(formula.columnar, scope);
```

这样**公式管理完全在后台数据库中**，工程师可通过现有管理后台修改公式，无需发版。

---

*文档结束*
