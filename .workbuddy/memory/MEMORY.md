# MEMORY.md — 项目长期记忆

## 项目：史特牢紧固件数字化平台

**目标**：从「标准查阅」升级为「计算+管理+教育」三位一体的工程决策支持平台

### 技术栈
- 后端：`/Users/wbtrex/website/localServer/node/edtib/interface/standards`
  - Node.js + RPC + knex ORM（MySQL）
  - 多进程支持（cluster），JWT/token中间件
  - 核心model：products/formulas/variables/shapes/categories/standards/interpretations
- 前端：`/Users/wbtrex/website/localServer/node/edtib/client/books/web`
  - Vue 3 + TypeScript + Element Plus + Vite + PWA
  - API路径：`/front/standards/*`

### 关键数据字段（已扫描）
- `products.parameters`(JSON)：螺纹规格、头部尺寸，计算工具的核心入参
- `products.grade`(String)：性能等级（4.8/6.8/8.8/10.9/12.9），强度计算分路参数
- `products.diameter_length`(JSON)：规格-长度矩阵，千支重批量计算入参
- `products.formulas`(JSON)：公式引用列表，已预埋产品-公式关联
- `formulas.columnar`(String)：公式表达式字符串，可用 mathjs 直接解析执行
- `variables.variable`(String)：符号定义库（d、p、As等），构成公式符号字典

### 已生成文档（2026-04-07）
- `docs/01_fastener-toolset-technical-spec.md` — 工具集技术方案
- `docs/02_pfma-system-feature-list.md` — PFMA系统功能清单
- `docs/03_industry-service-roadmap.md` — 服务化路线图
- `docs/04_product-market-strategy.md` — 产品市场化策略分析（2026-04-07）

### 产品市场化核心决策
- **第一个商业产品**：紧固件工程计算 SaaS 工具集（建议命名：螺定 SUREDIM）
- **差异化定位**：唯一原生支持 GB/ISO/DIN 三套标准的中文在线计算工具
- **MVP 优先工具**：千支重计算器 + 强度校验器（资产复用度最高）
- **付费触发点**：PDF 计算报告导出（无水印）
- **定价方案**：个人 199/年，专业 499/年，企业 2,999/年（10席位+API）
- **第一阶段砍掉**：PFMA、AI问答、知识图谱（防止资源分散）
- **核心壁垒**：GB/ISO/DIN 标准数据库积累（竞品 2-3 年无法复制）

### 全新系统规划（2026-04-07）
- 系统名称：**扣环（COUPLER）**，独立新系统（非旧系统升级）
- 三类核心用户：企业采购工程师 / 生产厂家 / 贸易商
- 9大模块：标准件库、工程工具、产品目录、询价交易、来图定制、供应商管理、质检追溯、库存管理、用户权限
- 技术栈：Vue3+Fastify+Prisma+MySQL8+Redis+Meilisearch
- 文档：`docs/05_new-system-requirements-architecture.md`

### 用户偏好
- 需要具体的代码路径和字段级分析，不接受宽泛建议
- 优先利用现有代码资产，而不是推倒重来
- 输出三阶段路线图：工具化 → 知识化 → 服务化

### 新增文件（2026-04-08）
- `CODEBUDDY.md` — AI助手指南，包含架构概览、技术栈、MVP范围、业务流程
- `docs/plans/2026-04-08-prototype-design.md` — COUPLER双角色原型设计文档
- `prototype.html` — 1000行单文件HTML原型，完整实现12个页面的双角色可点击演示原型
