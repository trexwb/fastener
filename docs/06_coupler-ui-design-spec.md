# 螺定 SUREDIM — 产品设计规范 v1.1

> 版本：1.1 | 日期：2026-04-10 | 状态：原型阶段
> 说明：本规范涵盖螺定 SUREDIM 小程序、桌面客户端、管理后台三个原型。PC 官网（内容站）待重新设计。

---

## 1. 设计理念

**核心理念：精密工程美学**

工业 B2B 采购平台的核心用户是**采购工程师**和**技术人员**。他们需要：
- 快速定位规格（而非被花哨动画分散注意力）
- 高效处理多任务（侧边栏永远可见）
- 数据驱动决策（清晰的数字和状态）

设计语言从三个维度提炼：
- **精密感**：六边形几何纹理暗喻紧固件的机械感
- **信任感**：深色侧边栏 + 暖白内容区的专业配色
- **效率感**：卡片式布局 + 实时计算工具的即时反馈

---

## 2. 色彩系统

### 主色板

```
Primary Blue    #2563EB   主按钮、链接、选中态、图标高亮
Primary Light   #EFF6FF   选中背景、hover 背景
Primary Dark    #1D4ED8   主按钮 hover
Primary Glow    rgba(37,99,235,0.15)  输入框 focus ring
```

### 辅助色板

```
Accent Orange   #F59E0B   警告徽章、待处理状态
Success Green   #10B981   成功、完成状态
Warning Amber   #F59E0B   警告状态
Error Red      #EF4444   错误、紧急状态
```

### 中性色板

```
Background     #F8F9FC   页面背景
Surface        #FFFFFF   卡片/面板背景
Border         #E8ECF0   边框线
Border Light   #F0F2F5   浅分割线

Text Primary   #111827   标题、主要文字
Text Secondary #6B7280   辅助说明文字
Text Tertiary  #9CA3AF   占位符、次要提示
Text Inverse   #FFFFFF   深色背景上的文字
```

### 标准色标签（业务专属）

```
GB 国标        #DC2626 红色背景 / #FEE2E2 浅红
ISO 国际       #2563EB 蓝色背景 / #DBEAFE 浅蓝
DIN 德国       #6B7280 灰色背景 / #F3F4F6 浅灰
ANSI 美标      #7C3AED 紫色背景 / #EDE9FE 浅紫
```

---

## 3. 字体系统

### 字体家族

```css
--font-sans: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
--font-mono: 'JetBrains Mono', 'Fira Code', monospace;
```

### 字体比例

| 级别 | 字号 | 字重 | 用途 | 行高 |
|------|------|------|------|------|
| Display | 30px | 800 | 大数字统计 | 1 |
| H1 | 24px | 700 | 页面标题 | 1.3 |
| H2 | 20px | 700 | 面板标题 | 1.3 |
| H3 | 18px | 700 | 区块标题 | 1.4 |
| Body L | 16px | 400 | 正文（备用） | 1.6 |
| Body | 14px | 400/500 | 正文、输入 | 1.6 |
| Small | 13px | 400/500 | 辅助说明 | 1.5 |
| XS | 12px | 400/500/600 | 标签、徽章 | 1.4 |
| Caption | 11px | 500/600 | 小标签 | 1.3 |

### Inter 字体规则

- 标题使用 `-0.02em` 字间距（更紧凑）
- 数字统计使用 `-0.03em` 字间距（更挺拔）
- 代码/规格参数使用 JetBrains Mono（等宽易读）

---

## 4. 间距系统

基础单位：**4px**

| Token | 值 | 用途 |
|-------|-----|------|
| space-1 | 4px | 紧凑间距 |
| space-2 | 8px | 元素内部微调 |
| space-3 | 12px | 标签到输入框 |
| space-4 | 16px | 标准内边距 |
| space-5 | 20px | 卡片内边距 |
| space-6 | 24px | 区块间距 |
| space-8 | 32px | 大区块间距 |
| space-12 | 48px | 页面级别间距 |
| space-16 | 64px | 超大间距 |

---

## 5. 圆角系统

| Token | 值 | 用途 |
|-------|-----|------|
| radius-xs | 4px | 标签内文字、kbd |
| radius-sm | 6px | 按钮、输入框、导航项 |
| radius-md | 10px | 卡片、标准面板 |
| radius-lg | 16px | 大面板、模态框 |
| radius-xl | 20px | 页面主面板 |
| radius-full | 9999px | 徽章、头像、pill |

---

## 6. 阴影系统

```css
shadow-xs:  0 1px 2px rgba(0,0,0,0.05);
shadow-sm:  0 1px 3px rgba(0,0,0,0.08), 0 1px 2px rgba(0,0,0,0.04);
shadow-md:  0 4px 6px rgba(0,0,0,0.06), 0 2px 4px rgba(0,0,0,0.04);
shadow-lg:  0 10px 15px rgba(0,0,0,0.08), 0 4px 6px rgba(0,0,0,0.04);
shadow-xl:  0 20px 25px rgba(0,0,0,0.08), 0 10px 10px rgba(0,0,0,0.04);
shadow-card-hover: 0 20px 40px rgba(37,99,235,0.12);  /* 卡片悬停态 */
```

---

## 7. 动效规范

### 过渡曲线

```css
--transition-fast:  150ms cubic-bezier(0.4,0,0.2,1);   /* 微交互 */
--transition-base:  250ms cubic-bezier(0.4,0,0.2,1);   /* 通用 */
--transition-slow:  350ms cubic-bezier(0.4,0,0.2,1);   /* 大区块 */
--transition-spring: 400ms cubic-bezier(0.34,1.56,0.64,1); /* 弹性 */
```

### 页面进入动画

```css
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(8px); }
  to   { opacity: 1; transform: translateY(0); }
}
```

- 页面标题：延迟 0ms
- 第一排卡片：依次延迟 50ms、100ms、150ms、200ms
- 第二排面板：延迟 250ms、300ms
- 交错动画（stagger）：每项间隔 50-80ms

### 交互动效

| 元素 | 触发 | 效果 |
|------|------|------|
| 按钮 | hover | `translateY(-1px)` + shadow-md |
| 卡片 | hover | `translateY(-3px)` + shadow-card-hover + 边框消失 |
| 导航项 | hover | 背景色过渡 + 文字变亮 |
| 导航项 | active | 左侧 3px 蓝色指示条 |
| 表单输入 | focus | 边框变蓝 + 3px glow ring |
| 标签筛选 | click | 背景色 + 边框色切换，60ms |
| 选项卡 | active | 下划线 slide-in 动画 |
| 删除行 | click | 高度折叠 + 淡出 |
| 提交按钮 | loading | 文字→旋转图标→打勾图标三态切换 |
| 搜索框 | focus | 边框变蓝 + glow + 背景变白 |
| 数字徽章 | 新消息 | pulse 动画（box-shadow 呼吸） |

---

## 8. 布局系统

### 整体布局

```
+--Sidebar(260px)--+-Header(64px)-----------------+
|                  |                               |
|  Logo            |  Breadcrumb  [Search]  [Btns] |
|  ─────           |  ────────────────────────────  |
|  Nav Section     |                               |
|  > Nav Item      |       Content Area             |
|    Nav Item      |       (scrollable)             |
|  ─────           |                               |
|  Nav Section     |                               |
|  ─────           |                               |
|  User Card       |                               |
+------------------+-------------------------------+
```

### 内容区网格

**仪表盘首页**：12 列网格，2 栏布局（主 7fr + 侧 5fr）

**标准库页面**：侧边栏 300px + 主内容区（auto-fill 卡片网格，最小 260px）

**询价流程**：居中最大 860px，三步进度指示器 + 表单面板

### 响应式断点

| 断点 | 宽度 | 布局变化 |
|------|------|---------|
| < 640px | 手机 | 侧边栏收起（hamburger） |
| 640-1023px | 平板 | 标准库侧边栏隐藏，表格视图 |
| 1024-1279px | 小桌面 | 2 列 stats |
| ≥ 1280px | 标准桌面 | 完整布局 |

---

## 9. 组件库

### 9.1 导航组件

#### Sidebar Navigation Item
- **Default**：透明背景 + 70% 透明度文字
- **Hover**：深色背景 + 85% 透明度文字
- **Active**：深色背景 + 白色文字 + 左侧 3px 蓝色条
- **Badge**：右上角圆角数字，警告用橙色，脉冲动画表示新消息

#### Breadcrumb
- 字号 14px，分隔符 14px 宽箭头，语义：次要色→主要色

### 9.2 按钮

| 变体 | 背景 | 文字 | 边框 | 用途 |
|------|------|------|------|------|
| Primary | #2563EB | #fff | none | 主要操作 |
| Secondary | #F8F9FC | #111827 | #E8ECF0 | 次要操作 |
| Ghost | transparent | #6B7280 | none | 辅助操作 |
| Danger | #EF4444 | #fff | none | 删除/危险 |

- **Hover**：Primary 变深 + translateY(-1px) + shadow-md
- **Active**：scale(0.97)
- **Loading**：文字替换为旋转 SVG
- **Disabled**：opacity 0.5 + cursor not-allowed

### 9.3 表单元素

#### Input / Select
- 高度：40px（标准）/ 36px（紧凑）
- 圆角：radius-md (10px)
- **Default**：边框 #E8ECF0，背景白
- **Focus**：边框 #2563EB，box-shadow: 0 0 0 3px rgba(37,99,235,0.15)
- **Error**：边框 #EF4444，背景 #FEF2F2

#### Textarea
- 最小高度 100px，可垂直拖拽

### 9.4 卡片

#### Stat Card
- 顶部 3px 渐变色条（按颜色分蓝/绿/橙/紫）
- 数字 30px 粗体，变化百分比用背景色 pill 标签
- 右上角 44×44 图标容器

#### Spec Card
- 可视区 140px 高，SVG 工程图纸背景
- 规格参数网格 3 列，等宽
- **Hover**：translateY(-3px) + 主色阴影 + 边框消失

### 9.5 状态徽章

| 状态 | 背景色 | 文字色 | 圆角 |
|------|--------|--------|------|
| 待报价 | #FEF3C7 | #D97706 | full |
| 已报价 | #DBEAFE | #2563EB | full |
| 已成交 | #D1FAE5 | #059669 | full |
| 发货中 | #D1FAE5 | #059669 | full |
| 生产中 | #DBEAFE | #2563EB | full |
| 已签收 | #F3F4F6 | #6B7280 | full |

### 9.6 筛选标签

- **Default**：透明背景 + #E8ECF0 边框 + #6B7280 文字
- **Hover**：边框变主色，文字变主色
- **Active**：主色背景 + 主色边框 + 白色文字

---

## 10. 标准体系色标

业务核心标识：每个紧固件标准同时标注 GB/ISO/DIN 三套编号

```
GB   国标  #DC2626（红底白字）  — 中国国家标准
ISO  国际  #2563EB（蓝底白字）  — 国际标准化组织
DIN  德国  #6B7280（灰底白字）  — 德国工业标准
ANSI 美国  #7C3AED（紫底白字）  — 美国国家标准
JIS  日本  #EA580C（橙底白字）  — 日本工业标准
```

---

## 11. 图标方案

图标风格：**线性（stroke）**，2px 线宽，Round linecap

来源建议：
- **Heroicons**（官方，MIT）：SVG sprite，按需导入
- **Lucide**（备选）：更多细分图标
- **禁止**：混用填充/线性风格

尺寸规范：
- 导航内图标：18px
- 面板内图标：18-22px
- 功能按钮图标：16px
- 头部图标：18px

---

## 12. 技术实现建议

### 推荐技术栈

```html
<!-- 字体 -->
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&family=JetBrains+Mono:wght@400;500&display=swap">

<!-- 图标（可选） -->
<!-- Heroicons SVG inline 或 SVG sprite -->
```

### 设计交接清单

- [x] 色彩 Token（hex + rgba）
- [x] 字体比例 + 字重
- [x] 间距系统（4px 基准）
- [x] 圆角规范
- [x] 阴影规范
- [x] 动效时序 + 曲线
- [x] 组件状态说明
- [ ] 图标 sprite 文件
- [ ] Logo SVG 文件
- [ ] 工程图纸 SVG（待制作）

### 建议后续步骤

1. **UI 评审**：基于本原型与用户/产品确认设计方向
2. **组件库建设**：将本稿拆分为 React/Vue 组件（推荐 React + Tailwind 或 Vue + Element Plus 二次封装）
3. **深色模式**：sidebar 以外的区域也可以支持深色切换
4. **移动端**：侧边栏改为底部 Tab Bar + Hamburger menu
5. **无障碍**：补充 ARIA label、键盘导航、焦点管理（当前为设计稿阶段）

---

## 附录：色板快速参考

```
主色    #2563EB  (蓝)
辅色    #F59E0B  (橙)
成功    #10B981  (绿)
警告    #F59E0B  (黄)
错误    #EF4444  (红)
背景    #F8F9FC
表面    #FFFFFF
侧栏    #0F1623
边框    #E8ECF0
文字主  #111827
文字辅  #6B7280
文字淡  #9CA3AF
```

---

## 附录 B：Notion 设计语言规范（v2 — 2026-04-09）

> 参考：Notion Design System (`awesome-design-md` — references/notion.md)
> 原型文件：`coupler-ui.html`（Notion 风格版）

### B.1 核心哲学

Notion 的设计系统建立在**温暖极简**的哲学上——页面是纯净的画布，文字不是纯黑而是温暖的近黑（`rgba(0,0,0,0.95)`），灰色调带有微妙的黄褐底调。核心特征：

- **Ultra-thin 边框**：`1px solid rgba(0,0,0,0.1)` — 像低语一样的分割线
- **多层软阴影**：单层透明度不超过 0.05，累积出自然深度
- **单一强调色**：`#0075de`（Notion Blue）只用于 CTA 和交互元素
- **8px 基准间距**：非刚性有机比例

### B.2 色彩规范

```
Primary Text    rgba(0,0,0,0.95)    标题、正文（近黑，非纯黑）
Pure White      #ffffff             页面背景、卡片表面
Warm White      #f6f5f4             交替区块背景（黄褐底调）
Notion Blue     #0075de             主 CTA、链接
Active Blue     #005bab             按钮按下态
Badge Blue Bg   #f2f9ff             胶囊徽章背景（淡蓝）
Badge Blue Text #097fe8             徽章文字（深蓝）
Warm Gray 500   #615d59             次要文字
Warm Gray 300   #a39e98             占位符、禁用态
Whisper Border  rgba(0,0,0,0.1)    所有边框（核心！）
Border Mid      #dddddd             表单输入框边框
Teal            #2a9d99              成功态
Orange          #dd5b00              警告态
```

### B.3 字体规范

| 级别 | 字号 | 字重 | 字间距 | 行高 | 用途 |
|------|------|------|--------|------|------|
| Display | 64px | 700 | -2.125px | 1.00 | 英雄标题（罕见） |
| H1 | 26px | 700 | -0.625px | 1.23 | 页面标题 |
| H2 | 22px | 700 | -0.25px | 1.27 | 面板/卡片标题 |
| Body Large | 20px | 600 | -0.125px | 1.40 | 介绍文字 |
| Body | 16px | 400/500 | normal | 1.50 | 正文 |
| Nav | 15px | 600 | normal | 1.33 | 导航文字 |
| Caption | 14px | 400/500 | normal | 1.43 | 元数据、说明 |
| Badge | 12px | 600 | +0.125px | 1.33 | 状态标签（唯一正间距） |
| Micro | 11px | 500 | 0.04em | 1.3 | 小标签 |

> **关键规则**：字号越大，字间距越负（压缩感）；Badge 是唯一使用正字间距的元素

### B.4 圆角规范

| 值 | 用途 |
|----|------|
| 4px (micro) | 按钮、输入框、功能元素 |
| 5px (subtle) | 链接、列表项 |
| 8px (standard) | 小卡片、内联元素 |
| 12px (card) | **标准卡片** |
| 16px (featured) | 大面板、英雄卡片 |
| 9999px (pill) | 徽章、状态标签 |

### B.5 阴影规范

```css
/* 卡片阴影 — 4 层叠加，最大透明度 0.04 */
--shadow-card:
  rgba(0,0,0,0.04) 0px 4px 18px,
  rgba(0,0,0,0.027) 0px 2.025px 7.85px,
  rgba(0,0,0,0.02) 0px 0.8px 2.93px,
  rgba(0,0,0,0.01) 0px 0.175px 1.04px;

/* 悬浮阴影 — 透明度略增 */
--shadow-card-hover:
  rgba(0,0,0,0.06) 0px 4px 18px,
  rgba(0,0,0,0.04) 0px 2.025px 7.85px,
  rgba(0,0,0,0.03) 0px 0.8px 2.93px,
  rgba(0,0,0,0.02) 0px 0.175px 1.04px;

/* 深阴影 — 5 层，最大 0.05，用于模态框 */
--shadow-deep:
  rgba(0,0,0,0.01) 0px 1px 3px,
  rgba(0,0,0,0.02) 0px 3px 7px,
  rgba(0,0,0,0.02) 0px 7px 15px,
  rgba(0,0,0,0.04) 0px 14px 28px,
  rgba(0,0,0,0.05) 0px 23px 52px;
```

> **阴影哲学**：多层极低透明度累积，比单层硬阴影更自然

### B.6 按钮规范

**Primary**
- 背景：`#0075de`，文字：`#ffffff`
- 圆角：4px，内边距：6px 14px
- Hover：`#005bab`
- Active：`scale(0.97)` + 深色

**Secondary**
- 背景：`rgba(0,0,0,0.05)`，文字：`rgba(0,0,0,0.9)`
- Hover：`rgba(0,0,0,0.08)`

**Ghost**
- 背景：透明，文字：`rgba(0,0,0,0.95)`
- Hover：背景 `rgba(0,0,0,0.05)` + 下划线

**Pill Badge**
- 背景：`#f2f9ff`，文字：`#097fe8`
- 圆角：9999px，字重 600，字间距 +0.125px

### B.7 动效规范

| 动效 | 时长 | 曲线 | 说明 |
|------|------|------|------|
| 微交互 | 120ms | ease | hover、状态切换 |
| 通用 | 200ms | ease | 卡片悬浮、表单聚焦 |
| 区块动画 | 300ms | ease | 面板进入、列表滑入 |
| 页面进入 | 350ms | ease | 透明度+translateY 组合 |

**Notion 入场动画**：
```css
@keyframes notion-in {
  from { opacity: 0; transform: translateY(10px); }
  to   { opacity: 1; transform: translateY(0); }
}
```

### B.8 与原版（精密工程美学）的差异

| 维度 | 原版（精密工程美学） | Notion 风格版 |
|------|--------------------|--------------|
| 侧边栏 | 深色 #0F1623 | 纯白 + 细边框 |
| 强调色 | 蓝/橙/绿多色 | 单一 #0075de |
| 边框 | 中等灰边框 | ultra-thin rgba(0,0,0,0.1) |
| 阴影 | 单层明显阴影 | 多层极软阴影 |
| 数字显示 | 冷色粗体 | 暖灰中规整数字 |
| 卡片 | 深色标题条 | 纯白无标题条 |
| 整体氛围 | 专业工业感 | 温暖纸质感 |

---

## 附录 C：螺定 SUREDIM 三原型设计规范（v1.1 — 2026-04-10）

> 三个原型文件：`stdic-miniapp.html`（微信小程序）、`stdic-client.html`（桌面客户端）、`stdic-admin.html`（管理后台）
> ⚠️ PC 官网（内容站）原型 `stdic-landing.html` 已废弃，待重新设计。

### C.1 产品定位

螺定 SUREDIM 是一款面向螺栓行业从业者的**标准知识库工具**，以「数据经专业校正，每条标准附专家解读」为核心差异化。

**主战场：微信小程序**（第一优先）
**补充场景：桌面客户端**（离线/工厂内网用户）
**数据中枢：管理后台**（标准库 + 专家解读 + 用户授权管理）

| 属性 | 说明 |
|------|------|
| 产品名称 | 螺定 SUREDIM |
| 产品类型 | Electron 桌面应用（跨平台） |
| 核心场景 | 离线标准查询 + 千支重计算 |
| 数据存储 | 本地 SQLite（sql.js / Electron store） |
| 授权模式 | 离线序列号激活（免费/专业/企业三档） |
| 免费配额 | GB 50 + ISO 30 + DIN 20 = 100 条 |
| 付费触发 | 不限量数据访问 + PDF 报告导出 |

### C.2 三原型概览

| 原型 | 文件名 | 用途 | 目标用户 | 优先级 |
|------|--------|------|---------|--------|
| 微信小程序 | `stdic-miniapp.html` | 标准查询、扫码、收藏、个人中心 | 采购工程师、贸易商 | P0 |
| 桌面客户端 | `stdic-client.html` | 离线查询 + 计算器（内网/无网场景） | 工厂技术人员 | P1 |
| 管理后台 | `stdic-admin.html` | 标准库 CRUD、专家解读、序列号管理 | 运营管理员 | P0 |

### C.3 统一设计语言

三个原型共享以下设计规范（与上文精密工程美学一致）：

#### 字体
```css
font-family: 'Inter', 'PingFang SC', system-ui, sans-serif;
font-mono: 'JetBrains Mono', monospace;
```

#### 主色系统
```
Primary Blue    #2563EB   主按钮、选中态、图标
Primary Light   #EFF6FF   背景高亮
Primary Dark    #1D4ED8   hover 态
Success Green  #10B981   成功、专业版标识
Free Orange    #F59E0B   免费版标识
Sidebar Dark   #1c2333   桌面客户端侧边栏
Sidebar Admin  #0f172a   管理后台侧边栏
Background     #f1f5f9   内容区背景
Surface        #ffffff   卡片背景
```

#### 标准色标签
```
GB   #DC2626 / #FEE2E2（红底白字）
ISO  #2563EB / #DBEAFE（蓝底白字）
DIN  #6B7280 / #F3F4F6（灰底白字）
```

#### 圆角规范
```
6px   — 按钮、输入框、导航项
8px   — 小卡片
10px  — 标准卡片
12px  — 大卡片、模态框
14px  — 窗口、面板
9999px — 徽章、Pill
```

#### 阴影规范
```
shadow-sm:  0 1px 3px rgba(0,0,0,.06)
shadow-md:  0 4px 12px rgba(0,0,0,.1)
shadow-lg:  0 8px 24px rgba(0,0,0,.12)
shadow-card: 0 20px 60px rgba(0,0,0,.18)  (窗口阴影)
```

### C.4 客户端原型（stdic-client.html）

#### 布局结构
```
+--Sidebar(230px)--+--Content Area----------------+
| Logo + 授权条    |                               |
| ─────            |  Header (搜索 + 筛选工具栏)     |
| 导航项           |  ─────────────────────────    |
|  > 标准件库       |  标准卡片网格 (3列 auto-fill)   |
|    千支重计算     |                               |
|    标准对比       |  分页 / 加载更多               |
|    我的收藏       |                               |
|    设置           |                               |
| ─────            |                               |
| 用户信息          |                               |
+------------------+-------------------------------+
```

#### 状态设计
- **授权栏**：免费版显示橙色配额提示，专业版显示绿色徽章
- **搜索**：实时过滤 + 回车确认，支持标准名/规格名/关键词
- **筛选**：按标准（GB/ISO/DIN）一级筛选 + 按类型二级筛选
- **详情页**：点击卡片弹出详情面板，含参数表格 + 千支重计算 widget
- **计算器**：输入螺纹规格 + 长度，实时输出千支重量（kg/千支）
- **对比表**：选中多条记录，横向展示所有参数差异
- **解锁弹窗**：输入序列号（格式 `SDIM-PRO-XXXX-XXXX`）激活专业版

#### 技术要点
- 状态管理：`state = {isPro: false, used: 67, curSpec: null, ...}`
- 数据结构：`ALL_SPECS[id] = {id, name, d, k, s, l, weight, codes:['GB/T xxx','ISO xxx'], free: true/false}`
- 序列号验证：任何以 `SDIM-` 开头的 key 激活专业版
- 千支重参考表：`WEIGHT_REF[diameter] = weightPerThousand`

### C.5 管理后台原型（stdic-admin.html）

#### 登录
- 地址/账号：`admin` / `admin123`
- 样式：深色渐变背景 + 居中白色卡片

#### 布局结构
```
+--Sidebar(210px)--+-Topbar(52px)-----------------+
| Logo              |  页面标题   [统计指示器] [用户] |
| ─────             |--------------------------------+
| 首页概览           |  Content Area (scrollable)      |
| 标准件管理         |                                  |
| 产品管理           |                                  |
| ─────             |                                  |
| 序列号管理         |                                  |
| 付费用户           |                                  |
+-------------------+--------------------------------+
```

#### 页面功能
1. **首页概览**：4 个统计卡（标准总数 / 已授权用户 / 活跃序列号 / 本月新增）+ 最近注册列表
2. **标准件管理**：数据表格（编号/名称/标准/类型/配额/操作），支持新增/编辑/删除/上下架
3. **产品管理**：产品列表，含标准件关联、参数 JSON 编辑
4. **序列号管理**：生成序列号（按 tier: free/pro/enterprise），批量生成 + 失效操作
5. **付费用户**：用户列表（ID/邮箱/授权版本/注册时间/状态）

#### 序列号生成
```javascript
const tierLabel = tier === 'pro' ? 'PRO' : 'ENT';
const key = 'SDIM-' + tierLabel + '-' +
  String(Math.random()).slice(2, 6) + '-' +
  String(Math.random()).slice(2, 6);
```

### C.6 小程序原型（stdic-miniapp.html）

#### 设备适配
- iPhone 14 Pro 模拟界面：390 × 780 px（含状态栏 + 灵动岛 + 安全区）
- 字体：PingFang SC / Helvetica Neue（系统原生）
- 底部 TabBar 高度：74px（含 safe-area）

#### 页面结构（4 Tab）

```
+------------------+
|  Status Bar 44px |
|    灵动岛 30px    |
+------------------+
|  NavBar (首页)    |  56px
+------------------+
|                  |
|  Scroll Content  |  flex:1
|                  |
+------------------+
|  Tab Bar  74px   |
+------------------+
```

#### 四个 Tab 页面

**Tab 1 · 首页**
- 搜索框（实时过滤，支持标准号/规格名/关键词）
- Banner 卡（渐变蓝色，品牌定位 + 配额提示）
- 筛选标签（全部 / GB / ISO / DIN / 专家解读）
- 标准卡片列表（点击进入详情）

**Tab 2 · 扫码**
- 全屏深色取景框（模拟相机扫描）
- 扫描线动画（蓝线从上到下循环）
- 扫码成功后底部弹出结果卡（规格名/标准号/参数）
- 点击"查看详情"跳转详情页

**Tab 3 · 收藏**
- 收藏列表，按日期分组
- 每条含标准名/标准号/参数预览
- 左滑/点击 ✕ 删除

**Tab 4 · 我的**
- 用户头像 + 昵称
- 授权状态卡（免费版/专业版）
- 配额进度条（已用 / 总数）
- 升级按钮（点击弹出激活弹窗）
- 菜单项：查询历史 / 知识库 / 关于我们 / 激活序列号

#### 详情页
- 技术参数表格（公称直径 d / 头部厚度 k / 对边宽度 s / 公称长度 L / 千支重）
- **千支重计算器**（内嵌，选螺纹规格 M + 长度 mm → 实时输出 kg/千支）
- **专家解读**（注意 / 警示 / 提示三类，左边框颜色区分）
- 一键复制标准信息

#### 激活弹窗
- 底部抽屉式（iOS 原生交互）
- 输入框：序列号格式 `SDIM-PRO-XXXX-XXXX`
- 激活成功：配额→无限量，标签变绿

#### 设计要点
- 标准色标签：GB #DC2626 / ISO #2563EB / DIN #6B7280
- 主色：#2563EB（蓝），成功：#10B981（绿），解读：#F59E0B（橙）
- 计算器内嵌在详情页，配色为灰色背景 + 蓝色结果数字
- 专家解读卡片左边框：橙色=注意，红色=警示，绿色=提示

### C.7 与 COUPLER 平台的关系

| 维度 | COUPLER 平台（远期） | 螺定 SUREDIM（当前） |
|------|--------------------|---------------------|
| 产品形态 | Web 平台（B2B SaaS） | 微信小程序（主）+ 桌面端（辅） |
| 目标用户 | 企业采购团队 + 供应商 | 个人工程师、贸易商 |
| 部署模式 | 云端服务 + 多租户 | 微信云开发 + 本地 SQLite |
| 商业化 | 平台抽佣 + 高级功能订阅 | 数据包授权（小程序）+ 离线版授权 |
| 数据层 | MySQL + Redis + Meilisearch | MySQL（后台）+ 微信云（小程序） |
| 当前阶段 | 架构规划中（docs/05） | **小程序 MVP 开发优先** |

> 螺定 SUREDIM 是 COUPLER 平台**第一个 MVP 的独立化落地**——以小程序快速验证市场，COUPLER 作为远期全链路平台规划。
