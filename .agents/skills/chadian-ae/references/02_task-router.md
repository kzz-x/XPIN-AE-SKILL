# 02｜Task Router

先路由，再加载。不要“任务复杂 = 读全部”。

## A｜EXISTING_PROJECT_PATCH
典型：
- 改选中图层
- 调文字 / 颜色 / 尺寸 / 关键帧
- 替换素材
- 修改一个窗口 / 图标 / 模块

加载：
- `workflows/modify-existing.md`
- `workflows/mcp-direct-control.md`（若 MCP）
- 与目标属性有关的 1 个 Engineering / Capability 模块

禁止默认加载完整规范。

## B｜NEW_PROJECT
典型：
- 从零搭一个镜头
- 创建完整可编辑工程

加载：
- `workflows/new-project.md`
- `engineering/project-architecture.md`
- `engineering/controls-and-tokens.md`
- `engineering/animation-and-timing.md`
- 任务触发的素材 / 3D / Effects 模块
- `quality/validation.md`

## C｜JSX_BUILD
加载：
- `workflows/jsx-generation.md`
- 相关工程模块
- `engineering/expressions-and-compatibility.md`

## D｜DIRECT_MCP
加载：
- `workflows/mcp-direct-control.md`
- 若是修改现有工程，再加 `modify-existing.md`

## E｜HYBRID
加载：
- `workflows/hybrid.md`
- `mcp-direct-control.md`
- `jsx-generation.md`
- 只加载本任务涉及的工程模块

## F｜COMPLEX_3D
加载：
- `capabilities/3d-camera-models.md`
- `engineering/assets-and-replacement.md`
- `engineering/project-architecture.md`
- `engineering/animation-and-timing.md`
- `quality/visual-quality.md`
- 若从零做，再加 `new-project.md`

## G｜MOGRT_TEMPLATE
加载：
- `capabilities/mogrt-essential-properties.md`
- `engineering/controls-and-tokens.md`
- `engineering/project-architecture.md`
- `engineering/assets-and-replacement.md`

## H｜REVIEW_DEBUG
加载：
- `workflows/review-debug.md`
- `quality/validation.md`
- `quality/render-policy.md`
- 再按错误类型加载 1 个对应模块

## 风险等级

LOW：
局部参数 / 单层 / 单模块修改
→ 不需要方案门禁

MEDIUM：
多个模块、结构调整、明显动画设计
→ 先简短计划

HIGH：
新视觉方向、完整场景、复杂 3D、插件依赖、素材路线改变、大面积重构
→ 必要时先方案 / 静帧 / 用户确认

## Grill-me

只有以下情况触发：
- 多条明显不同的创意路线；
- 关键条件缺失；
- 技术路线成本差异很大；
- 错一次会导致大面积返工。

最多优先问 1–5 个关键问题。
