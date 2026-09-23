# Motion｜Primitive Library

> Motion Principles 决定“为什么这样动”，Motion Profiles 决定“这种对象应该怎么动”，Primitive Library 决定“用哪一种成熟动作积木实现”。M2–M4 或方案明确需要程序化动作时按需加载。

原则：

**Primitive is an implementation vocabulary, not a style preset.**

## 1｜选择格式

推荐在设计 / BUILD SPEC 中写：

```text
Profile: UI + PRECISE
Primary: EASE_OUT
Secondary: SPRING_SETTLE
Stagger: GROUPED
Implementation: primary keyframes + secondary expression
```

不要写成“加点高级弹性”。

## 2｜核心 Primitives

### EASE
Use：绝大多数主位移、缩放、状态变化。
Implementation：真实 KF + Graph 优先。
Controls：duration / influence / velocity。
Avoid：把同一 Ease 套全部对象。

### SPRING
Use：Secondary settle、软性 UI、柔性图形。
Controls：mass / stiffness / damping 或 bounciness / settle time。
Implementation：Expression 或 baked KF。
Avoid：精密机械、Data、Hero Camera 的主运动。

### RECOIL
Use：按钮点击、机械冲击后的短反冲。
Controls：strength / decay / duration。
Avoid：常驻摇晃。

### FOLLOW
Use：附属元素、标签、secondary parts。
Controls：delay / damping / offset。
Implementation：Relationship + Expression / delayed progress。
Avoid：本应严格锁定的机械连接。

### DRIFT
Use：环境性漂浮 / 背景微动。
Controls：amplitude / frequency / axis。
Implementation：低频受控 noise / wiggle。
Avoid：Hero 主体抢注意力。

### BOUNCE
Use：明确弹性碰撞或活泼反馈。
Controls：height / count / decay。
Avoid：把 bounce 当万能高级感。

### LEAN
Use：移动方向导致的轻微倾斜 / 惯性姿态。
Controls：velocity mapping / max angle。
Avoid：静态 UI / 重机械无依据旋转。

### KINETIC
Use：根据速度派生 rotation / blur / scale / trailing。
Controls：velocity gain / clamp / smoothing。
Implementation：velocity-driven expression。
Avoid：无法解释的装饰变化。

### SQUASH_STRETCH
Use：软性图形、卡通、明确冲击反馈。
Controls：amount / volume preserve / settle。
Avoid：刚性产品、精密设备。

### THROW
Use：带初速度抛出、滑行、甩动。
Controls：initial velocity / drag / gravity-like force。
Avoid：需要精确停点的 UI。

### PATH_FOLLOW
Use：沿显式路径移动、装配、指针、物流。
Controls：progress / path bend / orientation。
Implementation：Path Rig / Expression / Null controls。
Avoid：把复杂 Position KF 当隐式路径。

### STAGGER
Use：3+ 相似对象的节奏展开。
Controls：delay / order / grouping / curve。
Implementation：Master Progress / index / spatial order / Marker。
Avoid：机械固定等间隔。

### SEQUENCE
Use：有明确先后逻辑的模块。
Controls：phase duration / overlap / hold。
Avoid：所有模块完全串行造成 PPT 感。

### RETIME
Use：模块整体快慢、重复动画适配不同时长。
Implementation：Precomp + Time Remap / Stretch / Master Progress。
Avoid：逐层拖几十个 KF。

## 3｜Live vs Bake

程序化 Primitive 可以：
- Live Expression：适合持续可调、关系驱动、快速迭代；
- Baked Keyframes：适合最终人工 Graph、Lottie / Render Farm / 兼容性；
- Hybrid：主节奏 KF，Secondary live。

默认不以“零关键帧”为目标。

## 4｜Primitive Controls

只有高频参数进入 CTRL：
- Strength
- Duration
- Delay / Stagger
- Damping / Settle
- Frequency / Amplitude
- Path Bend
- Max Angle
- Motion Blur Amount

不要暴露物理模型所有底层变量。

## 5｜Profile 映射建议

- UI + PRECISE → EASE / short STAGGER / tiny SPRING_SETTLE
- Mechanical + HEAVY → EASE / RECOIL(optional) / SEQUENCE / PATH_FOLLOW
- Typography + EDITORIAL → EASE / STAGGER / SEQUENCE / HOLD
- Data → EASE / RETIME / SEQUENCE，通常无 BOUNCE
- Camera + CALM → EASE / RETIME / Target Relationship，几乎无 spring
- Soft Graphic → SPRING / FOLLOW / SQUASH_STRETCH / DRIFT

这只是默认起点；真实 Reference / Previs 优先。

## 6｜晋升新 Primitive

只有满足以下条件才加入核心库：
- 多个案例重复出现；
- 行为可以参数化；
- 适用 / 不适用边界清楚；
- AE 实现可靠；
- 不依赖某个一次性镜头。

单次成功动作先进入 Pattern Library，不直接污染 Primitive 核心。
