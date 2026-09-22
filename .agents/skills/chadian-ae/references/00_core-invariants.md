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

## 质量原则
- 不把 AE 理解为 Shape + Text 生成器。
- **Native Feature Before Manual Construction**：AE 已有语义上直接对应的原生能力时，不因 Shape / JSX 更好写而默认手工模拟。
- **Relationship Before Independent Keyframes**：多个对象存在 Parent / Follow / Attach / Carry / Target / Connect / Shared Motion 等关系时，优先编码关系，不靠多套关键帧人工同步。
- 能动态引用的目标位置 / 尺寸 / Bounds，不默认烘焙成当前固定数值。
- 复杂产品、车辆、建筑、机械、人物、设备等，优先真实素材 / 官方素材 / 3D / 高质量外部资产。
- 简单几何、UI、数据图表、路径动画可优先 AE 原生。
- 使用高级能力必须有收益，不为炫技堆 3D、Glow、粒子、Camera、Expression、插件。
- 视觉目标 > 实现方便。
- 事实正确 > 静帧参考中的错误。
- 用户最后确认的设计目标 > AI 自己临时改风格。
- **Creative Authority**：有现成模板 / AEP / 人工设计结论时，默认读取并扩展，不重新发明；AI 自主决定构图、Motion taste 与视觉语言只在用户明确授权时启用。
- **AI Assist Before AI Replace**：当镜头高级感主要依赖构图、Timing、Spacing、Camera 或 Typography 判断时，优先让人锁定设计，AI 做工程化、表达式、控制层、批量扩展和 QA。

## Reference First\n- 新视觉 / 新动画 / 复杂结构 / 真实性重要时，先判断是否需要图片、视频、成熟动效、真实运动或可复用资产参考。\n- 准确产品、人物、复杂机械、类生物运动、物理现象不要因为 JSX / Shape 更容易写就凭空发明。\n- 静帧主要约束视觉；明显 Motion 任务应优先有视频 / GIF / Lottie / 成熟动画参考。\n- 能直接复用官方素材、SVG、Lottie、Footage、3D 或模板时，先评估复用，不默认从零重建。\n- 关键参考缺失时优先使用可替换 Placeholder，并明确未核验部分；不得把占位或 AI 猜测说成真实结构。\n- 详细流程仅在 Router 命中时读取 `workflows/reference-first.md`，普通局部 Patch 不增加上下文成本。\n\n## AE26 / 脚本
- 兼容 AE26 中文版 / Windows。
- JSX / Expression 底层优先 `matchName`。
- 需要给人看的名称可中文。
- 对字体、插件、素材缺失必须容错。
- 未实际检测到的插件或能力，不得声称已存在。
- **Undo Safety**：大型 BUILD 不使用一个覆盖全工程的巨大 UndoGroup；按逻辑模块拆成少量可理解步骤。后续 PATCH 一次请求只做一个小型原子 Undo，禁止为改几个参数重跑完整 BUILD。
- 每个 UndoGroup 必须可靠闭合；优先 `try / finally`，避免异常导致后续人工 Undo 行为异常。

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
