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

## Native / Editable
创建或重构结构时检查：
- 是否用基础 Shape / Transform 手工模拟了已有更合适的 Native Feature；
- 是否因为“方便脚本化”而放弃 Layer Style / Text Animator / Mask / Matte / Repeater / Adjustment Layer / Parent 等更合适机制；
- 是否存在可以 Parent / Attach / Follow / Target 却分别打关键帧的对象；
- 可动态引用的位置 / 尺寸是否被无意义烘焙成固定值；
- 用户移动目标、修改文字、调整布局后，相关结构是否仍成立。

简单 Patch 不要求为了这项检查重构用户原有合理结构。

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
- Relationship Rig 不依赖隐藏的手工同步

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

静帧只能检查 staging / 状态 / 构图；Timing、Spacing、Weight、Continuity、Relationship 稳定性仍需在 AE 前台连续预览或实际修改目标做验证。


## Undo Finalization Validation｜撤销封存验收

仅大型 BUILD / NEW_PROJECT / ASSET_REFACTOR / 结构重构需要检查；普通小 PATCH 不做自动 Purge。

执行 `app.purge(PurgeTarget.UNDO_CACHES)` 前必须逐项确认：
- 修改前恢复点真实存在，并且对应本轮修改前状态；
- 当前工作 Project 仍是正式工程，不是备份副本；
- BUILD 没有未处理异常或部分写入状态；
- 目标结构、关键表达式、素材依赖、控制器和必要视觉检查已经通过；
- 当前正式 AEP 已成功保存；
- 清 Undo 前再次确认恢复点仍可用；
- 用户没有要求保留 Undo；
- 当前不是实验 / Previs / 尚待确认的版本。

任何一项无法确认：
**停止 Purge。**
可以保留当前已完成工程，但必须明确报告“未执行撤销封存”以及具体原因。

Purge 后验证：
- 不再声称 Purge 前操作仍可通过 Ctrl+Z 恢复；
- 后续 PATCH 应重新正常积累新的 Undo 历史；
- 大回滚路径指向已验证的 AEP 恢复点，而不是 Undo History。
