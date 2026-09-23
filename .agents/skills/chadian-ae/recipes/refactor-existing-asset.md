# Recipe｜Refactor Existing AE Asset

适用：用户自己手搓的老 AEP、镜头工程、小元素动画、半模板资产。典型特征是图层命名混乱、插件较多、表达式链复杂、硬编码参数散落、人工能改但成本高，AI 每次也需要重新扫描大量上下文。

目标不是重做视觉，而是把现有资产整理成：

**Human-editable + AI-readable + Low-token patchable + Long-term reusable**

## 触发条件

用户表达以下意图时使用本 Recipe：
- “整理这个老工程 / 模板 / 小动画”
- “让人和 AI 都方便修改”
- “把控制参数集中起来”
- “以后 AI 能快速改，不要每次读整个工程”
- “批量把以前做的资产标准化”
- “把乱的工程整理成可复用组件”

这是结构重构任务，默认使用完整版 Skill；但不要因此读取无关 Motion / 3D / 素材模块。

## 最小加载

必须：
- `00_core-invariants.md`
- `02_task-router.md`
- `engineering/project-architecture.md`
- `engineering/controls-and-tokens.md`
- `engineering/expressions-and-compatibility.md`
- `workflows/modify-existing.md`
- 本 Recipe

按需：
- 有复杂共享动画才加载 Motion。
- 有插件问题才加载插件相关 Capability。
- 有素材替换结构才加载 `assets-and-replacement.md`。
- 不因为工程“大”就默认全量读取。

## 执行顺序

### 1. Recovery Point
现有工程第一次写入前先建立可靠恢复点。高风险重构没有恢复点不直接破坏性写入。

### 2. Scope First
优先确定“当前目标合成 / 资产根合成”。

只分析：
`目标合成 → 直接依赖预合成 → 必要控制器 / 表达式引用 / 插件依赖`

不要默认扫描整个 Project。

如果用户要求全工程批量整理，仍按“一个资产根合成一个批次”处理，不把几十个资产一次性混成一个大型重构。

### 3. Preserve Result
默认保持：
- 最终视觉
- 动画节奏
- 时间点
- 素材内容
- 用户人工微调
- 必要第三方插件效果

除非用户明确要求，不借整理之名重新设计。

### 4. Structure Cleanup
在确认依赖后：
- 清理明确无用 / 重复 / 废弃对象；
- 统一合成、图层、控制器命名；
- 按 BG / SOURCE / GRAPHIC / TEXT / FX / CTRL / OUTPUT 等角色整理；
- 减少无意义多层嵌套，但不为了“扁平”破坏合理预合成；
- 不依赖 Layer Index 定位关键对象；
- 关键对象优先保留稳定名称 / AI_ID / ROLE。

### 5. Build a Control Surface
优先建立单一、明显的控制入口，例如 `00_CTRL`。

只暴露真正高频修改项：
- 文案
- 主色 / 辅色
- 尺寸
- 全局位置 / Offset
- 动画速度 / Duration
- Delay / Stagger
- 强度
- 开关
- 可替换素材入口

不要为了“参数化”把几十个低价值参数全部暴露。

### 6. Replace Hidden Hardcoding
将散落且未来高概率修改的硬编码，优先改为：

`命名控件 → 短表达式引用 → 目标属性`

要求：
- 表达式短、局部、可读；
- 不制造长链式跨多合成引用；
- 同一事实尽量只有一个 Source of Truth；
- 能用 Parent / Controller / Essential Property / Marker 表达关系时，不复制多份数值；
- 保留人工 Graph Editor / Path / Keyframe override 能力。

### 7. Plugin Policy
第三方插件默认允许保留，不强制原生化。

但必须：
- 记录插件名与关键用途；
- 区分“核心依赖”与“可替代增强”；
- 不把插件内部几十个参数全部复制到控制层；
- 只映射真正需要长期调整的少量参数；
- 未检测到插件时，不声称存在。

### 8. AI Fast Path
整理后的资产应允许未来 AI 优先按以下路径读取：

`资产说明 / 00_CTRL → 目标图层 → 必要依赖`

只有修改失败、依赖不明或用户要求深度重构时，才扩大扫描范围。

目标是让普通参数修改无需重新理解整个工程。

若该资产会长期复用，整理完成后按 `../references/engineering/project-context-map.md` 生成极轻量 Context Map，记录 Root Comp、主要 ID、控制器、素材槽、依赖、Fast Patch 与 Manual Zones。Context Map 只是索引；以后与真实 AE 状态冲突时，以 AE 状态为准并更新 Map。

## Undo / Patch Safety

- 不用一个巨大 JSX UndoGroup 覆盖整个重构。
- 按“结构 / 控制器 / 表达式 / 清理”拆成少量逻辑步骤。
- 整理完成后的日常 Patch，只改对应控制项或目标图层。
- 禁止为了改一个参数重新运行完整 Refactor 脚本。
- 所有 UndoGroup 可靠闭合。

## 完成验收

确认：
- 视觉和动画结果没有意外改变；
- 无 Expression Error；
- 无新增 Missing Footage；
- 插件依赖清晰；
- 人类能在很短时间内找到主要控制入口；
- AI 后续不必默认扫描整工程；
- 主要高频参数已集中，但没有过度参数化；
- 人工关键帧 / 曲线 / 路径仍可继续编辑。

## 最终回报

保持短，只输出：
1. 整理范围；
2. 主要控制入口；
3. 第三方插件 / 外部依赖；
4. 以后 AI 修改时的最短读取路径；
5. 仍建议人工处理的特殊点（如有）。

不要输出长篇逐层日志。

## 批量资产模式

当用户要求整理多个历史资产：
- 一次处理一个资产根合成 / 一个 AEP；
- 每个资产独立恢复点；
- 使用同一套命名与控制规则；
- 每个资产生成极短的 Asset Map / Project Context Map；
- 完成一个再进入下一个，避免上下文和 Undo 混杂；
- 已整理资产后续优先直接 Patch，不再次完整 Refactor。
