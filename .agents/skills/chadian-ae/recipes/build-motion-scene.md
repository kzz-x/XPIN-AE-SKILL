# Recipe｜Build Motion Scene

1. 需求 / 视觉目标
2. Capability Preflight
3. 判断 Motion Complexity（M0–M4）
4. 素材路线
5. 工程层级
6. Design Tokens / Controls
7. 先成立 Hero Frame / 核心静态状态
8. 为主要对象选择 Motion Profile
9. 设计主动作结构：准备 → 加速 → 主运动 → 减速 → 超越/跟随 → 稳定（按需要删减）
10. 先搭共享 Motion Architecture：Master / Parent / Precomp / Local
11. 制作 Primary Beat，再做 Secondary / Stagger / Follow Through
12. Camera / 后期（仅需要时）
13. Graph / Timing / Spacing 校正
14. M2–M4 做 Animation QA
15. 关键帧静帧检查 + AE 前台连续预览
16. 局部修正
17. 交付工程说明

不要先把所有动画做完再发现构图不成立。
先让 Hero Frame / 核心状态成立，再扩展时序。

不要先给每层复制一套关键帧，再“最后整理控制器”。共享运动关系应尽量在制作前确定。

复杂 Motion 按 Router 读取：
- `../references/motion/motion-principles.md`
- `../references/motion/motion-profiles.md`
- `../references/motion/motion-control-architecture.md`
- `../references/quality/animation-qa.md`
