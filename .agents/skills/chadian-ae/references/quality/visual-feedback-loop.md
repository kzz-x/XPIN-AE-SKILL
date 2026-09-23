# Quality｜Visual Feedback Loop

> 用于明显视觉变化、新镜头、复杂 Motion、Camera、布局或用户反馈“看起来不对”的任务。目标不是逐帧让 AI 看视频，而是用少量高信息证据进行 See → Measure → Correct。

核心：

**Tool success ≠ visual success.**
**See → Measure → Correct → Re-check.**

## 1｜先定义要看什么

不要无目的截图。

优先检查：
- Composition / staging；
- 关键 Pose；
- 主次视觉中心；
- 字体 / bounds / clipping；
- Camera framing；
- 转场前后连续性；
- Motion peak / settle；
- Material 是否在正确位置和层级。

连续 Motion 的最终“手感”仍以 AE 前台预览 + 人工判断为准。

## 2｜Representative Times

新镜头通常选 3–6 个时间点：
- before action；
- anticipation / start；
- primary peak；
- transition；
- settle / hold；
- final。

若已有 Marker，优先 Marker 附近取样。

不要固定平均分布；关键帧验收应该按语义 Beat 取样。

## 3｜Engine Room 优先 Contact Sheet

当前为 Engine Room 时，优先单次：
`screenshot_frame({times:[...]})`

而不是多次逐帧调用。

视觉证据只在以下情况增加：
- 某一个关键 Pose 有问题；
- 材质细节必须单独看；
- 复杂 precomp 需要隔离诊断。

## 4｜Measure

截图发现“看起来不对”后，不要靠截图猜数值。

回到工程数据读取：
- bounds；
- position / scale / anchor；
- font / leading / tracking；
- keyframe time/value/ease；
- parent / source；
- camera / target；
- effects / expression；
- marker / in-out。

**Image identifies symptom; AE state identifies cause.**

## 5｜Correct

修正遵循 Patch First：
- 只改导致问题的结构 / property；
- 不因一个元素偏了就重跑整个 BUILD；
- 不用“重新生成一套”掩盖定位失败；
- 用户已经人工修好的部分视为新的 truth。

## 6｜Re-check Budget

一次修正后：
- property / diff 先验证；
- 视觉变化明显才补一张 Contact Sheet；
- 不形成“每改一次参数截图一次”的死循环。

通常一个复杂阶段最多：
1. 首次关键 Pose 检查；
2. 一轮主要修正后的复核；
3. 必要时最终交付检查。

更多视觉迭代应由真实问题驱动。

## 7｜Motion 不能靠静帧完全判断

以下问题必须提示用户在 AE 前台连续预览：
- easing 手感；
- 1–3 帧级 timing；
- stagger rhythm；
- camera velocity continuity；
- overshoot / settle；
- 音乐 / 口播同步；
- motion blur。

AI 可读取关键帧 / Graph 数据辅助判断，但不能把 Contact Sheet 当连续动画本身。

## 8｜视觉问题分类

发现问题后先归类：

- COMPOSITION → 布局 / framing / safe area
- MOTION → timing / spacing / graph / stagger
- RELATIONSHIP → parent / target / connector / dynamic bounds
- MATERIAL → effects / blend / plugin / matte
- TYPOGRAPHY → font / scale / leading / tracking / animator
- ASSET → source / crop / replace slot
- CAMERA → rig / target / focus / path
- ENGINEERING → duplicate / wrong layer / expression / missing dependency

分类后只加载相应模块，不重新读取整个 Skill。

## 9｜通过标准

视觉反馈闭环完成不代表“AI 宣布好看”，而是：
- 关键 Pose 没有明显布局 / 层级错误；
- 工程数据与视觉目标一致；
- 发现的问题已定位到具体原因；
- 自动可判断的问题已经修正；
- 需要 Motion taste 的部分明确留给人工连续预览 / Polish。
