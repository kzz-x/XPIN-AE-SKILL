# 01｜AE Capability Map

目的：防止 Agent 只会 Position / Scale / Opacity / Shape，同时避免为了动画质量默认加载整套 Motion System。

正式制作前快速扫能力类别，只在触发时加载详细模块。

## 设计 / AI 协作

当任务不是单纯执行，而是在决定“这个镜头怎么做、AI 应该做多少、如何交给 Codex”时：
- AI 适用性 / Production Mode / Creative Authority → `workflows/creative-authority.md`
- AE 施工方法（Keyframe / Expression / Relationship / Layer Style / Native Effect / Plugin / CTRL）→ `engineering/ae-implementation-spec.md`

普通改字、改色、改参数、小型 Patch 不读取这两个模块。

## AE Native Decision

创建新元素、效果或动画结构时，不只检查“AE 能不能做”，还要检查：
- 是否存在更直接的 Native Feature；
- 是否正在用 Shape / Keyframe 手工模拟已有功能；
- 是否存在对象 Relationship；
- 是否应该动态引用而不是烘焙坐标 / 尺寸；
- 用户以后最可能修改什么。

创建 / 重构视觉元素时详细读：
`capabilities/native-ae.md`

出现 Attach / Follow / Target / Carry / Connector / Auto Layout / Dynamic Bounds / Constraint / Destination-driven Motion 时，再读：
`motion/relationship-rigs.md`

---

## 二维 / 矢量
- Shape Layer / Shape Path / Morph
- Trim Paths / Repeater
- Text / Text Animator
- Range Selector / Expression Selector
- SVG / AI / PSD
- Mask Path / Expansion / Feather
- Track Matte
- Blend Mode
- Layer Style

适合：UI、图表、路径、标签、线条、文字系统、图标。

## 时间 / 动画
- Keyframes / Graph Editor
- Motion Blur
- Marker
- Time Remap / Time Stretch
- Posterize Time
- Expression
- Parent / Null
- Precomp
- Essential Properties

普通 M0–M1：
→ `engineering/animation-and-timing.md`

复杂 M2–M4 按 Router 增量加载：
- 运动原则 → `motion/motion-principles.md`
- 对象运动差异 → `motion/motion-profiles.md`
- 关系 / 约束 → `motion/relationship-rigs.md`（触发时）
- Master / Parent / Precomp / Local → `motion/motion-control-architecture.md`
- 深度动画验收 → `quality/animation-qa.md`

## 合成 / 后期
- Adjustment Layer
- Color Correction
- Blur / Sharpen
- Distort / Displacement
- Noise / Grain
- Keying
- Channel / Matte
- Generate / Stylize / Perspective / Time Effects

触发：统一质感、抠像、扭曲、后期处理。
详细读：`capabilities/effects-and-plugins.md`

## 空间 / 3D
- 2.5D Layer
- Camera / Light / Depth of Field
- Camera Rig / Target Null
- Z-space / Parallax
- Advanced 3D / 3D Model
- Parametric Mesh（仅当前版本与工具实际支持时）
- GLB / GLTF / OBJ / Render Sequence / EXR

触发：真实空间、多面物体、镜头环绕、景深、透视、真实 3D。
详细读：`capabilities/3d-camera-models.md`

复杂 Camera / 多对象运动额外走 Motion Router。

## 素材
- PNG / JPG / PSD / AI / SVG
- Footage / Image Sequence / Audio
- 3D Asset
- 官方品牌资源
- 外部生成图片 / 视频 / 纹理

触发：真实产品、人物、工厂、机械、车辆、品牌、复杂结构。
详细读：`engineering/assets-and-replacement.md`

## 模板 / 交付
- Essential Graphics
- MOGRT
- Essential Properties
- Replacement Slots
- Design Tokens
- Stable AI_ID

触发：模板、重复项目、PR 可编辑交付。
详细读：`capabilities/mogrt-essential-properties.md`

## 执行
- MCP Direct Control
- JSX / ExtendScript
- Hybrid MCP + JSX

根据 Task Router 加载对应 Workflow。

Raw JSX / AE26 宿主陷阱按需读取：
`engineering/ae26-scripting-gotchas.md`

仅在 Shape Contents / `addProperty`、Parent / 坐标空间、Repeater、图层重排、KeyframeEase，或对应脚本故障命中时加载；普通 MCP Patch 不读取。

## 插件
只在：
1. 原生不足；
2. 插件明显提高质量 / 效率；
3. 已安装或用户同意依赖；
4. 能可靠调用；
时使用。

## 快速决策

真实复杂对象
→ 官方 / 实拍 / 3D / 高质量资产

二维信息设计
→ AE Native Text / Shape / Mask / Effect

已有语义对应功能
→ Native Feature Before Manual Construction

对象有关联
→ Relationship Rig Before Independent Keyframes

真正空间关系
→ 3D / Camera / Model

只是二维推拉
→ 不强行 Camera

重复模块
→ Precomp / Repeater / Essential Properties

简单时间
→ Marker / Keyframe / Graph

复杂共享时间
→ Master Progress / Parent / Precomp + Time Remap / Expression

统一后期
→ Adjustment Layer + Native Effects
