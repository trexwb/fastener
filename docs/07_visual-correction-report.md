# 螺定 SUREDIM — 视觉修正案

> **版本**：v1.0
> **日期**：2026-04-10
> **适用范围**：stdic-miniapp.html / stdic-client.html / stdic-admin.html

---

## 一、修正原则

1. **设计一致性**：三端使用统一的 Design Token 命名体系
2. **交互可达性**：所有触控目标 ≥ 48px，移动端 `:active` 提供触觉反馈
3. **状态完整性**：每个组件具备 Default / Hover / Active / Disabled / Loading 五态
4. **响应即时性**：每一次点击/悬停/加载都有视觉反馈

---

## 二、修正清单（按端别）

### 2.1 stdic-miniapp.html（小程序端）P0

| # | 问题描述 | 修正前 | 修正后 | 文件位置 |
|---|---------|--------|--------|---------|
| M01 | 无 Design Token 系统 | 硬编码色值散布全文件 | 统一注入 `:root` CSS Variables（--blue/--blue-light/--blue-glow 等） | 第 9–20 行 |
| M02 | Tab Bar 无激活指示器 | 纯文字无区分 | active 项下方增加 3px 蓝色指示条，`::before` + spring 动画 | 第 50–52 行 |
| M03 | Tab Bar 触控目标不足 | 无 min-height | 增加 `min-height: 48px`，tap 缩放反馈 `scale(.9)` | 第 50, 56 行 |
| M04 | 标准卡片无触觉反馈 | 纯链接感 | `:active` 增加 `scale(.99)` + 背景变灰 | 第 92 行 |
| M05 | 标准卡片左侧无品牌强调 | 无视觉锚点 | `::before` 添加 3px 蓝色左侧竖线，hover 时显现 | 第 91 行 |
| M06 | 徽章无阴影层次 | 纯色背景 | 增加 `box-shadow: 0 1px 2px rgba(0,0,0,.12)` | 第 96 行 |
| M07 | 千支重标签无光晕 | 纯蓝底 | 增加 `box-shadow: 0 1px 3px rgba(37,99,235,.15)` | 第 105 行 |
| M08 | 扫码扫描线动画生硬 | `ease-in-out` 2s | 改为 `cubic-bezier(.4,0,.6,1)` + 蓝色发光 `box-shadow` | 第 175 行 |
| M09 | 扫码区域无脉冲氛围 | 静止状态 | 新增 `.scan-pulse` 元素，2s ease-in-out 呼吸动画 | 第 177–178 行 |
| M10 | 扫码成功无确认反馈 | 直接显示结果 | 扫描框 → `.success` 绿色边框闪光 → 400ms 延迟 → 结果卡片滑入 | JS showScanResult() |
| M11 | 扫码结果卡片无质感 | 纯黑实底 | 改为玻璃态 `rgba(255,255,255,.07)` + 边框 + `backdrop-filter: blur` | 第 181 行 |
| M12 | 扫码底部区域无景深感 | 纯黑背景 | 毛玻璃效果 `backdrop-filter: blur(20px)` + 微透明边框 | 第 180 行 |
| M13 | 扫码按钮无立体感 | 纯色背景 | `box-shadow: 0 4px 12px rgba(37,99,235,.3)` + `:active: scale(.96)` | 第 188–189 行 |
| M14 | 收藏删除按钮过小 | 20px | 增大至 36px，`:active` 红色背景反馈 | 第 199 行 |
| M15 | 计算器输入框无焦点态 | 无状态区分 | `focus-within` 边框变蓝 + `box-shadow` 光环 | 第 139–140 行 |
| M16 | 计算器结果数无更新动画 | 瞬时跳变 | 增加 `.updating` 类 → `scale(1.08)` spring 动画 | 第 147–148 行；JS updateCalc() |
| M17 | 专家解读类型未视觉区分 | 统一橙色左边框 | 使用 `:has(.interp-type.warn)` / `:has(.interp-type.tip)` 切换颜色和背景 | 第 156–158 行 |
| M18 | 解锁弹窗非 iOS 原生风格 | 普通居中对话框 | iOS 底部抽屉样式，`slideUpSheet` spring 动画，背景毛玻璃遮罩 | 第 180–200 行（推测行数） |
| M19 | 详情页参数表格无行 hover | 静态 | `tr:hover` 背景变灰，左侧蓝边显现 | 第 128–129 行 |
| M20 | 详情页小节标题无品牌标识 | 纯文字 | `h3::before` 蓝色 3px 竖条指示器 | 第 126 行 |

### 2.2 stdic-client.html（桌面端）P1

| # | 问题描述 | 修正前 | 修正后 | 文件位置 |
|---|---------|--------|--------|---------|
| C01 | 无 Design Token 系统 | 部分 token，命名不一致 | 完整 token 系统：--blue/-light/-dark/-glow / --shadow-xs~xl / --r-xs~xl / --t-fast/base/slow/spring | 第 15–41 行 |
| C02 | 无选中文字样式 | 浏览器默认蓝底 | 自定义 `::selection { background: var(--blue-glow); color: #1e293b }` | 第 46 行 |
| C03 | 无自定义滚动条 | 系统默认滚动条 | 统一 6px 宽圆角拇指滚动条（sidebar/内容区） | 第 47–49 行 |
| C04 | 侧边栏激活指示器不自然 | 纯色左边框 | 竖线改用 spring 动画 `scaleY(0→1)` 从中心展开 | 第 81–85 行 |
| C05 | 授权升级按钮无层次感 | 纯蓝底 | `box-shadow: 0 2px 8px rgba(37,99,235,.25)` + hover 上移 + 阴影加深 | 第 70–73 行 |
| C06 | 锁定提示条过于平淡 | 浅黄背景 | 添加右上角装饰渐变 `::before`，图标加阴影，按钮 hover/active spring | 第 98–107 行 |
| C07 | 头部搜索框宽度固定 | 无过渡动画 | `focus-within` 宽度从 260px 展开至 320px，图标同步变色 | 第 113–116 行 |
| C08 | 用户行 hover 无反馈 | 无视觉变化 | `hover` 背景微亮，头像阴影加深 | 第 91–93 行 |
| C09 | 无离线状态指示器 | 断网时无提示 | 新增 `.offline-indicator` 脉冲警告点动画（CSS 已注入，JS 待接入） | CSS 待接入 |
| C10 | 无专家解读面板 | 标准详情无解读 | 新增 `.interp-card` 三类型变体（note/warn/tip），色标+背景色区分 | CSS 已注入 |

### 2.3 stdic-admin.html（管理后台）P0

| # | 问题描述 | 修正前 | 修正后 | 文件位置 |
|---|---------|--------|--------|---------|
| A01 | 无 Design Token 系统 | 硬编码色值 | 完整 token（含 --purple 企业色系） | 第 15–28 行 |
| A02 | 登录页无深度感 | 扁平卡片 | 添加两层径向渐变光晕 `::before/::after`，卡片 25px 阴影 + spring 入场动画 | 第 42–46 行 |
| A03 | 登录表单输入框无焦点态 | 纯边框 | `focus` 边框变蓝 + `box-shadow` 光环 | 第 56–57 行 |
| A04 | 登录按钮无层次感 | 无渐变/阴影 | `linear-gradient` 双色渐变 + `box-shadow: 0 4px 14px` + hover/active 状态链 | 第 58–60 行 |
| A05 | Topbar 统计区无分隔 | 无视觉分隔 | 每个 stat-item 前增加 1px 竖线分隔 | 第 71–73 行 |
| A06 | 头像无悬停效果 | 静态 | `hover: scale(1.05)` + 阴影加深 | 第 76–77 行 |
| A07 | 搜索框无动态反馈 | 静态 | `focus-within` 边框变蓝 + glow ring + 宽度微扩 | CSS 流程式 |
| A08 | 页面标签无过渡 | 切换生硬 | `hover` 背景 `var(--gray-100)` + 200ms ease | CSS 流程式 |
| A09 | 按钮系统五态不完整 | 仅 Default/Active | 所有按钮变体增加：hover（上移1px+阴影加深）+ active（复位）+ shadow 光色 | 第 138–145 行 |
| A10 | 主按钮缺少品牌色阴影 | 无发光效果 | `.btn-primary` 增加 `box-shadow: 0 2px 6px rgba(37,99,235,.2)` | 第 140 行 |
| A11 | 表单输入无焦点光环 | 无焦点状态 | `focus: box-shadow: 0 0 0 3px rgba(37,99,235,.1)` | 第 150 行 |
| A12 | 表单下拉箭头非自定义 | 浏览器默认 | SVG 背景图自定义箭头 | 第 150 行 |
| A13 | 分页器无阴影层次 | 无深度 | active 页码加 `box-shadow`，hover 上移 | CSS 流程式 |
| A14 | 无批量选择栏 | 不支持多选操作 | 新增 `.batch-bar` 滑入动画（`animation: slideDown`），含计数和清空操作 | CSS 已注入 |
| A15 | 无空状态组件 | 列表为空时空白 | 新增 `.empty-state` 组件：64px 圆图标 + 标题 + 描述 | CSS 已注入 |

---

## 三、修正优先级总结

| 优先级 | 端别 | 数量 | 核心主题 |
|--------|------|------|---------|
| P0 修复中 | 小程序 | 20项 | 触觉反馈、扫码状态机、计算器动画 |
| P0 修复中 | 管理后台 | 15项 | 表单五态、批量操作、空状态 |
| P1 优化中 | 桌面端 | 10项 | 侧边栏动画、离线感知、专家面板 |

---

## 四、修正状态追踪

| 项目 | 状态 | 说明 |
|------|------|------|
| 设计 Token 注入 | ✅ 完成 | 三端均已注入完整 CSS Variables |
| 小程序交互完善 | ✅ 完成 | M01–M20 全部修正 |
| 桌面端交互完善 | ✅ 完成 | C01–C10 全部修正 |
| 后台交互完善 | ✅ 完成 | A01–A15 全部修正 |
| 批量选择 JS 逻辑 | ⏳ 待接入 | CSS 已就位，JS 需接入 |
| 离线指示器 JS 逻辑 | ⏳ 待接入 | CSS 已就位，JS 需接入 |
| 专家解读面板接入详情页 | ⏳ 待接入 | CSS 已就位，HTML 片段待添加 |
