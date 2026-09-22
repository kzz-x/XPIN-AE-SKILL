# Engineering｜Animation & Timing

> 这是轻量基础模块。普通 M0–M1 动画只读这里；M2–M4 再按 Router 加载 `references/motion/`。
>
> 如果当前任务是在“写 AE 制作方案 / 把设计交给 Codex”，同时读取 `ae-implementation-spec.md`，在设计阶段就决定 Keyframe / Expression / Relationship / Layer Style / Plugin / CTRL，而不是执行时再猜。

## Marker First
主时序优先通过 Marker 表达阶段，例如：
```text
IN
HOLD
OUT
END
```
或：
```text
入场开始
入场结束
稳定
出场开始
出场结束
```

不要把所有时序写死在散落关键帧中。

## Implementation Choice First
在打关键帧前先判断实现法：
- **主 Timing / Hero Motion / Camera / 关键 Typography** → 优先真实 Keyframe + Graph Editor；
- **常驻漂浮 / 呼吸 / loop / 自动延迟 / 距离驱动 / 跟随 / 自动连线 / 响应式尺寸** → 优先短、可控 Expression 或 Relationship；
- **高质量常见组合** → 主节奏 Keyframe + Secondary Motion / Settle Expression；
- 不因为 Expression 能写，就把所有 Motion 程序化；也不因为 Keyframe 直观，就手工复制大量重复行为。

例如轻微常驻漂浮可在方案里直接指定低频、低幅 `wiggle()` 并把 Frequency / Amplitude 接到 CTRL；主入场则保留真实关键帧，方便人工按帧微调。

## 基础元素级控制
按需要控制：
- 位移距离
- 缩放幅度
- 旋转
- Delay
- Stagger
- Overshoot
- Blur
- Ease 强度

普通局部动作可以直接关键帧完成；不要为了简单任务先搭复杂控制系统。

## 动作按元素设计
- 窗口 → 展开 / 裁切 / 吸附 / 状态切换
- 数据 → 计数 / 扫描 / 增长 / 排序
- 机械 → 位移 / 锁定 / 装配
- 标签 → Draw On / Slide
- 鼠标 → Position + Click feedback
- 图标 → Path / Morph / State
- Camera → 平滑推进 / 环绕 / 目标跟随

禁止所有东西都：
`Opacity 0→100 + Scale 80→100 + Easy Ease`。

## Curve 基线
- UI：干净、快速、克制
- 机械：有重量与锁定感
- 弹性物体：可适度 Overshoot
- 数据：平滑、逻辑优先
- 扫描：匀速或轻缓动
- 镜头：连续稳定
- 点击：快速反馈

如果需要决定 anticipation、inertia、weight、follow through、不同对象曲线或复杂节奏，升级读取：
- `../motion/motion-principles.md`
- `../motion/motion-profiles.md`

## Shared Motion
3 个以上图层共享同类运动时，不要默认复制关键帧。

优先判断：
- Parent Null；
- `CTRL_动画 + Master Progress`；
- Expression / Stagger；
- Precomp + Time Remap。

复杂情况读取：`../motion/motion-control-architecture.md`。

## Complex Motion
复杂动作可在标准时间段内制作，再通过：
Marker / Time Remap / Precomp / Stretch / Master Progress
控制实际时长。

## 基础检查
- 无突然刹停
- 无无意义抖动
- 无穿插
- 无无意图速度变化
- 多元素有层次 / 错帧
- Motion Blur 适度
- 简单任务不过度工程化

M2–M4 动画完成前，按 Router 追加 `quality/animation-qa.md`。
