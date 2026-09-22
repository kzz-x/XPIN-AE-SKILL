# Recipe｜Learn From Existing AE Project

适用：用户说“学习这个 AE 工程 / 学一下这个镜头 / 把这个工程里的动画和审美学下来 / 提炼这个 AEP 的构图、曲线、材质、动效规律”等。

目标不是修改工程，而是把一个优秀 AEP 中可复用的经验提炼成：

**Composition Grammar + Motion Grammar + Material Recipe + Visual Grammar + 必要的 Engineering Pattern**

并打包为候选 Learning Pack。只有用户明确确认后，才允许把其中选定内容写入 AE Skill 的 learned library。

---

## 1｜强制路由与边界

- 这是 **AE_PROJECT_LEARN**，直接使用完整版 `chadian-ae`。
- 默认只读，不修改源 AEP。
- 先锁定用户指定的资产根合成 / 镜头；只沿直接依赖、关键控制器、关键预合成、关键表达式和必要插件读取，不默认全工程扫描。
- 学习 ≠ 自动写入 Skill。用户只说“学习这个工程”时，只允许分析、提炼、打包候选结果。
- 在任何 canonical Skill / reference / recipe 被写入前，必须先进入 **Promotion Gate** 询问用户。

---

## 2｜最小加载

必须：
- `00_core-invariants.md`
- `01_capability-map.md`
- `02_task-router.md`
- 本 Recipe

按需：
- 动画是重点 → `motion/motion-principles.md` + `motion/motion-profiles.md`
- 共享控制 / 复杂 Motion → `motion/motion-control-architecture.md`
- 需要判断曲线质量 → `quality/animation-qa.md`
- 材质 / Effects / 插件是重点 → 对应 Effects / Plugin capability
- 3D / Camera 是重点 → 对应 3D capability
- 不因为“学习工程”就把全部模块和整个 Project 都读进来。

---

## 3｜Evidence First：先采样再总结

先建立最小证据集，不要凭最终印象写“高级、自然”。

### A. 视觉证据
优先导出 / 截取 2–6 张代表性静帧：
- 初始 / 稳定构图
- 主动作峰值
- 关键转场
- 最终落点
- 如材质有明显状态变化，再补必要帧

静帧用于判断构图、视觉层级、色彩、材质与信息密度。

### B. 工程证据
读取与当前根合成直接相关的：
- Comp 尺寸 / fps / duration
- Guide / 安全区（可读时）
- Layer / Precomp 层级
- Parent / Null / Marker / Controller
- Font / size / leading / tracking / alignment
- Shape / Mask / Matte
- Effects / Layer Styles / Blend Mode
- 关键表达式与动态关系
- 插件依赖

### C. Motion 证据
对于真正承担运动的属性，读取：
- Keyframe time
- Keyframe value
- temporal interpolation
- temporal ease / influence / speed
- spatial interpolation / tangent（相关时）
- duration
- delay / stagger
- overshoot
- settle
- hold
- Marker 与语义节点关系

不要只记录“用了 Easy Ease”。

---

## 4｜把绝对参数抽象成可复用规律

不要把一个 AEP 的坐标和秒数直接当风格规则。

优先同时记录：

**Observed**
- 当前工程真实参数。

**Normalized**
- X / Y → 相对画幅比例
- 元素宽高 → 相对画面或安全区比例
- Keyframe time → 帧数 + 动作总时长百分比
- 位移 / Overshoot → 相对对象尺寸或总位移百分比
- Font size → 相对画面高度 / 手机端可读层级
- 间距 → 相对字号 / 元素尺寸比例

**Interpretation**
- 这条规律为什么成立；
- 适合什么对象 / 场景；
- 什么情况下不要套用。

任何“为什么”无法从证据合理支持时，标记为 `INFERRED`，不要伪装成工程事实。

---

## 5｜候选 Learning Pack

每次学习完成后输出一个候选包，至少包含以下四张卡；没有价值的卡可以省略，不为了凑齐硬写。

### COMPOSITION_CARD
提炼：
- 构图骨架
- 主体占比
- 安全区
- 留白
- 对齐
- 字号 / 信息层级
- 第一 / 第二视觉中心
- 手机端可读性
- 可复用范围
- 不适用情况

### MOTION_CARD
提炼：
- 对象 Profile
- Timing / Spacing
- 曲线特征
- Anticipation
- Inertia / Weight
- Overshoot
- Follow Through
- Stagger / Overlap
- Hold / Settle
- Marker / 口播关系
- 真实关键帧样本与归一化版本
- 哪些主关键帧应保留给 Graph Editor

### MATERIAL_CARD
提炼：
- 材质的真实 AE 结构
- Shape / Mask / Matte / Effect / Layer Style / Adjustment / Plugin
- 哪些是核心依赖
- 哪些参数允许暴露
- 哪些参数不应重建
- 是否应直接复用模板 / 预合成，而不是凭描述重做

### VISUAL_CARD
提炼：
- 背景 / 主色 / 功能色
- 字体 / 字重
- 图形语言
- 线宽 / 圆角
- 阴影 / 描边
- Halftone / Grain / Grid 等纹理
- 信息密度
- 主次
- “像什么”与“明确不要像什么”

可选：
### ENGINEERING_CARD
只有当工程结构本身值得复用时才提炼：
- Parent / Rig
- Controller
- Expression
- Precomp
- Time Remap
- Asset Slot
- Plugin dependency
- AI Fast Path

---

## 6｜Good / Bad Boundary

如果用户同时指出“哪些地方是我喜欢的 / 哪些地方别学”，必须分开记录。

Learning Pack 可包含：
- `KEEP`：值得复用
- `AVOID`：明确反模式
- `ONE_OFF`：只适合这个镜头，不应推广

不要把一个优秀工程里的所有细节都升级成全局规则。

---

## 7｜Promotion Gate：写入 AE Skill 前必须问

候选包完成后，先给用户一个很短的晋升摘要，至少说明：

1. 学到了哪些候选规律；
2. 每条的证据来自哪里；
3. 建议作用域：
   - `generic`：通用 AE 规则
   - `channel`：频道 / 品牌视觉系统
   - `project`：只限某项目
   - `asset`：只限某个模板 / 材质 / 动画组件
4. 建议写入哪一类：
   - composition
   - motion
   - material
   - visual
   - engineering
5. 是否与现有 Skill 规则重复或冲突；
6. 建议“新增 / 并存 / 合并 / 覆盖”的方式。

然后明确询问用户：
**“哪些要加入？按什么作用域加入？与旧规则并存、合并还是覆盖？”**

未得到明确回答：
- 不修改 `references/learned/`
- 不修改现有 Motion / Engineering / Capability 规则
- 不自动提交到 Skill

“学习一下 / 可以 / 看着办”不等于授权写入 Skill。

---

## 8｜用户确认后的写入位置

批准后的长期知识优先写入：

- `references/learned/composition/<slug>.md`
- `references/learned/motion/<slug>.md`
- `references/learned/material/<slug>.md`
- `references/learned/visual/<slug>.md`
- `references/learned/engineering/<slug>.md`

并更新：
`references/learned/index.md`

原则：
- 一个卡只承担一个清楚主题；
- 文件名稳定、可检索；
- Index 只保存短摘要、tags、scope、source、path、trigger；
- 原始大量参数不要塞进 Index；
- 同一规律已有条目时优先 Patch，不制造近义重复卡；
- 只有真正通用、经过多个案例验证的规则，才考虑提升到核心 Motion / Engineering reference；
- 单个案例默认先留在 learned library，不直接污染核心规则。

---

## 9｜渐进式调用 Learned Library

后续 AE 任务只有在以下情况才读取 `references/learned/index.md`：
- 用户明确说“用之前学的 / 用频道风格 / 用这个工程学到的规律”；
- 用户点名某个 learned style / motion / material；
- 当前 Handoff 明确携带 learned tag；
- 当前任务与某个已批准的频道 / 项目 / 资产规则直接相关。

读取 Index 后：
- 只加载最相关的 1–3 张卡；
- 不默认读取全部 learned 文件；
- 不因为 library 变大而让每个简单 Patch 都增加上下文。

---

## 10｜完成回报

学习阶段只需告诉用户：
- 学习范围
- 代表性证据
- 候选卡摘要
- 哪些是事实 / 哪些是推断
- Promotion Gate 问题

批准写入后再告诉用户：
- 实际写入的 learned 文件
- Index 新增的 tag / trigger
- 是否修改了现有核心规则
- 未来如何触发调用

最终目标：

**不是让 Agent“看过一个 AEP 就自称学会审美”，而是把优秀工程变成有证据、可检索、可选择晋升、可渐进调用的长期 AE 知识。**
