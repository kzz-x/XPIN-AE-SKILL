# 01｜AE Capability Map

目的：防止 Agent 只会 Position / Scale / Opacity / Shape。

正式制作前快速扫一遍能力类别，只在触发时加载详细模块。

## 二维 / 矢量
- Shape Layer
- Shape Path / Morph
- Trim Paths
- Repeater
- Text / Text Animator
- Range Selector / Expression Selector
- SVG / AI / PSD
- Mask Path / Expansion / Feather
- Track Matte
- Blend Mode
- Layer Style

适合：UI、图表、路径、标签、线条、文字系统、图标。

## 时间 / 动画
- Keyframes
- Graph Editor
- Motion Blur
- Marker
- Time Remap
- Time Stretch
- Posterize Time
- Expression
- Parent / Null
- Precomp
- Essential Properties

触发：复杂时序、循环、模块复用、模板、批量动画。
详细读：`engineering/animation-and-timing.md`

## 合成 / 后期
- Adjustment Layer
- Color Correction
- Blur / Sharpen
- Distort / Displacement
- Noise / Grain
- Keying
- Channel / Matte
- Generate
- Stylize
- Perspective
- Time Effects

触发：统一质感、抠像、扭曲、后期处理。
详细读：`capabilities/effects-and-plugins.md`

## 空间 / 3D
- 2.5D Layer
- Camera
- Light
- Depth of Field
- Camera Rig / Target Null
- Z-space / Parallax
- Advanced 3D
- 3D Model
- Parametric Mesh（仅在当前 AE 版本与工具接口实际可用时）
- 外部 GLB / GLTF / OBJ / 渲染序列 / EXR

触发：真实空间、多面物体、镜头环绕、景深、透视、真实 3D 模型。
详细读：`capabilities/3d-camera-models.md`

## 素材
- PNG / JPG
- PSD
- AI / SVG
- Footage
- Image Sequence
- Audio
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

触发：根据任务路由加载对应 Workflow。

## 插件
只在：
1. 原生不足；
2. 插件明显提高质量 / 效率；
3. 已安装或用户同意依赖；
4. 能可靠调用；
时使用。

详细读：`capabilities/effects-and-plugins.md`

## 快速决策

真实复杂对象
→ 官方 / 实拍 / 3D / 外部高质量资产
→ 不要低质量 Shape 手绘

二维信息设计
→ AE 原生矢量 / Text / Mask / Effects

真正空间关系
→ 3D / Camera / Model

只是二维推拉
→ 不要强行 Camera

重复模块
→ Precomp / Repeater / Essential Properties

复杂时间
→ Marker / Time Remap / 控制器

统一后期
→ Adjustment Layer + Native Effects
