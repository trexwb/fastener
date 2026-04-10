# 螺定 SUREDIM — 核心 CSS 系统

> **版本**：v2.0
> **日期**：2026-04-10
> **用途**：直接复制到任何项目中作为 Design Token + 基础样式库

---

## 一、Design Tokens（设计变量）

### 1.1 品牌色阶

```css
:root {
  /* 主色 Blue */
  --blue:        #2563eb;
  --blue-light:  #EFF6FF;
  --blue-dark:   #1D4ED8;
  --blue-glow:   rgba(37, 99, 235, 0.12);

  /* 辅助色 */
  --green:        #10B981;
  --green-light:  #ECFDF5;
  --orange:       #F59E0B;
  --orange-light: #FEF3C7;
  --red:          #DC2626;
  --red-light:    #FEE2E2;
  --purple:       #7C3AED;     /* 企业功能专用 */
  --purple-light: #EDE9FE;
}
```

### 1.2 灰阶（11级）

```css
:root {
  --gray-50:  #f8fafc;
  --gray-100: #f1f5f9;
  --gray-200: #e2e8f0;
  --gray-300: #cbd5e1;
  --gray-400: #94a3b8;
  --gray-500: #64748b;
  --gray-600: #475569;
  --gray-700: #374151;
  --gray-800: #1f2937;
  --gray-900: #0f172a;
}
```

### 1.3 标准体系色

```css
:root {
  /* GB 国标 */
  --gb:      #DC2626;
  --gb-bg:   #FEE2E2;

  /* ISO 国际 */
  --iso:     #2563EB;
  --iso-bg:  #DBEAFE;

  /* DIN 德国 */
  --din:     #6B7280;
  --din-bg:  #F3F4F6;
}
```

### 1.4 阴影层级

```css
:root {
  --shadow-xs:   0 1px 2px  rgba(0, 0, 0, 0.06);
  --shadow-sm:   0 1px 3px  rgba(0, 0, 0, 0.08);
  --shadow-md:   0 4px 12px rgba(0, 0, 0, 0.10);
  --shadow-lg:   0 8px 24px rgba(0, 0, 0, 0.12);
  --shadow-xl:   0 20px 60px rgba(0, 0, 0, 0.18);

  /* 品牌发光阴影 */
  --shadow-blue: 0 2px 6px  rgba(37,  99, 235, 0.20);
  --shadow-glow:0 0 0 3px rgba(37,  99, 235, 0.12); /* focus ring */
}
```

### 1.5 圆角层级

```css
:root {
  --r-xs:  4px;   /* 徽章/小标签 */
  --r-sm:  6px;   /* 按钮/输入框 */
  --r-md:  8px;   /* 卡片/面板 */
  --r-lg:  10px;  /* 大卡片 */
  --r-xl:  14px;  /* 模态框 */
  --r-full:9999px; /* 药丸型标签 */
}
```

### 1.6 过渡曲线

```css
:root {
  --t-fast:   120ms ease;                          /* 微交互：hover 反馈 */
  --t-base:   200ms ease;                         /* 标准过渡 */
  --t-slow:   300ms ease;                         /* 页面级动画 */
  --t-spring: 400ms cubic-bezier(0.34, 1.56, 0.64, 1);  /* 弹性反馈 */
}
```

---

## 二、Reset（样式重置）

```css
/* === GLOBAL RESET === */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html, body {
  height: 100%;
  overflow: hidden; /* 或 auto，视场景而定 */
}

/* === 文字选中 === */
::selection {
  background: var(--blue-glow);
  color: #1e293b;
}

/* === 滚动条（全局） === */
::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}
::-webkit-scrollbar-thumb {
  background: rgba(0, 0, 0, 0.12);
  border-radius: 3px;
}
::-webkit-scrollbar-track {
  background: transparent;
}

/* === 基础元素 === */
body {
  font-family: 'Inter', 'PingFang SC', system-ui, -apple-system, sans-serif;
  font-size: 14px;
  color: #1e293b;
  background: #f1f5f9;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

button {
  cursor: pointer;
  border: none;
  background: none;
  font-family: inherit;
}

input, select, textarea {
  font-family: inherit;
}

a {
  text-decoration: none;
  color: inherit;
}
```

---

## 三、布局系统

### 3.1 Flex 工具类

```css
.flex        { display: flex; }
.flex-col    { display: flex; flex-direction: column; }
.items-center{ align-items: center; }
.items-start { align-items: flex-start; }
.justify-between { justify-content: space-between; }
.justify-center  { justify-content: center; }
.gap-1  { gap: 4px; }
.gap-2  { gap: 8px; }
.gap-3  { gap: 12px; }
.gap-4  { gap: 16px; }
.flex-1  { flex: 1; }
.flex-shrink-0 { flex-shrink: 0; }
```

### 3.2 间距系统（8px 基准）

```css
.p-1  { padding: 4px; }   .px-1 { padding-left: 4px; padding-right: 4px; }
.p-2  { padding: 8px; }   .px-2 { padding-left: 8px; padding-right: 8px; }
.p-3  { padding: 12px; }  .px-3 { padding-left: 12px; padding-right: 12px; }
.p-4  { padding: 16px; }  .px-4 { padding-left: 16px; padding-right: 16px; }
.m-1  { margin: 4px; }
.m-2  { margin: 8px; }
.m-3  { margin: 12px; }
.m-4  { margin: 16px; }
.mt-2 { margin-top: 8px; }  .mt-3 { margin-top: 12px; }  .mt-4 { margin-top: 16px; }
.mb-2 { margin-bottom: 8px; }.mb-3 { margin-bottom: 12px; }.mb-4 { margin-bottom: 16px; }
```

---

## 四、Typography（字体系统）

### 4.1 字体栈

```css
/* 西文：Inter */
--font-sans: 'Inter', 'PingFang SC', system-ui, -apple-system, sans-serif;
/* 等宽：JetBrains Mono（参数/代码/数值） */
--font-mono: 'JetBrains Mono', 'SF Mono', Menlo, monospace;
```

### 4.2 字号层级

```css
.text-xs  { font-size: 11px; }
.text-sm  { font-size: 12px; }
.text-base{ font-size: 14px; }
.text-lg  { font-size: 15px; }
.text-xl  { font-size: 17px; }
.text-2xl { font-size: 22px; }
.text-3xl { font-size: 26px; }

/* 数值/参数专用（等宽） */
.mono { font-family: var(--font-mono); letter-spacing: 0.02em; }
```

### 4.3 字重层级

```css
.font-regular { font-weight: 400; }
.font-medium  { font-weight: 500; }
.font-semibold{ font-weight: 600; }
.font-bold    { font-weight: 700; }
```

---

## 五、组件规格

### 5.1 标准徽章（Badge）

```css
/* 通用徽章 */
.badge {
  display: inline-flex;
  align-items: center;
  font-size: 10px;
  font-weight: 700;
  padding: 2px 7px;
  border-radius: var(--r-xs);
  letter-spacing: 0.02em;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.12);
}

/* GB */
.badge-gb  { background: var(--gb-bg);  color: var(--gb);  }
/* ISO */
.badge-iso { background: var(--iso-bg); color: var(--iso); }
/* DIN */
.badge-din { background: var(--din-bg); color: var(--din); border: 1px solid var(--gray-200); }

/* 状态徽章 */
.badge-green { background: #dcfce7; color: #16a34a; }
.badge-blue  { background: #dbeafe; color: #2563eb; }
.badge-yellow{ background: #fef9c3; color: #ca8a04; }
.badge-red   { background: #fee2e2; color: #dc2626; }
.badge-gray  { background: #f1f5f9; color: #64748b; }
```

### 5.2 按钮系统

```css
/* === BASE BUTTON === */
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
  padding: 7px 13px;
  border-radius: var(--r-md);
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
  transition: all var(--t-spring);
  border: none;
  white-space: nowrap;
}
.btn svg { width: 13px; height: 13px; flex-shrink: 0; }

/* Primary（品牌蓝） */
.btn-primary {
  background: var(--blue);
  color: #fff;
  box-shadow: 0 2px 6px rgba(37, 99, 235, 0.20);
}
.btn-primary:hover {
  background: var(--blue-dark);
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(37, 99, 235, 0.25);
}
.btn-primary:active {
  transform: translateY(0);
  box-shadow: 0 1px 4px rgba(37, 99, 235, 0.20);
}

/* Secondary */
.btn-secondary {
  background: var(--gray-100);
  color: var(--gray-600);
  border: 1px solid var(--gray-200);
}
.btn-secondary:hover {
  background: var(--gray-200);
  border-color: var(--gray-300);
  transform: translateY(-1px);
}

/* Success */
.btn-success {
  background: var(--green);
  color: #fff;
  box-shadow: 0 2px 6px rgba(16, 185, 129, 0.20);
}
.btn-success:hover {
  background: #059669;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(16, 185, 129, 0.25);
}

/* Danger */
.btn-danger {
  background: #fef2f2;
  color: var(--red);
  border: 1px solid #fecaca;
}
.btn-danger:hover {
  background: var(--red-light);
  border-color: #fca5a5;
  transform: translateY(-1px);
}

/* Ghost */
.btn-ghost {
  background: transparent;
  color: var(--gray-500);
}
.btn-ghost:hover {
  background: var(--gray-100);
  color: var(--gray-800);
}

/* 尺寸 */
.btn-sm { padding: 5px 10px; font-size: 12px; }
.btn-xs { padding: 3px 8px; font-size: 11px; }
```

### 5.3 表单输入框

```css
/* 输入框 */
.form-input {
  width: 100%;
  padding: 8px 11px;
  border: 1px solid var(--gray-200);
  border-radius: var(--r-md);
  font-size: 13px;
  color: var(--gray-800);
  background: #fff;
  outline: none;
  transition: border-color var(--t-base), box-shadow var(--t-base);
}
.form-input:focus {
  border-color: var(--blue);
  box-shadow: 0 0 0 3px var(--blue-glow);
}
.form-input:disabled {
  background: var(--gray-50);
  color: var(--gray-400);
  cursor: not-allowed;
}
.form-input::placeholder { color: var(--gray-400); }

/* 带图标的输入框 */
.input-wrap {
  display: flex;
  align-items: center;
  gap: 8px;
  background: var(--gray-100);
  border: 1px solid var(--gray-200);
  border-radius: var(--r-md);
  padding: 0 12px;
  height: 40px;
  transition: border-color var(--t-base), box-shadow var(--t-base), width var(--t-spring);
}
.input-wrap:focus-within {
  border-color: var(--blue);
  box-shadow: 0 0 0 3px var(--blue-glow);
}
.input-wrap svg { width: 14px; height: 14px; stroke: var(--gray-400); flex-shrink: 0; }
.input-wrap:focus-within svg { stroke: var(--blue); }
.input-wrap input {
  flex: 1;
  border: none;
  background: none;
  font-size: 13px;
  color: var(--gray-800);
  outline: none;
  min-width: 0;
}
```

### 5.4 卡片组件

```css
.card {
  background: #fff;
  border: 1px solid var(--gray-200);
  border-radius: var(--r-lg);
  overflow: hidden;
  box-shadow: var(--shadow-sm);
  transition: border-color var(--t-base), box-shadow var(--t-base), transform var(--t-base);
}
.card:hover {
  border-color: rgba(37, 99, 235, 0.20);
  box-shadow: var(--shadow-md);
  transform: translateY(-2px);
}
.card-header {
  padding: 12px 16px;
  border-bottom: 1px solid var(--gray-200);
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.card-title {
  font-size: 14px;
  font-weight: 700;
  color: var(--gray-800);
}
.card-body { padding: 14px 16px; }
```

### 5.5 专家解读卡片

```css
.interp-card {
  padding: 12px 14px;
  border-radius: var(--r-md);
  border-left: 3px solid var(--orange);
  background: var(--orange-light);
  transition: all var(--t-base);
}

/* 注意 */
.interp-card.note  { border-left-color: var(--orange); background: var(--orange-light); }
.interp-card.warn  { border-left-color: var(--red);     background: var(--red-light);    }
.interp-card.tip   { border-left-color: var(--green);  background: var(--green-light);  }

.interp-label {
  font-size: 10px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.6px;
  margin-bottom: 4px;
  display: flex;
  align-items: center;
  gap: 4px;
}
.interp-label.note { color: #92400e; }
.interp-label.warn { color: #B91C1C; }
.interp-label.tip  { color: #047857; }

.interp-text {
  font-size: 13px;
  color: var(--gray-700);
  line-height: 1.7;
}
```

### 5.6 空状态组件

```css
.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 48px 24px;
  text-align: center;
}
.empty-icon {
  width: 64px;
  height: 64px;
  background: var(--gray-100);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 28px;
  margin-bottom: 16px;
}
.empty-title {
  font-size: 15px;
  font-weight: 600;
  color: var(--gray-700);
  margin-bottom: 6px;
}
.empty-desc {
  font-size: 13px;
  color: var(--gray-400);
  max-width: 280px;
}
```

---

## 六、动画规格

### 6.1 全局 Transition Token 用法

```css
/* 快速反馈（hover/active） */
transition: all var(--t-fast);

/* 标准过渡（展开/收起） */
transition: all var(--t-base);

/* 页面级动画（滑入/淡入） */
transition: all var(--t-slow);

/* 弹性反馈（按钮点击/弹窗） */
transition: all var(--t-spring);
```

### 6.2 关键帧

```css
/* 滑入（上） */
@keyframes slideUpFade {
  from { opacity: 0; transform: translateY(12px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* 滑入（下） */
@keyframes slideDown {
  from { opacity: 0; transform: translateY(-8px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* 脉冲（离线警告点） */
@keyframes pulse {
  0%, 100% { opacity: 1; transform: scale(1); }
  50%       { opacity: 0.5; transform: scale(1.15); }
}

/* 呼吸（扫描脉冲） */
@keyframes scanPulse {
  0%, 100% { transform: scale(1); opacity: 0.5; }
  50%       { transform: scale(1.04); opacity: 1; }
}

/* 扫描线 */
@keyframes scanLine {
  0%   { top: 8%; opacity: 1; }
  50%  { opacity: 1; }
  100% { top: 92%; opacity: 0; }
}
```

### 6.3 状态变化动画规则

| 操作 | 触发方式 | 曲线 | 时长 |
|------|---------|------|------|
| 按钮 hover | `:hover` | ease | 120ms |
| 按钮 click | `:active` | spring | 400ms |
| 输入框 focus | `:focus-within` | ease | 200ms |
| 弹窗滑入 | `@keyframes slideUpFade` | ease | 300ms |
| 扫描线 | `@keyframes scanLine` | cubic-bezier | 2000ms |
| 数字更新 | `.updating` class | spring | 400ms |

---

## 七、辅助工具

```css
/* 文字截断 */
.truncate { overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }

/* 隐藏滚动条 */
.no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
.no-scrollbar::-webkit-scrollbar { display: none; }

/* 触控优化 */
.tap-none { -webkit-tap-highlight-color: transparent; }

/* Focus ring（键盘导航用） */
.focus-ring:focus-visible {
  outline: 2px solid var(--blue);
  outline-offset: 2px;
}
```
