# Workflow｜Creative Authority & AI Suitability

用于 AE 设计 / 制作方案阶段先判断：**这个镜头 AI 应该做到什么程度，而不是默认让 AI 从零包办。**

核心原则：

**AI 最适合执行可规则化、可验证、可复用的 AE 工作；越依赖构图直觉、节奏判断和高级 Motion taste，越应由人主导。**

---

## 1｜先选 Production Mode

### A. AI_DIRECT_BUILD
AI 可以从结构设计到 AE 搭建承担主要工作。

适合：
- 架构图 / 系统图 / 流程图 / 时间线；
- 数据图表 / 排名 / 参数对比 / HUD / 信息场；
- 大量文字、标签、节点、连线、窗口、规则卡片；
- 规则明确的 UI / 图标 / 路径 / 扫描 / 计数；
- 高重复、强约束、结果容易验证的镜头。

要求：
- 先把信息层级、布局规则、时序、视觉规范写清；
- 仍需保留人工最终 Polish；
- 若构图 / Motion taste 开始成为主难点，立刻降到 HUMAN_DESIGN_AI_ENGINEER。

### B. HUMAN_DESIGN_AI_ENGINEER
默认优先模式。

人先确定构图、关键 Pose、Motion Grammar、模板或参考；AI 负责把它工程化、扩展和批量完成。

特别适合：
- 已有一套漂亮模板，换内容 / 换风格 / 扩展到新镜头；
- 已有一个正确卡片 / 标题 / Motion Pattern，让 AI 批量复制到其余元素；
- 将模板 B 的内容结构迁移到模板 A 的材质 / 插件 / 视觉系统；
- 表达式绑定、控制层设计、Parent / Null / Relationship、响应式布局；
- 批量命名、Precomp、素材替换、参数集中、Marker、错帧、Stagger；
- 历史工程整理、AI-friendly 模板化；
- 从已批准 AEP / 模板中复用真实 Motion / Material / Effect Chain。

AI 不应重新发明已有人类设计结论。

### C. HUMAN_MOTION_AI_ASSIST
人主导构图和主 Motion，AI 只处理工程辅助。

典型：
- Hero 产品镜头；
- 品牌包装 / 高级 Typography；
- 高要求 Camera choreography；
- 音乐节奏驱动的精细动作；
- 创意转场、抽象视觉隐喻；
- 类生物动作、复杂机械 / 液体 / 布料 / 碰撞；
- “看起来很简单，但高级感主要靠 timing / spacing / composition”的镜头。

AI 可做：
- 工程整理；
- 表达式 / 控制器；
- 批量复制 / 替换；
- 技术实现；
- 检查 / QA；
- 依据已确定 Motion 做扩展。

---

## 2｜Creative Authority

Production Mode 决定生产关系；Creative Authority 决定 AI 有多少创作权限。

### Authority 0｜EXECUTE_ONLY
用户已经决定视觉和 Motion。

AI 只：
- 改参数；
- 复制 / 替换；
- 绑定；
- 整理；
- 工程化；
- QA。

禁止擅自重新设计构图、节奏、材质或 Motion Grammar。

### Authority 1｜EXTEND_EXISTING_SYSTEM
默认优先。

AI 可以学习 / 读取已有：
- 模板；
- AEP；
- 关键帧；
- Graph；
- 材质；
- 插件链；
- 布局系统；
- Motion Pattern；

然后扩展到新内容。

原则：**Learn and extend, do not reinvent.**

### Authority 2｜STRUCTURED_GENERATION
信息结构、设计规则、时序和实现逻辑都足够明确时，AI 可以完整生成。

常见：架构图、数据图、流程图、规则化 UI / HUD、批量模块。

### Authority 3｜CREATIVE_MOTION
AI 自主决定构图、镜头、动画节奏、视觉语言。

默认关闭。

只有用户明确授权“让 AI 自己试 / 自由设计”时启用；仍应先 Reference First，并把结果视为候选方案，不把首次生成当最终设计。

---

## 3｜AI Suitability 判断

不要用“画面简单 / 复杂”判断。

优先判断：

**AI Suitability ≈ 工程确定性 × 规则重复性 ÷ 审美自由度**

### 越适合 AI
- 规则能写清；
- 多个元素共享结构；
- 结果能客观验证；
- 有模板 / 参考 / 已批准 Motion；
- 可以参数化；
- 重复操作多；
- 修改目标明确。

### 越不适合 AI 从零设计
- 构图要靠高级视觉判断；
- timing 需要大量“早 2 帧 / 慢一点 / 再压一下”的人工感觉；
- 连续镜头运动决定高级感；
- 品牌 taste / 留白 / typography 是核心；
- 真实物理 / 生物运动是重点；
- 用户只给“高级一点 / 丝滑一点 / 有设计感”而无 Motion Grammar。

一个 100 节点架构图可能比一个 Logo 滑入更适合 AI；复杂度不是关键，**决策自由度**才是。

---

## 4｜高收益 AI 辅助任务

优先把这些琐碎但规则明确的工作交给 AI：

- Expression：wiggle、loop、距离驱动、路径跟随、自动连线、响应式尺寸、计数、共享 offset；
- 控制层：CTRL_GLOBAL / CTRL_LAYOUT / CTRL_MOTION / CTRL_STYLE / CTRL_MATERIAL；
- 3+ 元素共享动画的 Parent / Null / Master Progress / Stagger；
- 插件 / Effect 参数集中和已有材质系统迁移；
- 大量图层批量命名、整理、Precomp、Role / AI_ID；
- 大量素材替换并保留 Scale / Crop / Matte / Motion；
- 自动布局、sourceRectAtTime、动态边界；
- Marker / Time Remap / Duration Scale；
- 一个优秀元素扩展成一组；
- 旧工程标准化、低 Token Fast Path；
- 模板视觉系统迁移；
- QA：Expression Error、重复层、丢素材、依赖、命名和控制入口。

---

## 5｜Template / Style Migration

当用户说：
- “把另一个模板改成这个液态玻璃风格”；
- “按这个工程的插件和材质做”；
- “把 B 的内容结构套到 A 的视觉系统”；

默认路由：

**HUMAN_DESIGN_AI_ENGINEER + Authority 1**

执行前先读取源模板真实实现，不凭空猜：
- Layer Style；
- Native Effect；
- Blend Mode；
- Adjustment Layer；
- Plugin 与关键参数；
- Precomp；
- Expression；
- CTRL；
- Material / Motion dependencies。

迁移时保留目标工程原有内容结构和正确 Motion，优先迁移“设计系统”，不是简单复制外观数值。

未检测到插件 / Effect 时，不得声称已复用。

---

## 6｜设计阶段输出 AE ROUTE

只要任务包含“设计 AE 镜头 / 出 AE 制作方案 / 决定 Codex 怎么做”，先给一个很短的路由：

```text
AE ROUTE
Production: HUMAN_DESIGN_AI_ENGINEER
Creative Authority: 1
AI Suitability: HIGH / MEDIUM / LOW
Reason: 核心难点是……
Human Locks: 构图 / 主节奏 / Hero Motion
AI Tasks: 表达式 / CTRL / 批量扩展 / 材质迁移 / 工程整理
```

不需要写成长报告。

如果路由为 HUMAN_MOTION_AI_ASSIST，应明确提醒用户：先锁定关键 Pose / Previs / Motion 参考，再进入 Codex 制作。

---

## 7｜禁止行为

- 不把每段文案都自动路由为 AI 从零做完整镜头；
- 不因为 Codex 会 JSX 就让 JSX 决定视觉；
- 不因技术实现成功就认为设计成立；
- 不让 AI 重做用户已经设计好的 Motion；
- 不把“更自动化”放在“更好看、好改”之前；
- 不用 Authority 3 作为默认模式。
