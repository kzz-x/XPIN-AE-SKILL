# Motion｜Profiles

> 仅在需要明显动画设计时加载。Profile 用来决定对象“应该怎么动”，避免所有对象共享同一套 Easy Ease + Overshoot。

## 1｜选择方式

先选 1 个主 Profile，再按需要叠加 0–2 个 Modifier。

```text
Motion = Profile + Modifier + Scene Rhythm
```

不要为了追求复杂而混合过多风格。

## 2｜基础 Profiles

| Profile | 核心感觉 | Anticipation | Inertia / Weight | Overshoot | Stagger / Overlap | Settle |
|---|---|---|---|---|---|---|
| **UI** | 快、轻、精准、响应明确 | 通常无或极小 | 低 | 小或无 | 短、功能性 | Snap / Soft Settle |
| **Mechanical** | 有重量、受约束、锁定感 | 可有 | 中高 | 极小 | 按机构顺序 | Hard / Damped Settle |
| **Typography** | 节奏、层级、阅读优先 | 可有 | 中 | 小 | 可明显 | Visual Settle |
| **Data** | 信息清晰、逻辑明确 | 通常无 | 低 | 通常无 | 按逻辑顺序 | Hold |
| **Camera** | 连续、稳定、空间可信 | 隐性 | 高 | 极小 | 不适用 | Cinematic Settle |
| **Soft Graphic** | 弹性、流动、形变 | 可明显 | 中高 | 可明显 | Organic | Damped / Elastic Settle |

## 3｜Profile 执行规则

### UI
- 响应快，位移和缩放幅度克制。
- 常用短 ease-out、吸附、裁切、mask / reveal、状态切换。
- Overshoot 只作为轻微反馈，不默认弹跳。
- 多卡片 Stagger 通常短而清晰；优先表达层级和操作顺序。
- 组件进入完成后尽快稳定，保证可读。

### Mechanical
- 先判断驱动力、连接关系、关节和锁定位置。
- 重物启动和停止不能像 UI 一样轻飘。
- 可使用短 anticipation、加速、硬停止、极小反冲。
- 联动部件使用 Follow Through / Overlap，而不是全部同步。
- 精密机械禁止无理由果冻感、弹簧感。

### Typography
- 以阅读顺序和语义层级决定 Timing。
- 标题、数字、标签可以使用不同速度 / delay，不要整段文字统一飞入。
- 优先 Text Animator、Mask、Tracking、Baseline、字符级 stagger 等文字原生能力。
- Secondary motion 服务语义强调，不抢正文阅读。

### Data
- 数值增长、图表展开、扫描、排序必须跟逻辑关系一致。
- 动画应帮助理解“先后 / 比较 / 增长 / 变化”，而不是仅装饰。
- 常用线性或克制 ease；极少使用 bounce / overshoot。
- 数据状态变化后留足 Hold。

### Camera
- Camera 是镜头级动作，不是装饰性 Position 动画。
- 优先连续速度、连续方向和明确目标；转向 / 推拉要有视觉动机。
- 需要时使用 Camera Rig / Target Null。
- Camera settle 极轻；不要像 UI 卡片一样 overshoot。
- 主体动画和 Camera 同时发生时，明确谁承担 Primary Beat。

### Soft Graphic
- 可以使用明显 anticipation、squash & stretch、overshoot、follow through。
- 形变要维持体积感 / 方向感，不要随机 wobble。
- 附属部分允许更慢 settle，形成有机层次。

## 4｜Modifiers

Modifier 改变 Profile 的力度，而不是替换它。

- **HEAVY**：更慢启动 / 停止、更明显惯性、更小反弹。
- **LIGHT**：更快响应、更短 settle、更小时间跨度。
- **PRECISE**：减少 overshoot / wobble，强调吸附、锁定、对齐。
- **AGGRESSIVE**：更快加速、更大速度对比、更短 Hold，但仍需可读。
- **CALM**：更长 timing、更柔和速度变化、更少同时事件。
- **ELASTIC**：增加可解释的形变 / overshoot / follow through。
- **INDUSTRIAL**：强调结构、质量、机械顺序、锁定，不做廉价赛博弹跳。
- **EDITORIAL**：强调构图切换、文字节奏、停顿与视觉对比。

例：
```text
Mechanical + HEAVY + INDUSTRIAL
Typography + AGGRESSIVE + EDITORIAL
UI + PRECISE
Camera + CALM
```

## 5｜同场景差异化

一个镜头可包含多个 Profile，但每个对象必须按自身属性运动。

例如工业 UI 镜头：
- Camera → Camera + CALM
- 主机械装置 → Mechanical + HEAVY + INDUSTRIAL
- 浮层窗口 → UI + PRECISE
- 数值 → Data
- 标题 → Typography + EDITORIAL

它们可以共享节奏，但不应共享完全相同的曲线。

## 6｜反模式

若出现以下情况应重新分配 Profile：
- UI / 机械 / 文字 / Camera 全部同一 ease；
- 每个对象都有相同 8% Overshoot；
- 所有 stagger 都等间距、同长度；
- Camera 用“弹一下”表现高级感；
- Data 为了活泼加入无意义 bounce；
- 重机械 5–8 帧瞬间起停却没有设计意图。
