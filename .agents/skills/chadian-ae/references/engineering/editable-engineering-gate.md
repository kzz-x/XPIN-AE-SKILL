# Engineering｜Editable Engineering Gate

> 这是完整动画 / 多图层 Motion 的**执行门禁**，不是审美参考。目标：视觉成立的同时，工程必须少重复关键帧、层级清晰、可被人工继续修改。

## 1｜触发条件

命中任一项就应用本 Gate：
- 从零制作完整动画镜头；
- M2–M4 多对象 Motion；
- 3+ 图层存在共享 Position / Scale / Rotation / Opacity / Timing；
- 用户明确要求“方便我后续自己改”“做成可编辑工程 / 模板”；
- 当前工程已出现密集关键帧、重复 Transform、无 Parent / Rig 的问题。

## 2｜Pre-animation Gate：先搭骨架，再打主关键帧

首个 Primary Motion 关键帧前，先完成：

```text
00_CTRL
├─ NULL_场景 / Camera Rig
├─ NULL_主体组 / UI组 / 模块组
│  └─ Visual Layers
└─ 高频人工控制
```

并声明每段运动由谁负责：
- Global Motion → Scene / Camera Rig
- Group Motion → Group Null / Parent
- Repeated Motion → Master / CTRL / Expression / Stagger
- Local Motion → Visual Layer
- Secondary Motion → Local Keyframe 或短 Expression

**Shared motion goes upward. Unique motion stays local.**

同一段整体运动不得同时在 Parent 与 Child 重复实现。

## 3｜Keyframe Budget

目标不是零关键帧，而是**少量有意义的关键帧 + 真正的曲线**。

普通单阶段 A → B：
- 默认从 2 个主关键帧开始；
- anticipation / overshoot / settle 通常增加到 3–4 个；
- 速度感用 Temporal Ease / Spatial Bezier / Graph Editor 调，不用增加一串 key 模拟。

除非用户明确要求 Tracking / 数据采样 / Bake / 逐帧动画，否则禁止：
- frame loop 连续 `setValueAtTime()`；
- 每帧或高密度生成 Transform key；
- 为了同步而向多个图层复制同一套 key；
- 用关键帧数量代替 easing。

## 4｜Animation / Adjustment 分离

### 首选：Parent 动画 + Child 本地调整

```text
NULL_标题动画     ← 主运动
└─ 标题文字       ← 用户直接拖动 / 缩放 / 旋转做构图微调
```

这样“改动画”和“改构图”天然分离。

### 同一属性必须叠加人工 Override

保留原始 keyframe，让 Expression 只增加 Offset：

```jsx
// Position
value + effect("位置偏移")("Point")

// Rotation
value + effect("旋转偏移")("Slider")

// Opacity
clamp(value + effect("透明度偏移")("Slider"), 0, 100)

// Scale：优先比例控制
s = 1 + effect("缩放比例")("Slider") / 100;
value * s
```

3D Position 使用合适的 3D Offset / XYZ 控制，不要硬套 2D Point。

原则：
- 能用 Parent + Child Local Transform，就不要额外写 Expression；
- 同属性必须叠加时，优先 `value + offset` / multiplier；
- Follow / Delay / Secondary Motion 再使用 `valueAtTime()` 等；
- 不改写、平移、复制原始 key 来实现“手动偏移”。

## 5｜Editable QA｜机器检查

复杂动画完成 Primary Motion 后，**在 Secondary Motion 前先检查一次**；交付前再检查一次。

至少输出：
1. 所有动画图层的 Parent / Rig；
2. 每个动画属性的 Keyframe Count；
3. 3+ 图层重复 / 近似 Transform 候选；
4. Controller / Expression / Master Progress；
5. 每个主要对象的人工 Override 路径；
6. Parent 与 Child 是否重复承担同一运动。

### QA FAIL

出现任一项就先修工程：
- 普通单阶段动作出现明显不必要的密集关键帧；
- 非 Tracking / 数据 / Bake 却逐帧写 key；
- 3+ 图层复制相同 Transform 动画；
- 明显整体运动没有 Parent / Rig / Master；
- Parent 与 Child 双重执行同一整体 Transform；
- 用户改单个对象只能破坏原动画 key；
- 主要控制入口 30 秒内无法找到。

修复优先级：
`Parent / Rig → Master / Expression → Precomp / Time Remap → Local Keyframes`。

## 6｜完成标准

交付必须同时满足：

**Visual Correct + Motion Correct + Editable Structure + Non-destructive Manual Override**

关键判断：
- 整体移动只改一个 Parent / Rig；
- 单个对象构图可以独立改；
- 原动画不用重做；
- Graph Editor 中保留的是少量真正有意义的主关键帧；
- AI 做完后，人类仍能像正常 AE 工程一样继续工作。
