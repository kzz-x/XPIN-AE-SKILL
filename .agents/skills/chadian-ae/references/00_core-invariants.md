# 00｜Core Invariants

这些规则始终生效，不需要在其他模块重复。

## 工程行为
- Read Before Write。
- Patch First。
- Preserve Manual Work。
- 修改范围保持最小。
- 不因“重新创建更容易”而重建用户已经存在的合成、控制器、素材槽或动画。
- 能识别真实状态时，不根据提示词猜结构。
- 重要对象优先使用 `AI_ID + 合成名 + 图层名 + ROLE/TYPE` 定位，不依赖 Layer Index。

## Recovery Point Gate
- 每个用户请求视为一次修改批次；不是每改一个数值都复制一次工程。
- 对现有 AEP 第一次写入前，先确认存在可恢复到“本轮修改前”的恢复点。
- 若当前工具能可靠创建备份：优先建立时间戳 / 递增版本备份，或等价的可恢复副本。
- 备份必须保留当前工作工程为工作工程；不要因为备份而切换 Active Project、改变用户原始项目路径或让后续修改落到备份副本上。
- 若当前工具无法确认备份已安全创建：明确提醒用户先备份；不要声称“已备份”。
- LOW 风险小 Patch：完成备份检查 / 提醒后可继续。
- MEDIUM / HIGH 风险操作（批量删除、结构重构、大型 JSX、控制器重建、多合成联动、大面积素材替换等）：必须有可靠恢复点后再做破坏性写入。
- 如果项目包含尚未落盘的人工修改，备份策略必须覆盖这些最新修改，不能只复制一个过期的磁盘版 AEP。
- AE Auto-Save 可作为额外保障，但只有能确认其恢复点足够新且包含本轮前状态时，才可视为本轮恢复点；不要盲目假设 Auto-Save 已可用。

## 模板化源工程隔离
- 用户要求“做成模板 / 模板化 / 沉淀模板库 / 拆独立组件 / 做成视觉系统包”时，源 AEP 与源素材默认只读。
- 模板化属于结构性高风险任务：先建立恢复点，再创建独立模板化工作副本；重命名、整理、删除、Relink、依赖收集、拆组件等只作用于副本。
- 禁止覆盖、移动或删除用户本地原 AEP / 原素材；禁止把备份副本或模板化副本冒充原工程。
- 工具不能可靠创建 / 验证副本时，不进行高风险模板化写入。
- 正式发布模板默认不可变；生产使用必须复制到工作区后修改。

## AI 自主依赖收集｜模板化硬规则
- 模板化任务默认不用 AE 原生“收集文件”；优先由 AI / MCP 读取当前模板真实引用，再通过文件系统复制到模板包。
- 收集分两层：先收工程真实引用，再补扫字体、插件、脚本、预设、LUT、Codec、3D / HDRI / 贴图、Adobe 版本等环境依赖。
- 所有依赖收集行为只允许**复制**；禁止移动、删除、覆盖用户原文件。
- 只收当前模板真实需要的最小必要集合；禁止为了保险复制整个 Program Files、整个插件库、整个字体库或整个素材库。
- 字体、`.aex`、CEP / UXP、第三方脚本等在复制前先判断许可、自包含性和伴随文件。
- Red Giant / Maxon、Sapphire 等安装器 / 授权体系依赖默认只记录，不打包。
- 不确定是否合法、是否必要、是否自包含时，宁可写入依赖清单，不进行高风险复制。
- 收集完成后必须做外部路径残留检查；能安全重定向到模板包内副本的才重定向，不能自包含的明确写入依赖说明。
- 模板化依赖收集只操作模板化工作副本与其新建模板目录，不改变源工程 / 源素材。

## 质量原则
- 不把 AE 理解为 Shape + Text 生成器。
- **Native Feature Before Manual Construction**：AE 已有语义上直接对应的原生能力时，不因 Shape / JSX 更好写而默认手工模拟。
- **Relationship Before Independent Keyframes**：多个对象存在 Parent / Follow / Attach / Carry / Target / Connect / Shared Motion 等关系时，优先编码关系，不靠多套关键帧人工同步。
- 能动态引用的目标位置 / 尺寸 / Bounds，不默认烘焙成当前固定数值。
- 复杂产品、车辆、建筑、机械、人物、设备等，优先真实素材 / 官方素材 / 3D / 高质量外部资产。
- 简单无语义几何、数据图表、自定义路径动画可优先 AE 原生；有明确语义的 Icon / Symbol / Logo / 标准 UI Asset 不因“简单”而跳过 Asset First。
- 使用高级能力必须有收益，不为炫技堆 3D、Glow、粒子、Camera、Expression、插件。
- 视觉目标 > 实现方便。
- 事实正确 > 静帧参考中的错误。
- 用户最后确认的设计目标 > AI 自己临时改风格。
- **Creative Authority**：有现成模板 / AEP / 人工设计结论时，默认读取并扩展，不重新发明；AI 自主决定构图、Motion taste 与视觉语言只在用户明确授权时启用。
- **AI Assist Before AI Replace**：当镜头高级感主要依赖构图、Timing、Spacing、Camera 或 Typography 判断时，优先让人锁定设计，AI 做工程化、表达式、控制层、批量扩展和 QA。

## Search First\n- Reference First 与 Asset First 是独立门禁，可单独或同时触发。\n- 新视觉 / 新动画 / 复杂结构 / 真实性重要时，先判断是否需要图片、视频、成熟动效或真实运动参考。\n- 有明确语义的 Icon / Logo / Animated Icon / Lottie / 3D Model / HDRI / Texture / Material / Template 等，必须先判断是否有现成资产；确认没有合适结果后才允许自制。\n- 准确产品、人物、复杂机械、类生物运动、物理现象不要因为 JSX / Shape 更容易写就凭空发明。\n- 静帧主要约束视觉；明显 Motion 任务应优先有视频 / GIF / Lottie / 成熟动画参考。\n- 关键参考 / 资产缺失时优先使用可替换 Placeholder，并明确未核验部分；不得把占位或 AI 猜测说成真实结构。\n- 详细流程仅在 Router 命中时读取 `workflows/reference-first.md` / `workflows/asset-first.md`，普通局部 Patch 不增加上下文成本。\n\n## AE26 / 脚本
- 兼容 AE26 中文版 / Windows。
- JSX / Expression 底层优先 `matchName`。
- 需要给人看的名称可中文。
- 对字体、插件、素材缺失必须容错。
- 未实际检测到的插件或能力，不得声称已存在。
- **Undo Safety**：大型 BUILD 不使用一个覆盖全工程的巨大 UndoGroup；默认在单次或少量执行中，脚本内部按约 3–6 个逻辑阶段建立独立 UndoGroup。禁止最外层再包一个覆盖全部 BUILD 的总 UndoGroup。后续 PATCH 一次请求只做一个小型原子 Undo，禁止为改几个参数重跑完整 BUILD。
- **Undo 分组 ≠ 多轮 MCP**：不要仅为了拆 Undo 而增加 MCP 往返、重复读取真实状态或拆成大量 Agent 回合；能在一个 JSX 内完成的逻辑分组就留在同一脚本内部。
- 每个 UndoGroup 必须可靠闭合；优先 `try / finally`，避免异常导致后续人工 Undo 行为异常。



## Undo Finalization Gate｜撤销安全封存

目标：大型 AI BUILD 完成后，把已经验收的结果变成新的工作起点，避免用户之后误按 Ctrl+Z 把整套 AI 构建撤回。

### 触发
默认考虑自动封存：
- 大型 BUILD / NEW_PROJECT；
- ASSET_REFACTOR / 结构重构；
- 大型 JSX / 多模块创建；
- 用户明确要求“做完就封存 / 清掉旧撤销”。

默认不封存：
- LOW 风险小 PATCH；
- 单层 / 少量属性修改；
- 试验性修改、Previs、尚未批准的视觉方案；
- 用户明确要求保留 Undo。

### Purge 前四项硬条件
只有以下全部成立，才允许执行 `app.purge(PurgeTarget.UNDO_CACHES)`：
1. 修改前恢复点已真实创建并验证可用；
2. 本轮 BUILD 已完成必要的结构 / 表达式 / 素材 / 控制器 / 视觉验证，没有未处理错误；
3. 当前正式工作 AEP 已成功保存，且没有把备份副本切成 Active Project；
4. 清 Undo 前再次确认修改前恢复点仍存在。

任一条件失败 → 不清 Undo；明确说明“已保存但未封存”的原因。

### 顺序固定
`Backup First → Build → Verify → Save → Re-check Backup → Purge Undo`

禁止：
- 先 Purge 再保存；
- 仅因为调用过 `app.project.save()` 就假设保存成功；
- 把 AE Auto-Save 当成默认可靠恢复点；
- BUILD 中途报错后仍 Purge；
- 小 PATCH 完成后自动 Purge；
- Purge 后声称仍能撤销 Purge 前的操作。

### 完成语义
成功封存后可以说明：
“当前大型 BUILD 已验证并保存，修改前恢复点仍存在，旧 Undo Cache 已清除；从现在开始的新 Ctrl+Z 只针对后续新操作。”

没有实际执行并验证 Purge 时，不得声称“已清除撤销记录”。


## 中文优先的人机界面

原则：**给人看的中文优先，给机器读的稳定标识不乱翻译。**

默认使用中文的用户可见内容：
- Project Folder / Comp / Precomp / Layer / Null / Camera / Light 名称；
- 控制层、Expression Control、自定义 Effect 实例名；
- Placeholder / 素材槽 / 模块名；
- Marker Comment、Layer Comment 中给用户看的说明；
- Essential Graphics / MOGRT 暴露名称；
- UndoGroup 名称；
- AE 制作方案、BUILD SPEC、执行提示、验收结果中用户需要直接阅读的字段。

命名格式优先级：
1. 能纯中文表达 → 直接中文，如 `动画控制`、`主体_手机`、`素材槽_驾驶室`。
2. 英文是行业术语或机器约定且保留有价值 → `中文｜English`，如 `动画控制｜CTRL_MOTION`、`入场｜IN`。
3. 英文名称不可由脚本重命名（插件参数、AE 固定 UI、API / matchName）→ 保持英文原值，并在相邻的中文层名、Comment、控制层或交付说明中补中文解释。
4. 品牌名、产品名、型号、文件扩展名、代码、路径、API、Expression / JSX 变量与枚举不强行翻译。

禁止：
- 新建 `Shape Layer 1 / Null 3 / Comp 17 / Main / Controller / Background` 等无中文解释的默认英文名；
- 新建一整套 `CTRL_GLOBAL / CTRL_MOTION / HERO / BG / HUD` 仅英文用户界面；
- Marker 只写 `IN / HOLD / OUT` 而没有中文；
- 为“看起来专业”而堆英文缩写。

兼容与保护：
- 普通 Existing Project Patch 不为满足命名规范而擅自批量重命名旧层，避免破坏 Expression、脚本、人工习惯；
- 新建对象从一开始按中文优先；
- 用户明确要求“整理 / 重构 / 标准化命名”时，可把旧英文名称迁移为中文优先，但必须先检查表达式、脚本、链接和插件依赖；
- `AI_ID / ROLE / TYPE` 等稳定机器字段可继续使用英文键和值；若用户会直接看到 Comment，应另加一行中文说明，不要改坏机器解析。

### 中文可读性验收
完成新建工程或结构性修改前，检查本次新建的用户可见对象：
- 是否存在无必要的纯英文图层 / 合成 / 控制器 / Marker；
- 必须保留的英文是否已有中文说明；
- 用户能否不理解内部代码，也能快速判断“这个层是什么、这个控件改什么、这个 Marker 表示什么”。


## 动画
- 明显运动必须有合理关键帧和曲线。
- 动作曲线应匹配元素材质、重量、功能。
- 禁止所有元素同一套 Ease。
- 复杂时间优先 Marker / Precomp / Time Remap / 控制器。
- Camera 与物体动画尽量分离。
- 目标不是“零关键帧”，而是减少重复关键帧；保留少量有意义、可在 Graph Editor 调整的 Master / Local Keyframes。
- **设计阶段决定实现法**：AE 制作方案不能只写“漂浮 / 弹性 / 高级”；主 Motion 应说明 Keyframe / Graph，持续程序化行为可说明 wiggle / loop / spring / distance-driven / Relationship，视觉材质应说明 Layer Style / Native Effect / Plugin / CTRL 的实现方向。详细格式仅在设计任务时加载 `engineering/ae-implementation-spec.md`。

## 素材
- 最终工程不得依赖实时 URL。
- 外部素材记录来源 / 许可证 / 用途。
- 缺素材时优先高质量占位槽，不用低质量乱画替代。
- 占位应保留最终比例、裁切、圆角、Matte、动画、调色关系。

## 渲染权限
- “做完 / 改好 / 给我看看 / 检查一下”不等于允许完整渲染。
- 默认只做关键帧静帧验收。
- 只有明确要求“渲染成片 / 导出视频 / 最终预览”才完整渲染。
- **模板化轻量预览例外**：当任务明确命中“做成模板 / 模板化 / 视觉系统包”时，允许自动生成用于模板检索和理解的短预览视频；这不等同于完整成片授权。预览必须短、低分辨率、低成本，只覆盖代表性片段；已有预览优先复用，高成本效果必要时退回静帧。具体限制见 `engineering/human-ai-template-library.md`。
