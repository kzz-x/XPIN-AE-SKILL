# Recipe｜Build Motion Scene

1. 需求 / 视觉目标
2. Capability Preflight
3. Relationship Scan
4. Native Capability Decision
5. 判断 Motion Complexity（M0–M4）
6. 素材路线
7. 工程层级
8. Design Tokens / Controls
9. 先成立 Hero Frame / 核心静态状态
10. 建立必要的 Relationship / Constraint Rig
11. 为主要对象选择 Motion Profile
12. 设计主动作结构：准备 → 加速 → 主运动 → 减速 → 超越/跟随 → 稳定（按需要删减）
13. 分配 Master / Parent / Precomp / Local
14. 制作 Primary Beat，再做 Secondary / Stagger / Follow Through
15. Camera / 后期（仅需要时）
16. Graph / Timing / Spacing 校正
17. Native / Relationship / Animation QA
18. 关键帧静帧检查 + AE 前台连续预览
19. 局部修正
20. 交付工程说明

不要先把所有动画做完再发现构图不成立。
先让 Hero Frame / 核心状态成立，再扩展时序。

不要先给每层复制关键帧，再“最后整理控制器”。共享运动和对象关系应在制作前确定。

## 制作前快速问

- 这是什么对象 / 效果？
- AE 是否已有更直接的 Native Feature？
- 谁跟随谁 / 携带谁 / 指向谁 / 连接谁？
- 哪些位置 / 尺寸应该动态引用，而不是写死？
- 哪些动画应该共享 Progress？
- 哪些参数以后用户最可能修改？
- 最后才决定 Local Keyframes。

## 按需读取

创建新视觉结构 / 重构：
- `../references/capabilities/native-ae.md`

出现 Attach / Follow / Target / Carry / Connector / Auto Layout / Dynamic Bounds：
- `../references/motion/relationship-rigs.md`

复杂 Motion：
- `../references/motion/motion-principles.md`
- `../references/motion/motion-profiles.md`
- `../references/motion/motion-control-architecture.md`
- `../references/quality/animation-qa.md`
