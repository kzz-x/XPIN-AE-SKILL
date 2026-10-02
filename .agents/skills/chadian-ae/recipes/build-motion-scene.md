# Recipe｜Build Motion Scene

1. 需求 / 视觉目标
2. Capability Preflight
3. Relationship Scan
4. Native Capability Decision
5. 判断 Motion Complexity（M0–M4）
6. 素材路线
7. Hero Frame / 核心静态状态先成立
8. **Engineering Skeleton**：`00_CTRL → Motion/Group Null/Rig → Visual Layers`
9. 声明 Motion Ownership：Global / Group / Local / Repeated / Secondary
10. Design Tokens / Controls / Manual Override 入口
11. 建立必要 Relationship / Constraint Rig
12. 为主要对象选择 Motion Profile
13. 分配 Master / Parent / Precomp / Local
14. 设计主动作结构：准备 → 加速 → 主运动 → 减速 → 超越/跟随 → 稳定（按需要删减）
15. 制作 Primary Beat；普通 A → B 从 2 个主关键帧开始，用 Graph / Bezier 塑造速度，不用密集关键帧模拟 easing
16. **Editable QA Gate**：检查 Parent、Keyframe Count、重复 Transform、`value + offset` / multiplier 或 Child Local Override；FAIL 先整理
17. 再做 Secondary / Stagger / Follow Through
18. Camera / 后期（仅需要时）
19. Graph / Timing / Spacing 校正
20. Native / Relationship / Animation QA
21. 关键帧静帧检查 + AE 前台连续预览
22. 局部修正
23. 交付工程说明

不要先把所有动画做完再发现构图不成立。
先让 Hero Frame / 核心状态成立，再扩展时序。

不要先给每层复制关键帧，再“最后整理控制器”。共享运动和对象关系应在制作前确定。

## 制作前快速问

- 这是什么对象 / 效果？
- AE 是否已有更直接的 Native Feature？
- 谁跟随谁 / 携带谁 / 指向谁 / 连接谁？
- 哪些位置 / 尺寸应该动态引用，而不是写死？
- 哪些动画应该共享 Progress？
- 哪些属性需要用户以后直接手动微调？
- 这段运动属于 Global / Group / Local / Repeated / Secondary 哪一层？
- 最后才决定 Local Keyframes。

## 按需读取

新建完整动画 / 多层共享 Motion / 强调后续人工可改：
- `../references/engineering/editable-engineering-gate.md`

用户明确要求控制面板 / 参数化 / Preset / 高频人工调参，或该镜头明显会长期复用：
- `../references/engineering/chadian-controls.md`

创建新视觉结构 / 重构：
- `../references/capabilities/native-ae.md`

出现 Attach / Follow / Target / Carry / Connector / Auto Layout / Dynamic Bounds：
- `../references/motion/relationship-rigs.md`

复杂 Motion：
- `../references/motion/motion-principles.md`
- `../references/motion/motion-profiles.md`
- `../references/motion/motion-control-architecture.md`
- `../references/quality/animation-qa.md`
