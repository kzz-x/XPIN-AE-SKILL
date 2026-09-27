# Workflow｜Visual Anchor Gate（视觉锚点门禁）

> 用于从零视觉设计、中高视觉复杂度镜头、口播转视觉、2.5D / 3D 信息场、材质 / 光影 / 构图依赖明显，以及用户反馈“AI 自己设计容易丑 / 太 PPT / 一眼 AI”的任务。

核心原则：

**先把画面定准，再让 Agent 执行。图不满意，先不做。**

**Visual Anchor 定义“画面应该长什么样”；Execution Prompt 只补充“怎么动、何时动、哪些不能改”。**

---

## 1｜什么时候必须先有 Visual Anchor

除非用户明确要求跳过，否则以下任务默认先取得并确认至少一个 Visual Anchor：

- 从零设计完整镜头；
- 构图、比例、空间层级、材质、灯光决定最终高级感；
- 2.5D / 3D / Camera / 多模块信息场；
- 把口播或文案转成具体视觉镜头；
- 需要 AI 承担较多视觉设计决策；
- 用户已反馈直接让 Agent 做容易跑偏、太 PPT、太模板或“一眼 AI”；
- 一次 BUILD 成本高，错误方向会造成明显返工。

普通改字、改色、改尺寸、小 Patch、已有明确模板扩展，不触发本 Gate。

---

## 2｜什么可以作为 Visual Anchor

Visual Anchor 不限定来源，按当前任务选择最低成本且最明确的方式：

1. 用户提供的准确参考图；
2. 当前 AE 工程截图 / 已批准关键帧；
3. 用户自己的 Blockout / 灰盒 / 草图；
4. GPT / 图像模型生成的完整关键帧设计图；
5. 已确认的上一版关键帧；
6. 上游 Handoff 中已锁定的视觉稿。

如果已有足够明确的 Anchor，不重复生成新图。

需要真实产品 / 品牌 / 设备准确性时，生成图不能替代真实产品参考；应与 Reference First 联用。

---

## 3｜标准流程

### Step A｜先判断 AI 可行性
先用 Creative Authority 判断：
- AI 能直接构建；
- 人锁视觉、AI 工程执行；
- 人锁主 Motion、AI 只辅助。

超出当前 Agent 稳定能力的设计先拆分、降级或保留人工主导，不用低质量效果冒充完成。

### Step B｜先做 / 选 Visual Anchor
视觉锚点应尽量把这些决策变成“看得见的事实”：
- 构图；
- 主体占比；
- 前后层级；
- 信息密度；
- 视觉中心；
- 配色；
- 材质；
- 光影；
- 关键文字关系；
- Camera framing。

### Step C｜Visual Anchor Gate
在进入正式 BUILD 前确认：

- 画面本身是否已经好看 / 成立？
- 主次关系是否清楚？
- 构图与比例是否正确？
- 风格是否符合当前项目？
- 用户是否接受这个方向？

若答案是否定：
**继续修改图片 / Blockout，不进入最终 AE BUILD。**

禁止“图还不满意，但先让 Codex 做出来再说”。

### Step D｜生成短 Execution Prompt（默认 P2 Directed Creative）
Visual Anchor 已确认后，不要把图片重新翻译成几千字。

执行提示词只补充图片无法表达的信息：
- 口播原文；
- 口播音频 / Marker / 节奏依据；
- 动画语义与阶段；
- 元素之间的 Interaction / Relationship；
- 哪些视觉属性 LOCKED；
- 实现优先级；
- DO NOT；
- 验收 / Deliverables。

原则：

**Visual Anchor = visual truth**
**Execution Prompt = motion + timing + behavior + constraints**

**视觉锁定 ≠ 实现写死。** Anchor 已经明确的内容不要再次用坐标、逐层步骤、逐帧参数复述；除非该实现本身就是已确认设计的一部分，否则让执行 Agent 自选合理方法。

### Step E｜先做 V1，再关键帧验收
默认先交付可编辑 V1：
- 起始 Pose；
- Primary Action / 中间 Pose；
- Settle / Final Pose；
- 必要时低质量 Preview。

不默认完整高质量渲染。

### Step F｜再进入 Engineering / Polish
只有视觉和主 Motion 已经成立后，再追加：
- Parent / Relationship；
- CTRL；
- Expression；
- Effect / Plugin；
- 命名；
- Precomp；
- 模板化；
- Secondary Motion；
- 最终 Polish。

不要把大量工程化成本花在一个尚未确认的视觉方向上。

---

## 4｜与 Previs First 的关系

Visual Anchor 与 Previs 不冲突：

- **Visual Anchor** 主要锁“长什么样”；
- **Previs** 主要锁“怎么动”。

典型中高复杂度流程：

**Reference → Visual Anchor → Previs（需要时）→ BUILD SPEC → BUILD → Visual Feedback → Polish**

若用户已有可靠 Motion Previs，但最终视觉尚未确定：
先保留其 Motion Truth，再补 Visual Anchor。

若用户已有成熟静帧 / 模板且 Motion 规则也明确：
可直接进入 BUILD，不机械增加 Previs。

---

## 5｜口播驱动镜头

用户要求“按口播 / 音频做动画”时：

1. Visual Anchor 先锁构图和视觉层级；
2. 音频 / Marker 决定 Timing；
3. 口播原文决定语义动作；
4. Execution Prompt 只写：
   - 哪一句强调哪个区域；
   - 哪一句触发哪个主动作；
   - 哪一句完成连接 / 转场 / 状态变化；
5. 不用长篇文字重新描述 Visual Anchor 中已经明确的画面。

Motion Timing 仍遵循 speech-driven-motion 规则，用户 Marker 优先。

---

## 6｜执行提示词压缩规则

Visual Anchor 已确认后：

### 应写
- “严格以附件关键帧为视觉目标”；
- 口播原文；
- 真实音频 / Marker；
- Motion Phases；
- 主动作；
- 关系逻辑；
- 优先级；
- 禁止擅自重设计；
- 关键帧 / Preview 验收。

### 不应重复写
- 已在图中清晰可见的每个物体位置；
- 大量坐标；
- 每个颜色值；
- 每块卡片的形状描述；
- 无必要的逐层施工细节；
- 为了显得“完整”而堆砌几十条软性形容词。

当实现方法没有唯一正确答案时，允许执行 Agent 自选 Native / Expression / Parent / Precomp / Camera 等方法，只锁定视觉与行为结果。

---

### Prompt Freedom Mode

Visual Anchor 通过后，默认使用 **P2 DIRECTED_CREATIVE**：锁视觉真值、主动作、节奏依据、硬约束与验收，不锁死无必要的实现细节。

只有以下情况升级为 **P3 EXECUTION_SPEC**：
- 用户已经明确指定关键帧 / Graph / Expression / Effect / Parent / CTRL 等实现；
- 正在修复已知偏差或 Bug；
- 需要精确复刻已经批准的 Motion；
- 模板化、批量迁移或工程一致性要求高于探索空间。

如果用户明确要“先让 Codex 自己试 / 看它能设计成什么样”，可在正式 BUILD 前用 **P1 CREATIVE_BRIEF** 做低成本候选 / Previs；候选通过后再进入 P2/P3。

## 7｜失败 / 降级规则

如果某个参考图效果无法被当前 AE / MCP / JSX 稳定复现：

1. 不得用明显低质量效果冒充完成；
2. 保留当前可编辑结构；
3. 明确指出无法可靠复现的部分；
4. 给出最接近且稳定的替代方式；
5. 不擅自改变已确认构图来迁就脚本方便。

**简单但正确 > 复杂但糊弄。**

---

## 8｜默认提醒

当命中本 Gate 时，主动提醒用户：

> 这个镜头建议先生成 / 确认参考图或关键帧图。图不满意，先不做；先把画面定准，再让 Agent 执行。

若用户已经提供足够明确的 Visual Anchor，不重复提醒生图，直接确认它将作为视觉真值。

---

## 9｜通过条件

进入正式 BUILD 前至少满足：

- 已有明确 Visual Anchor；
- 用户没有表达“这图还不对 / 先别做”；
- Anchor 中构图、主体比例和视觉中心可执行；
- 真实主体需要准确时已有真实参考或明确 Placeholder 策略；
- Motion 需要额外验证时已决定是否走 Previs；
- Execution Prompt 已压缩到 Motion / Timing / Behavior / Constraints / Verify。

一句话：

**No approved visual, no expensive build.**
