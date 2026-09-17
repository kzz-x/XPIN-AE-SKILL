# Quality｜Validation

交付前按当前任务范围检查，不需要为小修改做全工程审计。

## Engineering
- 主合成 / 目标合成存在
- 命名清晰
- Precomp 合理
- 无重复垃圾层
- Expression 无错误
- 外部素材可用
- AI_ID / 稳定目标可继续定位

## Visual
- 构图正确
- 文字 / 数据 / Logo 事实正确
- 字体 / 色彩 / 描边 / 圆角有层级
- 素材风格统一
- 没有为了自动化而低质量手绘

## Motion｜基础
M0–M1 只检查当前范围：
- 曲线与元素属性匹配
- 无明显线性生硬 / 突然刹停
- 无穿插
- 多元素节奏有基本层次
- Camera 与对象关系合理

M2–M4 或用户明确要求深度动画验收时，追加读取：
`animation-qa.md`

不要把完整 Animation QA 强加给简单文字 / 颜色 / 单层关键帧 Patch。

## Controls
- 常改项可找到
- 不暴露过多无意义参数
- 素材可独立替换
- Marker / 时序逻辑清晰
- 复杂共享动画没有无脑复制成大量关键帧

## Compatibility
- AE26 中文版
- matchName
- font/plugin/asset fallback
- JSX undo/error handling

## Existing Project Patch
额外检查：
- 用户原关键帧是否还在
- Parent / Matte / Expression 是否误改
- 是否误建第二套控制器
- 是否改到了错误 AE 实例 / Comp

## Review Frames
视觉任务需要时选 4–6 个真正有代表性的时间点：
开场 / 运动中间态 / Hero / 最终 / 必要的转场状态。

静帧只能检查 staging / 状态 / 构图；Timing、Spacing、Weight、Continuity 仍需在 AE 前台连续预览或 Graph / Keyframe 数据中判断。
