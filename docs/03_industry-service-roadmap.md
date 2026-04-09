# 行业服务化路线图

> 版本：v1.0 | 日期：2026-04-07 | 作者：趋势研究员

---

## 一、战略转型定位

**从「查阅型知识库」升级为「工程决策支持平台」**

```
当前状态（0→1）               目标状态（1→10）
────────────────────         ────────────────────────────────────
单纯展示：标准 PDF 查阅   →   计算：工程参数在线计算工具集
孤立标准：各标准独立存在  →   连接：知识图谱 + 跨标准关联
被动等待：用户主动检索    →   主动推送：AI 每日技术简报 + 自动答疑
单端产品：PC Web 展示     →   多端服务：Web + 小程序 + API 对外开放
```

---

## 二、前端 books 重构方案（组件化工具箱）

### 2.1 当前架构问题

通过代码扫描（`books/web/src/views/standards/products/`）发现：
- 当前为**单页列表模式**：产品查询 → 详情展示，交互链路止步于"看"
- `vabAutoComponents/` 下 29 个自动生成组件，存在**高度重复的展示逻辑**，缺乏抽象
- API 层（`src/api/`）14 个文件已有良好分离，是重构的稳定基础

### 2.2 重构目标：模块化「工具箱」架构

```
src/views/
├── standards/               ← 现有，保留
│   ├── products/            ← 现有，保留（标准查询入口）
│   ├── calculator/          ← 新增：工具集模块
│   │   ├── index.vue        ← Tab导航：重量/强度/扭矩/安全载荷
│   │   ├── WeightCalc.vue
│   │   ├── StrengthCalc.vue
│   │   ├── TorqueCalc.vue
│   │   └── SafeLoadCalc.vue
│   ├── pfma/                ← 新增：失效管理
│   ├── knowledge/           ← 新增：知识图谱
│   │   ├── graph.vue        ← 知识图谱可视化（推荐: AntV G6）
│   │   └── explorer.vue     ← 标准关联链探索器
│   └── expert/              ← 新增：AI 专家问答
│       ├── index.vue        ← 专家回复引擎入口
│       └── daily.vue        ← 每日技术推送订阅
│
└── library/                 ← 现有组件库（已有 44个.vue，可扩展）
    ├── ProductCard.vue      ← 新增：产品卡片通用组件
    ├── ParameterTable.vue   ← 新增：参数表格通用组件
    ├── FormulaDisplay.vue   ← 新增：公式渲染（LaTeX/KaTeX）
    └── ResultExporter.vue   ← 新增：计算结果导出通用组件
```

### 2.3 关键重构原则

**原则 1：从「页面」到「能力」**

将计算能力封装为可独立调用的 Composable：

```typescript
// src/views/standards/calculator/hooks/useStrengthCalc.ts
export function useStrengthCalc() {
  const productId = ref<number | null>(null)
  const frictionCoef = ref(0.13)
  const results = reactive({ As: 0, F_break: 0, T_install: 0 })
  
  const calculate = async () => {
    const res = await calcStrengthAPI({ product_id: productId.value, friction_coef: frictionCoef.value })
    Object.assign(results, res.data)
  }
  
  // 当 productId 变化时自动重新计算
  watch(productId, calculate)
  
  return { productId, frictionCoef, results, calculate }
}
```

**原则 2：参数从数据库驱动，不硬编码**

- 密度、性能等级、摩擦系数→全部从 `variables` API 读取，后台可配置
- 公式表达式→从 `formulas` API 读取 `columnar` 字段

**原则 3：渐进增强，不破坏现有流程**

计算工具作为产品详情页的「延伸入口」，在现有 `products/detail` 底部增加「一键计算」按钮，保持原有查阅流程不变。

---

## 三、知识图谱化方案

### 3.1 知识链设计思路

将孤立的标准数据点，串联成**工程决策链**：

```
示例链路：氢脆问题排查链
────────────────────────────────────────────────────────────
螺栓 10.9 级 → 性能等级要求 → 需热处理 → 淬火+回火 
    ↓
  表面处理选择 → [禁止酸洗镀锌] → [推荐机械镀锌 / 达克罗]
    ↓
  标准限制 → GB/T 5267.1（机械镀锌标准）
    ↓
  相关失效模式（PFMA联动） → 氢脆断裂案例库
```

### 3.2 知识图谱技术实现

**数据来源**（全部来自现有数据库）：

| 节点类型 | 数据来源 |
|---|---|
| 产品节点 | `products` 表（`names`, `grade`, `code`） |
| 标准节点 | `standards` 表（`abbreviation`, `names`） |
| 分类节点 | `categories` 表（树形结构） |
| 属性节点 | `parameters` JSON字段中的关键字段 |
| 失效节点 | `pfma_failure_modes`（新建） |
| 知识节点 | `interpretations` 表（现有解释库） |

**边关系定义**：

```javascript
// 边类型设计
const edgeTypes = {
  'belongs_to':    '产品 → 标准',
  'has_category':  '产品 → 分类',
  'has_formula':   '产品 → 公式',
  'causes_failure':'工艺 → 失效模式',
  'restricts':     '标准 → 工艺限制',
  'references':    '标准 → 标准（参考关系）',
  'interprets':    '标准条款 → 解释说明'
}
```

**前端可视化**：推荐使用 `AntV G6`（比 D3.js 易用，支持 Vue 3）

```typescript
// views/standards/knowledge/graph.vue
import G6 from '@antv/g6'

// 节点数据从后端 API 聚合获取
// 后端新增：GET /front/standards/knowledgeGraph?product_id=xxx&depth=2
```

### 3.3 后端知识图谱 API

新增一个聚合接口，以某产品为中心，返回 N 跳关联数据：

```javascript
// src/app/controller/knowledge.js（新增）
async function knowledgeGraph(data, context) {
  // data: { center_type: 'product'|'failure'|'standard', center_id, depth: 1|2 }
  // 1. 取中心节点数据
  // 2. 递归取关联节点（products→formulas→shapes；products→pfma→actions）
  // 3. 返回 { nodes: [], edges: [] } 标准图数据格式
}
```

---

## 四、AI 专家回复引擎

### 4.1 定位

**不是通用聊天机器人**，而是**有明确技术边界的专业问答系统**：

- 回答范围：紧固件选型、力学计算、标准解读、失效分析、装配工艺
- 语气风格：模拟资深紧固件工程师（专业、简洁、引用标准依据）
- 数据来源：先检索本地知识库，再生成回答（RAG 架构）

### 4.2 RAG 技术架构

```
用户提问
    ↓
[意图识别层]
 识别问题类型：选型咨询/强度计算/失效诊断/标准查询
    ↓
[检索层] ── 向量检索（products/interpretations/pfma_failure_modes）
         ── 精确匹配（standards API: 标准号直查）
    ↓
[上下文构建] 将检索到的标准内容/公式/失效案例作为 context 注入
    ↓
[LLM生成层] 使用注入了专业 prompt 的大模型生成回答
    ↓
[答案后处理] 附加引用来源（标准号+条款）、相关工具入口跳转
```

### 4.3 专家 System Prompt 框架

```
你是一位拥有20年经验的紧固件工程师，精通 GB、ISO、DIN、ASTM 标准体系。
回答问题时：
1. 优先引用检索到的标准原文和参数数据（已提供在 context 中）
2. 数字和公式必须明确说明依据（如：依据 GB/T 3098.1-2010 表1）
3. 对于无法确定的问题，明确说明建议实测验证，不猜测
4. 使用技术术语，但对复杂概念给出工程实例辅助理解
5. 如果问题涉及安全相关的紧固件（如航空/汽车），特别强调验证要求
```

### 4.4 每日技术推送脚本

利用现有后端 `schedule`（定时任务）模块，新增每日推送生成任务：

```javascript
// src/app/schedule/dailyPush.js（新增）

async function generateDailyTechBrief() {
  // 1. 从数据库取当日热点标准（按 products_hits 表热度排序）
  // 2. 从 pfma 库取近期新增失效案例（最近7天）
  // 3. 随机取1个"知识点"：从 interpretations 表取一条解释记录
  // 4. 调用 LLM API 生成500字的技术简报（Markdown格式）
  // 5. 存入 docs 表，status=1，供前端 /expert/daily 页面展示
  // 6. 可选：推送至订阅用户的微信/邮件
}

// 定时任务配置：每天 07:30 执行
module.exports = {
  schedule: { cron: '0 30 7 * * *' },
  async task() {
    await generateDailyTechBrief();
  }
}
```

---

## 五、整体服务化路线图

### Phase 1（0~3个月）：工具化

| 里程碑 | 具体目标 | 关键交付 |
|---|---|---|
| M1-基础工具上线 | 千支重计算 + 强度校验可用 | `calculator` 模块 MVP |
| M1-数据标准化 | `variables` 表补充密度/等级数据 | 后台配置页面 |
| M2-PFMA MVP | 失效录入 + RPN 计算可用 | PFMA 模块 MVP |

### Phase 2（3~6个月）：知识化

| 里程碑 | 具体目标 | 关键交付 |
|---|---|---|
| M3-知识图谱 | 产品-标准-失效三类节点可视化 | `knowledge/graph.vue` |
| M4-AI问答基础版 | 基于标准库的 RAG 问答上线 | `expert/index.vue` |
| M4-每日推送 | 每日技术简报自动生成 | `schedule/dailyPush.js` |

### Phase 3（6~12个月）：服务化

| 里程碑 | 具体目标 | 关键交付 |
|---|---|---|
| M5-API开放 | 向企业客户开放计算工具 API（付费） | API 文档 + 鉴权扩展 |
| M6-小程序端 | 计算工具 + 标准查询移动端化 | 微信小程序版本 |
| M6-订阅服务 | 技术简报邮件/微信订阅 | 订阅管理模块 |
| M7-行业社区 | 失效案例用户贡献 + 专家审核机制 | 内容审核工作流 |

---

## 六、技术选型建议

| 场景 | 推荐方案 | 理由 |
|---|---|---|
| 公式计算引擎 | `mathjs`（Node.js） | 支持表达式解析，与 `columnar` 字段天然配合 |
| 知识图谱可视化 | `AntV G6` | Vue 3 友好，文档完善，性能优于 D3.js |
| AI 大模型接入 | 腾讯混元 / 阿里通义（国内部署） | 合规性好，支持私有化知识库 |
| 向量数据库 | Milvus / Qdrant | 支撑 RAG 检索，开源可私有部署 |
| 公式渲染（前端） | KaTeX | 轻量级数学公式渲染，性能优于 MathJax |
| 数据导出 | `xlsx`（SheetJS） | 复用现有 `src/assets/*.xlsx` 模板 |

---

*文档结束*
