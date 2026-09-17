# Quality｜Animation QA

> 仅对 M2–M4 或用户明确要求深度动画验收时加载。小型 Patch 不做全套 Motion 审计。

## 1｜Motion Design

检查：
- 是否出现“所有对象 Easy Ease”的通用模板化；
- UI / 机械 / 文字 / 数据 / Camera 是否错误共享同一运动逻辑；
- Timing 与 Spacing 是否匹配对象重量和动作意图；
- 是否存在无理由 bounce / shake / rotation / overshoot；
- 需要重量感的对象是否过轻；需要快速响应的 UI 是否过拖；
- 是否缺失必要的 anticipation / deceleration / settle；
- Follow Through / Overlapping Action 是否用于真正存在层级或连接关系的对象；
- Secondary Action 是否抢主动作。

发现重复模式时不要只微调数值，应回到 `motion-principles.md` / `motion-profiles.md` 重选逻辑。

## 2｜Rhythm / Staging

检查：
- 是否所有元素同时开始、同时结束；
- Stagger 是否机械等间距，还是有视觉分组和层级；
- 是否存在 Primary Beat / Secondary Beat / Rest Beat；
- 是否给重要文字 / 数据足够 Hold；
- 是否整段一直在动，没有稳定状态；
- 动作密度是否符合信息量；
- 转场前后是否存在清晰视觉承接。

## 3｜Weight / Inertia

检查：
- 加速和减速是否合理；
- 重物是否存在足够惯性；
- 精密机械是否有不必要弹性；
- Soft Graphic 是否完全没有 follow through，导致僵硬；
- Overshoot 幅度、次数和衰减是否与材质 / 对象属性匹配；
- Settle 是否过长、过短或无限抖动。

## 4｜Camera Continuity

Camera 单独检查：
- 运动是否有明确目标；
- 前后方向、速度、空间关系是否连续；
- 是否突然急停 / 无理由改向；
- Camera 与主体动画是否同时争夺 Primary Beat；
- 推拉、平移、环绕、目标跟随是否真的需要真实 Camera；
- Camera settle 是否克制，禁止 UI 式弹跳。

## 5｜Motion Architecture

检查：
- 3 个以上图层共享同类运动时，是否仍在复制关键帧；
- 是否存在十几层几乎相同 Position / Scale / Opacity 动画；
- 整体运动是否应交给 Parent Null / Rig；
- 重复模块是否应 Precomp 后统一 retime；
- 是否可以使用 Master Progress + Expression / Stagger；
- Marker / Control / Time Remap / Layer Keyframes 职责是否混乱；
- 是否出现多套功能重复的 CTRL_动画；
- Expression 是否为了“自动化”变成难维护黑盒；
- 单个对象是否仍可局部 override。

## 6｜Editable QA

人工接手时应能快速完成：
- 整体改快 / 改慢；
- Stagger 增减；
- 全局 Motion Strength 调整；
- Camera 单独调整；
- 单个对象改 timing；
- 某一 Profile 的 overshoot / settle 强度修改。

如果必须逐层翻找几十个关键帧，Motion Architecture 不合格。

## 7｜通过标准

复杂动画通过 QA 时应同时满足：

**运动有意图 + 对象有差异 + 节奏有主次 + Camera 连续 + 关键帧不无脑复制 + 人工可快速调整。**
