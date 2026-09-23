# Workflow｜Previs First

> 用于“最终高级感主要取决于构图、Pose、Timing、Camera、Typography”的 Motion-sensitive 镜头。目的不是多做一步，而是在材质和工程复杂度变高前，用最低成本验证设计。

核心：

**If the motion is ugly in greybox, polish will not save it.**

## 1｜何时触发

优先触发：
- HUMAN_MOTION_AI_ASSIST；
- HUMAN_DESIGN_AI_ENGINEER 且主难点是 Motion taste；
- Hero 产品 / 品牌包装 / 高级 Typography；
- Camera choreography；
- 用户反馈“太 PPT / 太机械 / 衔接难看”；
- 从零镜头但没有成熟可直接复用 Motion；
- 用户已提供简单 AE 预演 / 灰盒动画，希望 AI 完整化。

通常不触发：
- 改字 / 改色 / 素材替换；
- 明确模板内的机械扩展；
- 架构图 / 数据图 / 信息 UI 且时序规则已经锁定；
- Authority 0 的纯执行。

## 2｜Previs 只验证这些

- 主体位置 / 占比；
- 第一、第二视觉中心；
- 关键 Pose；
- 主运动路径；
- Camera 起止与转向；
- Primary / Secondary / Rest Beat；
- 入场、Hold、出场时长；
- 转场承接；
- 信息阅读顺序；
- 大体 Stagger / overlap。

此阶段故意弱化：
- 复杂材质；
- 插件效果；
- 最终调色；
- 细颗粒 / glow / glass；
- 高成本 3D 细节；
- 无关装饰。

## 3｜灰盒实现

优先使用：
- Text；
- Shape；
- Null；
- Placeholder；
- 简单 Parent；
- 少量真实关键帧；
- 简单 Camera / 2.5D（只有空间关系本身需要验证时）。

不要因为最终要用复杂插件，就在 Previs 阶段先搭插件链。

## 4｜关键帧原则

Previs 不是“粗糙 = 线性关键帧”。

主 Motion 仍应：
- 有清晰 Timing / Spacing；
- 有正确 Ease / Hold；
- Camera 连续；
- 不无脑 bounce；
- 能代表最终节奏。

允许后续 Polish 改曲线，但 Previs 必须先证明动作逻辑成立。

## 5｜Previs Gate

进入正式 BUILD 前至少回答：

- 构图是否成立？
- 主体在关键 Pose 是否清楚？
- Primary Beat 是否唯一明确？
- 是否像 PPT 一步一步切换？
- Camera 是否与主体争动作？
- Hold 是否够读？
- 转场是否有视觉承接？
- 是否存在本应 Relationship / Parent 的假同步？
- 是否需要用户手调主 Graph 后再继续？

若用户已经手动修改 Previs：
**以用户修改后的 Previs 为 Motion Truth，不重新发明。**

## 6｜从 Previs 到正式工程

确认后：
1. 锁定关键 Pose / Timing；
2. 生成 `AE BUILD SPEC`；
3. 工程化 Parent / CTRL / Relationship；
4. 替换 Placeholder 为真实素材；
5. 加 Material / Effects / Plugin；
6. 加 Secondary Motion；
7. 视觉反馈与人工 Polish。

不要为了工程化而改变已经批准的主要 Motion。

## 7｜Human Polish Point

以下内容可以刻意留给人：
- Hero Graph；
- 关键 Path；
- Typography 最终 spacing；
- Camera 的最后 5–10% 手感；
- 音乐卡点的 1–3 帧微调。

AI 的目标是让这些手调发生在清晰、干净、可编辑的工程上，而不是消灭人工 Motion Design。
