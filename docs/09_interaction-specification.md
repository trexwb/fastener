# 螺定 SUREDIM — 交互说明书

> **版本**：v1.0
> **日期**：2026-04-10
> **适用范围**：stdic-miniapp.html / stdic-client.html / stdic-admin.html

---

## 一、交互设计原则

1. **即时反馈**：每次点击/悬停/加载≤ 120ms 内给出视觉反馈
2. **五态覆盖**：每个可交互组件必须具备 Default / Hover / Active / Disabled / Loading 五态
3. **减少认知负荷**：异步操作使用状态机，避免直接跳变
4. **移动优先**：触控场景优先于悬停场景设计

---

## 二、按钮交互规范

### 2.1 标准按钮状态机

```
[Default] ──hover──> [Hover: translateY(-1px) + shadow 加深]
      │                      │
      │                 click/tap
      │                      │
      ▼                      ▼
[Active: translateY(0) + shadow 复位]
```

| 状态 | 视觉表现 | 时长 |
|------|---------|------|
| Default | 正常颜色 + 初始阴影 | — |
| Hover | `translateY(-1px)` + 阴影加深 50% | 120ms ease |
| Active/Pressed | `translateY(0)` + 阴影恢复 Default | 120ms ease |
| Disabled | 灰色背景，cursor: not-allowed，无动画 | — |
| Loading | 显示 spinner，禁用点击，背景保持 | — |

### 2.2 主要按钮（Primary）特殊处理

- 阴影颜色 = 按钮主色透明色（如 `rgba(37,99,235,.2)`）
- Hover 时阴影扩大 + 上移 1px
- Active 时阴影收缩到初始值

### 2.3 Ghost / Text 按钮

- 无背景色，以背景色替代阴影
- Hover 时背景变为 `var(--gray-100)`
- Active 时背景变为 `var(--gray-200)`

---

## 三、表单交互规范

### 3.1 输入框焦点状态

```
[默认] ──focus──> [边框变蓝 #2563eb + box-shadow glow 3px]
    ▲                      │
    └────blur─────── [恢复默认]
```

**实现代码**：

```css
.input-wrap {
  border: 1px solid var(--gray-200);
  transition: border-color 200ms ease, box-shadow 200ms ease;
}
.input-wrap:focus-within {
  border-color: var(--blue);
  box-shadow: 0 0 0 3px var(--blue-glow);
}
```

### 3.2 搜索框动态扩展（桌面端）

- **默认宽度**：260px
- **聚焦宽度**：320px（`transition: width 400ms cubic-bezier(0.34,1.56,0.64,1)`）
- **搜索图标颜色**：`#94a3b8` → 聚焦时变为 `#2563eb`

### 3.3 表单校验反馈

| 类型 | 视觉表现 | 说明 |
|------|---------|------|
| 必填空字段 | 红色边框 + 红色 glow + 错误提示文字 | 防止无意义提交 |
| 格式错误 | 同上 + 输入框下方错误文案 | 如：邮箱格式、手机号 |
| 校验通过 | 绿色小对勾图标出现 | 可选增强 |

---

## 四、扫码流程状态机

### 4.1 状态流转

```
[空闲] ──点击扫描按钮──> [扫描中（动画循环）]
      │                        │
      │                   识别成功
      │                        │
      │                        ▼
      │                 [扫描框闪光]
      │                    │
      │               延迟 400ms
      │                    │
      │                    ▼
      └────────────────> [结果卡片滑入]
```

### 4.2 各状态视觉表现

| 状态 | 视觉表现 | 时长 |
|------|---------|------|
| 空闲 | 白色半透明扫描框，四个圆角装饰 | — |
| 扫描中 | 扫描线来回动画（蓝色 + 发光），背景脉冲呼吸 | 循环 2000ms |
| 扫描框闪光 | 边框变为绿色 `#10B981`，`box-shadow: 0 0 16px rgba(16,185,129,.4)` | 400ms |
| 结果滑入 | 玻璃态卡片 `slideUpFade` 从下方滑入 | 300ms ease |

### 4.3 实现参考（Miniapp JS）

```javascript
function showScanResult(specData) {
  // 1. 扫描框变绿（状态确认）
  const frame = document.querySelector('.scan-frame');
  frame.classList.add('success'); // CSS: border-color: green + glow

  // 2. 延迟显示结果（减少认知负荷）
  setTimeout(() => {
    // 3. 显示结果卡片
    const resultCard = document.querySelector('.scan-result-card');
    resultCard.style.display = 'block';
    resultCard.classList.add('animate-slide-up');

    // 4. 填充数据
    renderScanResult(specData);
  }, 400);
}
```

### 4.4 扫码按钮

| 状态 | 视觉表现 |
|------|---------|
| Default | 蓝色背景 + 阴影 `0 4px 12px rgba(37,99,235,.3)` |
| Hover | 颜色加深 `#1d4ed8` + 阴影扩大 |
| Active | `transform: scale(.96)` + 阴影收缩 |

---

## 五、计算器交互规范

### 5.1 计算流程

```
[输入规格参数] ──点击计算──> [显示加载中/禁用按钮]
                              │
                         计算完成
                              │
                         [结果数字动画]
```

### 5.2 计算结果数字更新动画

**触发时机**：每次计算完成后更新结果数字时

**动画逻辑**：

```javascript
function updateCalc() {
  const resultEl = document.querySelector('.calc-result strong');

  // 1. 移除旧动画
  resultEl.classList.remove('updating');

  // 2. 触发重排（强制浏览器重新应用动画）
  void resultEl.offsetWidth;

  // 3. 添加动画类
  resultEl.classList.add('updating');

  // 4. 动画结束后移除
  setTimeout(() => resultEl.classList.remove('updating'), 400);
}
```

**CSS 动画**：

```css
.calc-result strong {
  transition: all 200ms ease;
}
.calc-result strong.updating {
  transform: scale(1.08);
  color: var(--blue-dark);
  transition: all 400ms cubic-bezier(0.34, 1.56, 0.64, 1);
}
```

### 5.3 输入框联动

- 任意一个输入框值变化 → 计算按钮立即可用（如果有完整参数）
- 参数不完整时按钮置灰

---

## 六、解锁/激活弹窗交互规范（iOS 底部抽屉）

### 6.1 状态流转

```
[隐藏] ──点击激活按钮──> [背景遮罩淡入]
      │                      │
      │                 弹窗滑入（spring）
      │                      │
      │              [可交互状态]
      │                      │
      └──点击遮罩/取消──> [弹窗滑出]
                             │
                        [隐藏]
```

### 6.2 遮罩层

| 属性 | 值 |
|------|-----|
| 背景 | `rgba(0, 0, 0, 0.5)` |
| 动画 | `opacity 0→1`, 200ms ease |
| 附加效果 | `backdrop-filter: blur(4px)` |

### 6.3 抽屉弹窗

| 属性 | 值 |
|------|-----|
| 圆角 | 顶部 16px，底部 0 |
| 背景 | 白色 + `box-shadow` 上层阴影 |
| 入场动画 | `@keyframes slideUpSheet` + spring 曲线 |
| 关闭动画 | 同入场，反向 |
| 拖动关闭 | iOS 原生下拉关闭（可扩展） |

### 6.4 序列号输入框

| 状态 | 视觉表现 |
|------|---------|
| Default | 灰色边框 |
| Focus | 蓝色边框 + glow ring |
| Valid | 绿色边框 + 对勾 |
| Invalid | 红色边框 + 错误提示 |

### 6.5 提交按钮

| 状态 | 视觉表现 |
|------|---------|
| Default | 蓝色背景 + 品牌阴影 |
| Disabled（序列号格式不符） | 灰色背景，无阴影，不可点击 |
| Loading | 显示 spinner，禁用 |
| Success | 短暂变为绿色，显示 ✓，然后跳转 |

---

## 七、Tab Bar / 侧边栏导航交互

### 7.1 小程序 Tab Bar

| 操作 | 视觉反馈 | 时长 |
|------|---------|------|
| 点击 Tab | active 项下方蓝色指示条出现（spring 展开） | 400ms spring |
| 按下 Tab（touch） | `transform: scale(.9)` | 120ms |
| 切换页面 | 对应 `.page` 添加/移除 `.active` 类 | 200ms ease |

**CSS 实现**：

```css
.tab-item.active::before {
  transform: translateX(-50%) scaleX(0);
  transition: all 400ms cubic-bezier(0.34, 1.56, 0.64, 1);
  /* active 时 scaleX(0→1) 展开 */
}
```

### 7.2 桌面端侧边栏

| 操作 | 视觉反馈 |
|------|---------|
| Hover 菜单项 | 背景变亮 + 左侧指示线从中心向上下展开（spring） |
| 点击菜单项 | 背景变深 + 左侧指示线完全展开 |
| 导航徽章 | Hover 时微亮 |

---

## 八、列表/卡片交互规范

### 8.1 标准卡片触控反馈（移动端）

```css
.spec-card {
  transition: background 120ms ease, transform 120ms ease;
  -webkit-tap-highlight-color: transparent;
}
.spec-card:active {
  background: var(--gray-50);
  transform: scale(0.99);
}
```

### 8.2 表格行 Hover（桌面端）

```css
tr {
  position: relative;
  transition: background 100ms ease;
}
tr::after {
  /* 左侧蓝色指示条 */
  content: '';
  position: absolute;
  left: 0; top: 0; bottom: 0;
  width: 0;
  background: var(--blue);
  transition: width 150ms ease;
  opacity: 0.08;
  border-radius: 0 2px 2px 0;
}
tr:hover::after { width: 3px; }
tr:hover td { background: #f8faff; }
```

---

## 九、加载与错误状态

### 9.1 异步操作通用模式

| 阶段 | 视觉表现 |
|------|---------|
| 发起请求 | 按钮变为 Loading 态（spinner + 禁用） |
| 请求成功 | 短暂绿色成功态 → 跳转或刷新 |
| 请求失败 | 按钮恢复可点击 + Toast 错误提示（红色，3s 自动消失） |

### 9.2 空状态

| 场景 | 表现 |
|------|------|
| 无搜索结果 | 空状态组件：图标 + "未找到相关标准" + 建议文案 |
| 无收藏 | 空状态组件：星形图标 + "暂无收藏" + 快捷入口 |
| 无数据（表格） | 空状态组件：占位图形 + 说明文字 |

### 9.3 离线状态（桌面端）

```
[在线] ──网络断开──> [离线指示器出现（脉冲警告点）]
                              │
                         网络恢复
                              │
                         [指示器消失（淡出）]
```

**离线指示器样式**：

```css
.offline-indicator {
  display: none; /* JS 切换为 flex */
  align-items: center;
  gap: 6px;
  background: #fef3c7;
  border-bottom: 1px solid #fde68a;
  padding: 8px 16px;
  font-size: 12px;
  color: #92400e;
  font-weight: 500;
}
.offline-dot {
  width: 8px;
  height: 8px;
  background: #f59e0b;
  border-radius: 50%;
  animation: pulse 1.5s ease-in-out infinite;
}
```

---

## 十、页面过渡规范

### 10.1 页面切换（单页应用）

| 场景 | 动画 | 时长 | 曲线 |
|------|------|------|------|
| Tab 页面切换 | 淡入淡出（opacity） | 200ms | ease |
| 详情页进入 | 从右侧滑入（translateX） | 300ms | ease-out |
| 详情页返回 | 滑回右侧 | 250ms | ease-in |

### 10.2 弹窗/模态框

| 阶段 | 动画 |
|------|------|
| 入场 | `opacity: 0→1` + `scale(0.96→1)`，300ms spring |
| 退场 | `opacity: 1→0` + `scale(1→0.96)`，200ms ease |

---

## 十一、待接入 JS 逻辑清单

以下交互动画 CSS 已就绪，需接入 JavaScript 逻辑：

| 项目 | 端别 | 描述 |
|------|------|------|
| 批量选择栏显示/隐藏 | Admin | 多选 checkbox → 触发 `.batch-bar` slideDown 滑入 |
| 离线指示器切换 | Client | `navigator.onLine` 监听 → 切换 `.offline-indicator.show` |
| 专家解读面板渲染 | Client | 详情页 → 根据数据类型注入 `.interp-card` HTML |
| 扫描结果数据填充 | Miniapp | `renderScanResult(specData)` → 动态填充扫码结果卡片 |
| 计算结果数字动画 | Miniapp | `updateCalc()` → 触发 `.updating` 类动画 |
