# Engineering｜Animation & Timing

> 这是轻量基础模块。普通 M0–M1 动画只读这里；M2–M4 再按 Router 加载 `references/motion/`。

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
