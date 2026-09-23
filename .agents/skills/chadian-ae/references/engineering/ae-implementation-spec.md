# Engineering｜AE Implementation Spec

用于 **AE 设计 / 制作方案阶段**。目标是让方案像真人 Motion Designer 写给 AE 执行人员的施工说明，而不是只描述“画面长什么样、怎么动”。

核心：

**设计阶段同时决定视觉意图与 AE 实现方法。不要把技术选择全部推迟到 Codex 执行阶段。**

---

## 1｜每个主要元素都要回答“AE 里怎么做”

方案中按需要明确：
- 用什么 Layer / Precomp / Matte / Parent / Null；
- 哪些属性用真实 Keyframe；
- 哪些持续 / 程序化行为用 Expression；
- 是否使用 Text Animator / Shape Operator / Repeater / Time Remap；
- Layer Style / Native Effect / Adjustment Layer 怎么承担材质；
- 是否需要第三方插件；
- 哪些参数进 CTRL；
- 哪些参数保持局部；
- AI 可以自动做什么；
- 哪些最终必须由人手调。

不要只写：
“轻微漂浮 / 有弹性 / 玻璃质感 / 高级一点”。

要写成可执行方法。

---

## 2｜Keyframe / Expression / Relationship 的选择

### 优先真实 Keyframe
这些通常由人类 Motion 判断主节奏：
- Hero 主体入场 / 出场；
- 产品关键运动；
- 重要 Typography；
- Camera push / orbit / reveal；
- 音乐卡点；
- 创意转场；
- 明确路径与 Pose；
- 需要 Graph Editor 精修的 Timing / Spacing。

原因：最终很可能需要“早 2 帧、慢一点、弧线再圆一点”的人工调整。

### 优先 Expression
这些通常是持续、程序化、规则化行为：
- 低频漂浮 / wiggle；
- 呼吸 / oscillation；
- loop / ping-pong；
- 随机但受控的轻微扰动；
- distance-driven scale / opacity / blur；
- Follow / Attach / Connect；
- 路径或目标跟随；
- 自动连线；
- sourceRectAtTime 响应式尺寸；
- 数字递增 / 自动计数；
- 基于 Marker / Index / Delay 的派生；
- 共享 Offset / Stagger；
- 简单 spring / decay / settle，且不承担主节奏。

### Hybrid
高质量镜头经常是：

**主节奏 Keyframe + 程序化 Secondary Motion / Settle / Relationship**

例如：
- Position 主运动用关键帧；
- 尾部微弱 spring 用表达式；
- 卡片整体入场用关键帧；
- 常驻轻漂浮用低频 wiggle；
- Hero Camera 用关键帧；
- 辅助 HUD 延迟由 Marker / Index 派生。

不要追求“零关键帧”，也不要为了可编程把主 Motion 全塞进 Expression。

---

## 3｜常见技巧在设计阶段就写出来

### Wiggle / Ambient Motion
若只是常驻微动：
- 不手 K 大量随机关键帧；
- 用低频、低幅 `wiggle()` 或等价受控噪声；
- 建议把 Frequency / Amplitude 放到 CTRL；
- Hero 主体默认克制，UI / 装饰幅度更小。

### Spring / Bounce / Overshoot
先判断对象是否应该弹。

- UI：通常快速、轻微、克制；
- 机械：优先锁定感，弹性极少；
- 软性图形：可更明显；
- Hero 产品：主 timing 用关键帧，必要时仅尾部加入很小 settle。

能稳定参数化的 Secondary Settle 可用 Expression；
决定主节奏 / 重量感的 Overshoot 优先真实 Keyframe + Graph。

### Loop
规则循环优先：
- loopOut / Time Remap / Precomp；
- 不复制数十段重复关键帧。

### Relationship
A 的结果依赖 B：
- Parent / Null / Layer Control / Point Control / Expression；
- 不把 B 当前坐标复制成 A 的固定数值。

---

## 4｜视觉材质也要写实现方法

设计方案应主动考虑 AE 原生能力，而不是默认 Shape 堆砌。

常见：
- 描边 → Layer Style / Stroke / Native Effect / Shape Stroke，按用途选择；
- 阴影 → Drop Shadow / Layer Style，特殊透视才独立 Shadow Layer；
- Inner Shadow / Inner Glow / Bevel → 适合时直接用 Layer Style；
- Blur → Fast Box Blur / Gaussian Blur / Camera DOF / 已有模板效果，按需求；
- Grain / Noise → Adjustment Layer 或统一后期；
- Reveal → Mask / Track Matte；
- 逐字动画 → Text Animator；
- 规则重复 → Repeater；
- 多层统一调色 / Blur / Grain → Adjustment Layer。

方案里要说明“为什么选这个实现”，尤其当存在多条路线时。

---

## 5｜Plugin Policy

插件不是禁用项，也不是默认项。

### 应用插件
- 已有优秀模板真实依赖该插件；
- 插件显著提升材质 / 效率 / 可编辑性；
- 原生替代会明显降质或复杂很多；
- 用户明确要求复用现有插件系统。

### 不要
- 未读取工程就猜插件；
- 为了“高级”堆插件；
- 把插件所有参数暴露到 CTRL；
- 已有材质系统可复用时重新发明一套原生近似版。

### Template Style Migration
先读取源模板实际：
`Effect Chain → Plugin → Layer Style → Blend Mode → Precomp → Expression → CTRL`

再决定迁移方式。

如果插件缺失：
- 明确指出；
- 提供原生 fallback 或保留占位；
- 不伪造成功。

---

## 6｜Control Surface 在设计阶段确定

只暴露高频真正会改的参数。

建议按需要拆：
- `CTRL_GLOBAL`
- `CTRL_LAYOUT`
- `CTRL_MOTION`
- `CTRL_STYLE`
- `CTRL_MATERIAL`
- `CTRL_CAMERA`

常见可控项：
- Duration / Speed；
- Stagger / Delay；
- Motion Strength；
- Wiggle Frequency / Amplitude；
- Overshoot / Settle；
- Offset；
- Color / Stroke / Shadow；
- Blur / Glass / Distortion；
- Plugin 的少量关键参数；
- 全局开关。

**Shared motion goes upward. Unique motion stays local.**

不要为了“AI 方便”把全部参数集中成一个巨型控制面板。

---

## 7｜AE IMPLEMENTATION 输出块

AE 制作方案中，对主要镜头至少给一个精简实现块：

```text
AE IMPLEMENTATION
Structure: 主体 Precomp + HUD Precomp + CTRL_MOTION
Primary Motion: Position / Scale 真实关键帧，Graph Editor 精修
Secondary Motion: 低频 wiggle Expression
Settle: 轻微 spring，仅用于尾部
Material: 复用 Liquid Glass Template 的现有 Effect / Plugin Chain
Layer Style: Stroke + Inner Shadow + Highlight
Relationship: HUD 跟随主体 Null；连线实时引用端点
Controls: Duration / Stagger / Glass Amount / Wiggle Strength
Editable by Human: 主 Timing、路径、Hero Pose
AI Tasks: 绑定 CTRL、表达式、批量扩展、材质迁移、命名整理
```

M0 / 极简单 Patch 不需要机械输出完整块。

复杂镜头、Previs 已通过、或要把完整方案交给 Codex 稳定执行时，不要继续把施工细节堆在自然语言方案里；升级为 `ae-build-spec.md`，形成结构化 Build Contract。

---

## 8｜设计者提醒

当用户只给了视觉目标，但以下实现信息会显著影响质量时，设计阶段应主动补全或提醒：
- 主 Motion 应 Keyframe 还是 Expression；
- 是否有现成模板 / AEP / Motion 可复用；
- 是否依赖插件；
- 主体是否需要真实素材 / 3D；
- 哪些效果必须保留人工 Graph；
- 是否需要统一 CTRL；
- 是否有类生物 / 复杂物理，需要 Reference First。

如果可以合理判断，直接给建议，不要把所有问题都丢回用户。

---

## 9｜最终原则

**像真人 AE 设计师一样设计实现，不只像导演一样描述结果。**

优先级：
1. 视觉 / Motion 意图正确；
2. 实现方式符合 AE 习惯；
3. 人工易调；
4. AI 易扩展；
5. 自动化方便。

第 5 条不能反过来支配前 4 条。


---

## 10｜Motion Primitive Vocabulary

当需要描述可复用程序化动作时，优先使用 `../motion/motion-primitives.md` 的标准词汇，例如：
`EASE / SPRING / FOLLOW / DRIFT / RECOIL / KINETIC / PATH_FOLLOW / STAGGER / SEQUENCE / RETIME`。

方案至少说明：
- Primary 还是 Secondary；
- Keyframe / Expression / Hybrid；
- 核心可调参数；
- 哪些对象禁用该 Primitive；
- 是否最终需要 Bake 为关键帧。

不要用“弹性一点 / 丝滑一点 / 有点惯性”代替实现定义。
