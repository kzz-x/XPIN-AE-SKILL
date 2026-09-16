# Engineering｜Animation & Timing

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

## 元素级控制
按需要控制：
- 位移距离
- 缩放幅度
- 旋转
- Delay
- Stagger
- Overshoot
- Blur
- Ease 强度

## 动作按元素设计
- 窗口 → 展开 / 裁切 / 弹窗
- 数据 → 计数 / 扫描 / 增长
- 机械 → 位移 / 锁定 / 装配
- 标签 → Draw On / Slide
- 鼠标 → Position + Click feedback
- 图标 → Path / Morph / State
- Camera → 平滑推进 / 环绕 / 目标跟随

禁止所有东西都：
Opacity 0→100 + Scale 80→100。

## Curve
- UI：干净 Ease
- 机械：短缓动 + 锁定感
- 弹性物体：适度 Overshoot
- 数据：平滑增长
- 扫描：匀速或轻缓动
- 镜头：稳定平滑
- 点击：快速反馈

检查：
- 无突然刹停
- 无无意义抖动
- 无穿插
- 无无意图速度变化
- 多元素有层次 / 错帧
- Motion Blur 适度

## Complex Motion
复杂动作可在标准时间段内制作，再通过：
Marker / Time Remap / Precomp / Stretch
控制实际时长。
