# Capability｜3D / Camera / Models

## 先判断
如果二维层已经能自然表达，不强行 3D。
真正需要以下内容时再上：
- 多面物体
- 真实透视
- 镜头环绕
- 空间遮挡
- Parallax
- 景深
- 真实 3D 模型

## 可考虑
- 2.5D Layer
- Camera
- Light
- DOF
- Camera Rig / Target Null
- Advanced 3D
- AE 支持的 3D Model
- Parametric Mesh（必须先确认当前 AE Build 与 MCP / Script API 实际可用）
- 外部 GLB / GLTF / OBJ
- 外部 EXR / PNG / Sequence

## Camera
Camera 与对象动画分离。
优先：
`Camera → NULL_镜头Rig → NULL_目标`

不要把大量难编辑关键帧直接堆 Camera。

## Model Strategy
复杂真实对象：
1. 官方 / 高质量现成 3D 模型
2. 外部 3D 工具生成 / 修改
3. AE 导入 + Camera / Lighting / Compositing

不要把 AE 当 Blender / C4D / Houdini。

默认不承诺用 JSX 稳定完成：
- 顶点级建模
- UV
- 骨骼 Rig
- 高级材质节点
- 复杂动力学 / 流体 / 布料

## Parametric / Native 3D
如果当前版本支持并且 MCP / Script 能可靠创建：
优先用原生真 3D 体块解决“需要真实多面”的简单几何对象。
如果 UI 有能力但自动化接口不可用：
可以明确建立手动 handoff，而不是退化成低质量伪 3D。

## Quality
沙盘 / 城市 / 产品 3D：
- 模型要有真实厚度与多个可见面
- 统一比例与材质语言
- 合理 AO / 阴影 / 接触感
- Camera 运动稳定
- 不用 Shape 假装复杂真 3D
