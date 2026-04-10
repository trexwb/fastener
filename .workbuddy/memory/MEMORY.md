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

### 螺定 SUREDIM 项目（新）

**产品定位**：Electron 离线桌面工具，螺栓标准查询 + 千支重计算，面向个人工程师/贸易商

**MVP 范围**：
- 客户端：标准查询 + 千支重计算器
- 管理后台：数据增删改查 + 序列号管理 + 付费用户记录
- 落地页：下载页 + 版本说明 + 升级日志

**三原型文件**（已全部完成）：
- `stdic-client.html` — 桌面客户端，含授权栏/搜索/筛选/详情/计算器/对比/解锁弹窗
- `stdic-admin.html` — 管理后台，含登录/仪表盘/标准CRUD/产品CRUD/序列号生成/用户管理
- `stdic-landing.html` — 落地页，含 Hero/Features/Coverage/Pricing/Changelog

**技术选型**：Electron + sql.js (WASM SQLite) + 离线序列号激活

**商业模型**：
- 免费：GB 50 + ISO 30 + DIN 20 = 100 条
- 专业版 ¥199/年（不限数据 + 对比分析）
- 企业版 ¥2,999/年（10席位 + API）

**设计语言**：精密工程美学（#2563eb 主色 + JetBrains Mono + Inter 字体），三个原型统一

**设计规范**：`docs/06_coupler-ui-design-spec.md`（已更新附录C）

### 新增文件（2026-04-10 下午）

**docs/07_visual-correction-report.md** — 视觉修正案（三原型所有修正项清单）
**docs/08_core-css-system.md** — 核心 CSS 系统（Design Token + 组件规格代码块）
**docs/09_interaction-specification.md** — 交互说明书（状态机 + 响应规范）

### UI 优化完成状态（2026-04-10）
- ✅ 设计 Token 注入（三端完整）
- ✅ 小程序交互完善（20项 P0）
- ✅ 后台交互完善（15项 P0）
- ✅ 桌面端交互完善（10项 P1）
- ✅ 批量选择 JS 逻辑（stdic-admin.html）
- ✅ 离线指示器 JS 逻辑（stdic-client.html）
- ✅ 扫码状态机 JS（stdic-miniapp.html，已有）
- ✅ 专家解读面板接入详情页（stdic-client.html — 4种类型卡片动态渲染）
- 需要具体的代码路径和字段级分析，不接受宽泛建议
- 优先利用现有代码资产，而不是推倒重来
- 输出三阶段路线图：工具化 → 知识化 → 服务化

### 产品定位（2026-04-10 重大更新）
- **起点定位**：「紧固件从业者的标准知识库——数据经专业校正，每条标准附专家解读」
- **终极愿景**：「紧固件行业从选型到交付的全链路数字化工作台」
- **核心差异化**：数据壁垒（专业纠正，部分已申请国标/企标）+ 专家解读 + 供应链匹配 + 多产品组合
- **战略路径**：产品驱动，服务层后置；专家时间用于内容生产，不做高频服务交付
- **种子用户策略**：邀请贸易公司老客户10-20人内测
- **MVP边界**：标准数据库（GB/ISO/DIN，每条附专家解读）+ 全文搜索 + 收藏夹；禁止碰交易/PFMEA/供应商管理
- **调研报告**：`docs/plans/2026-04-10-product-research-summary.md`

### 新增文件（2026-04-08）
- `CODEBUDDY.md` — AI助手指南，包含架构概览、技术栈、MVP范围、业务流程
- `docs/plans/2026-04-08-prototype-design.md` — COUPLER双角色原型设计文档
- `prototype.html` — 1000行单文件HTML原型，完整实现12个页面的双角色可点击演示原型
