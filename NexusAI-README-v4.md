# NexusAI · 金融数字孪生平台 v4

> **版本**: 4.0  
> **发布日期**: 2026-05-26  
> **状态**: 原型交付

---

## 项目概述

NexusAI 是面向金融行业（银行/券商/基金/保险）的 3D 数字孪生平台。v4 将视角从宏观城区俯瞰转变为精致的办公室内部等距视图，通过**空间即数据**的设计理念，将部门状态、KPI 指标、工作流和风险告警嵌入物理办公场景中。

**v4 核心改进**：
- 告别金色主题 → 转向深蓝钢 / 电光蓝 / 冷白 现代金融配色
- 告别漆黑空洞背景 → 明亮围合的办公室自然光照
- 告别宏大城区 80×80 尺度 → 精确 20×15 办公层空间
- 告别纯色发光玩具建筑 → 木纹 / 玻璃 / 哑光金属 PBR 材质
- 新增几何 Agent 角色系统，每个部门拥有独特的柏拉图立体化身
- 数据嵌入空间：工位 KPI、走廊光带、风控墙、会议室投影

---

## 技术栈

| 层级 | 技术 | 版本 |
|------|------|------|
| 3D 引擎 | Three.js (ES Module) | 0.180.0 |
| 模块加载 | Import Map (CDN) | jsdelivr |
| 相机系统 | OrthographicCamera (等距正交视图) | — |
| 阴影系统 | PCFSoftShadowMap | — |
| 色调映射 | ACES Filmic | — |
| 材质系统 | MeshStandardMaterial / MeshPhysicalMaterial | — |
| UI 框架 | 原生 HTML/CSS (Glassmorphism) | — |
| 字体 | Playfair Display + DM Sans + JetBrains Mono | Google Fonts |

---

## 架构说明

```
NexusAI-Prototype-v4.html  (1035行单文件)
├── CSS Layer (~250行)
│   ├── CSS Variables (色彩Token / 间距 / 阴影 / 字体)
│   ├── Layout: Topbar + Sidebar-Left + Viewport + Sidebar-Right + Bottombar
│   ├── Component Styles: KPI Cards / Alert List / Viewport Controls
│   └── Glassmorphism: backdrop-filter + 半透明面板
│
├── HTML Layer (~60行)
│   ├── Topbar: Logo + 导航按钮 (全景/投行/资管/风控/会议)
│   ├── Sidebar-Left: 部门导航列表
│   ├── Viewport: 3D 渲染容器 + 视口控制按钮 + 图例 + Tooltip
│   ├── Sidebar-Right: KPI卡片 ×4 + 告警列表 ×3
│   └── Bottombar: 时间轴滑块 + Agent状态摘要
│
└── JavaScript Layer (~700行)
    ├── Color System: 12色 Three.js 色板 (与CSS变量对应)
    ├── Scene Setup: OrthographicCamera + 4光源 + PCFSoftShadowMap
    ├── Material Factory: woodTexture / deskTopTexture (Canvas程序化纹理)
    ├── Geometry Builders:
    │   ├── createDesk()         → 桌面 + 4腿 + 显示器 + 屏幕发光
    │   ├── createChair()        → 座垫 + 靠背 + 4腿
    │   ├── createMeetingRoom()  → 玻璃隔断 + 金属框架 + 会议桌 + 6椅 + 投影
    │   ├── createRiskWall()     → 深色面板 + 3×2监控屏 + 红色脉冲光圈
    │   ├── createAgent()        → 几何核心 + 双光环 + 底座 + 状态灯
    │   ├── createCorridor()     → 发光平面 + 30个流动数据粒子
    │   └── createPottedPlant()  → 花盆 + 球形植物
    ├── Data Layer:
    │   ├── DEPTS[] → 5个部门定义 (ID/名称/几何体/颜色/状态/位置/工位)
    │   └── KPI Sprites → 桌面悬浮Canvas数值卡片
    ├── Interaction System:
    │   ├── Pan (mousedrag → camTarget平移, 钳制范围 ±9 / -5.5~8.5)
    │   ├── Zoom (wheel → camZoom 0.5×~3.5×)
    │   ├── Hover (raycaster → tooltip)
    │   ├── DoubleClick Focus (raycaster → animateCamera 800ms easeOutCubic)
    │   └── Preset Buttons (6个视角一键切换)
    ├── UI Binding: 左侧列表 / 顶部导航 / 视口按钮 三向联动
    ├── Animation Loop: Agent旋转脉冲 + 走廊粒子流动 + 光环缩放
    └── Resize Handler: 窗口缩放同步更新渲染器和相机投影
```

---

## 色彩 Token 表

| Token 名称 | Hex | RGB | 用途 |
|-----------|-----|-----|------|
| `--navy` | `#1E3A5F` | 30, 58, 95 | 主色：Logo、标题、IT部Agent |
| `--blue` | `#3B82F6` | 59, 130, 246 | 辅色：投行/合规Agent、交互态、数据高亮 |
| `--emerald` | `#10B981` | 16, 185, 129 | 成功态：资管Agent、正常状态 |
| `--amber` | `#F59E0B` | 245, 158, 11 | 警告态：风控Agent、P1/P2告警 |
| `--red` | `#EF4444` | 239, 68, 68 | 危险态：P0告警、风控墙光圈 |
| `--bg-page` | `#F1F5F9` | 241, 245, 249 | 页面底色、3D场景背景 |
| `--bg-panel` | `#FFFFFF` | 255, 255, 255 | 面板背景 |
| `--text-primary` | `#1E293B` | 30, 41, 59 | 主文字 |
| `--text-secondary` | `#64748B` | 100, 116, 139 | 次级文字 |
| `--text-muted` | `#94A3B8` | 148, 163, 184 | 辅助文字 |
| `--border` | `#E2E8F0` | 226, 232, 240 | 边框线 |
| `--glass` | `rgba(255,255,255,0.72)` | — | 玻璃拟态面板 |

### 3D 材质色（Three.js）

| 变量名 | 用途 | 材质类型 |
|--------|------|---------|
| `C.floor` | 地板 | MeshStandardMaterial + 木纹 CanvasTexture |
| `C.wall` | 墙壁 | MeshStandardMaterial, roughness 0.92 |
| `C.deskTop` | 桌面 | MeshStandardMaterial + 桌面 CanvasTexture |
| `C.metal` | 金属框架/桌腿 | MeshStandardMaterial, metalness 0.65 |
| `C.glass` | 玻璃隔断 | MeshPhysicalMaterial, opacity 0.35, roughness 0.05 |

---

## 场景布局图

```
地板尺寸: 20 × 15 单位
相机: 正交等距 (OrthographicCamera)
默认视角: 从右上方45°俯瞰办公层

┌────────────────────────────────────────┐
│  [左墙]                                │
│                                        │
│   🪴                             🪴    │
│                                        │
│  ┌──────────┐          ┌──────────┐   │
│  │ 投资银行部 │          │ 资产管理部 │   │
│  │ -6.5, -5  │          │  3.0, -5  │   │
│  │  4工位    │          │  3工位     │   │
│  │ ◆ 菱形    │          │ ◆ 八面体  │   │
│  └──────────┘          └──────────┘   │
│                                        │
│  ═══════════ [走廊光带] ═══════════   │  (z = -0.5)
│                                        │
│  ┌──────────┐          ┌──────────┐   │
│  │ 风险管理部 │          │ 信息技术部 │   │
│  │ -6.5, 2  │          │  3.0, 2   │   │
│  │  2工位    │          │  4工位     │   │
│  │ ◆ 二十面体│          │ ■ 立方体  │   │
│  └──────────┘          └──────────┘   │
│                                        │
│  [风控监控墙]                              │
│  (-7.2, 1.5)                           │
│                                        │
│  ┌──────────┐     ┌──────────────┐    │
│  │  合规部   │     │    会议室     │    │
│  │ -6.5,6.5 │     │  6.0, 6.5    │    │
│  │  2工位    │     │  玻璃隔断     │    │
│  │ ▲ 四面体 │     │  6椅+投影    │    │
│  └──────────┘     └──────────────┘    │
│                                        │
│   🪴                             🪴    │
│                                        │
│  [远墙]                                │
└────────────────────────────────────────┘

绿植 🪴 位置: 四角 + 走廊中线两端 (共6盆)
```

---

## 部门/Agent 映射表

| 部门ID | 名称 | 几何体 | 面数 | 颜色 | 寓意 |
|--------|------|--------|------|------|------|
| `ibd` | 投资银行部 | 菱形十二面体 (Dodecahedron) | 12 | 电光蓝 `#3B82F6` | 多面性、交易结构 |
| `am` | 资产管理部 | 八面体 (Octahedron) | 8 | 翡翠绿 `#10B981` | 平衡、组合管理 |
| `rm` | 风险管理部 | 正二十面体 (Icosahedron) | 20 | 琥珀 `#F59E0B` | 多维度监控 |
| `it` | 信息技术部 | 立方体 (Box) | 6 | 深蓝钢 `#1E3A5F` | 稳定基础设施 |
| `comp` | 合规部 | 正四面体 (Tetrahedron) | 4 | 电光蓝 `#3B82F6` | 最简稳定规则 |

### Agent 状态视觉语言

| 状态 | 几何核心 | 光环 | 状态灯颜色 |
|------|---------|------|-----------|
| normal (正常) | 缓慢旋转，蓝色发光 | 稳定环形，低脉冲 | 翡翠绿 |
| alert (告警) | 间歇颤动，琥珀色 | 光环断裂，不规则闪烁 | 琥珀/珊瑚红 |
| critical (严重) | 明显抖动，红色 | 光环脉冲剧烈扩散 | 珊瑚红 |

---

## 交互模型说明

### 核心交互

| 操作 | 触发 | 行为 | 动画 |
|------|------|------|------|
| **平移** | 左键按住拖拽 | 场景跟随鼠标移动，视角角度不变 | 实时跟随 |
| **缩放** | 鼠标滚轮 | 正交视图缩放 0.5× ~ 3.5× | 实时 |
| **悬停** | 鼠标静止 200ms | 光标变 pointer，tooltip 显示部门名 | 即时 |
| **聚焦** | 双击工位/Agent/部门 | 相机 easeOutCubic 飞行 800ms | 平滑过渡 |
| **返回** | 双击空白 / 点"全局"按钮 | 相机飞回全局视角 | 800ms |
| **预设** | 点击视口按钮 | 一键切换6个预设视角 | 800ms |

### UI 联动

```
左侧部门列表 ←→ 3D场景聚焦 ←→ 顶部导航按钮
       ↑                              ↑
       └────── 视口预设按钮组 ─────────┘
         (三向同步active态)
```

### 边界约束

| 轴 | 范围 | 说明 |
|----|------|------|
| X | -9 ~ 9 | 视口水平钳制 |
| Z | -5.5 ~ 8.5 | 视口深度钳制 |
| Zoom | 0.5× ~ 3.5× | 缩放硬边界 |

---

## 运行方式

### 方式一：直接打开（推荐）

```
双击 NexusAI-Prototype-v4.html
→ Chrome/Edge 自动打开
→ Three.js 通过 CDN import map 自动加载
```

### 方式二：本地服务器

```bash
# 在文件所在目录启动任意HTTP服务器
npx serve .
# 或
python -m http.server 8080

# 浏览器访问
http://localhost:8080/NexusAI-Prototype-v4.html
```

### 浏览器要求

- Chrome 120+ / Edge 120+ (WebGL2)
- Firefox 115+ (部分 PBR 材质效果可能不一致)
- 屏幕分辨率 ≥ 1280×720
- 需要网络连接（首次加载 Three.js r180 CDN 资源）

---

## 已知限制

1. **正交缩放**：当前 `updateCamera()` 仅改变相机位置，未同步更新正交投影矩阵，缩放功能表现为相机平移而非真正放大。修复：在 `updateCamera()` 末尾添加 `camera.left/right/top/bottom` 按 `camZoom` 缩放并调用 `updateProjectionMatrix()`。

2. **时间轴**：底部时间轴滑块仅有 UI 骨架，无实际回放逻辑。

3. **数据驱动**：KPI 数值和风控墙屏幕颜色为静态/随机生成，未连接真实数据源。

4. **单文件**：所有 CSS/HTML/JS 在单一文件中，适合原型阶段。生产环境建议拆分为模块。

---

## 相关文档

| 文档 | 路径 | 说明 |
|------|------|------|
| 设计审计报告 | `NexusAI-Design-Audit-v4.md` | v4 设计方案来源（标杆分析 + 问题诊断 + 方案建议） |
| 设计终审报告 | `NexusAI-Design-Audit-v4.html.review.md` | 对 v4 原型的逐项审查结论 |
| 测试规范 | `NexusAI-Test-Spec-v4.md` | 视觉/交互/动画/性能测试用例 |
| 项目文档 | `NexusAI-README-v4.md` | 本文件 |

---

> **设计精神**: 精致（每个像素都有意图）、专业（金融不是游戏）、明亮（告别黑暗空洞）、有生命力（几何Agent的状态律动 + 数据在空间中流动）。