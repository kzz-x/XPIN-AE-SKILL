# Quality｜Animation QA

> 仅对 M2–M4 或用户明确要求深度动画验收时加载。小型 Patch 不做全套 Motion 审计。

## 1｜Motion Design
检查：
- 是否所有对象无脑 Easy Ease；
- UI / 机械 / 文字 / 数据 / Camera 是否错误共享同一运动逻辑；
- Timing / Spacing 是否匹配重量和动作意图；
- 是否有无理由 bounce / shake / rotation / overshoot；
- 是否缺必要 anticipation / deceleration / settle；
- Follow Through / Overlapping Action 是否用于真正存在层级或连接关系的对象；
- Secondary Action 是否抢主动作。

发现重复模板时，应回到 Motion Principles / Profiles 重选逻辑。

## 2｜Rhythm / Staging
检查：
- 是否所有元素同时开始、同时结束；
- Stagger 是否机械等间距，还是有视觉分组；
- 是否存在 Primary Beat / Secondary Beat / Rest Beat；
- 重要文字 / 数据是否有足够 Hold；
- 是否整段一直在动；
- 动作密度是否匹配信息量；
- 转场前后是否有清晰视觉承接。

## 3｜Weight / Inertia
检查：
- 加速和减速是否合理；
- 重物是否有足够惯性；
- 精密机械是否有不必要弹性；
- Soft Graphic 是否完全无 Follow Through；
- Overshoot 幅度、次数、衰减是否匹配对象属性；
- Settle 是否过长、过短或无限抖动。

## 4｜Camera Continuity
检查：
- Camera 是否有明确目标；
- 方向、速度、空间关系是否连续；
- 是否突然急停 / 无理由改向；
- Camera 与主体是否争夺 Primary Beat；
- 是否真的需要真实 Camera；
- Camera settle 是否克制；
- Target / Focus 是否应使用关系 Rig，而不是人工同步多套关键帧。

## 5｜Expert Relationship QA

检查：
- 两个对象是否靠两套近似 Position Keyframe 假装 Follow？
- Carrier / Payload 是否应该 Attach / Parent / Constraint？
- Target 改位置后 Motion 是否失效？
- 连线端点是否与节点分开打关键帧？
- Label / Callout 是否可以 Follow 主体？
- Arrow / Camera / Mechanical Part 是否应 Look At / Target？
- Dynamic Bounds 是否被写死？
- 是否存在本可共用的 Path / Progress / Parent？

### Stress Test
对关键 Target 做小范围临时偏移，例如 100px 或等价改动：
- 相关对象是否自动保持应有关系？
- 恢复 Target 后系统是否正常？

如果本应能够自动适配却不能，Relationship Rig 不合格。

## 6｜Motion Architecture
检查：
- 3+ 图层共享同类运动时是否仍复制关键帧；
- 是否存在大量相同 Transform Animation；
- 整体运动是否应交 Parent Null / Rig；
- 重复模块是否应 Precomp 后统一 retime；
- 是否可以 Master Progress + Expression / Stagger；
- Marker / Control / Time Remap / Layer Keyframe 职责是否混乱；
- 是否出现多套重复 CTRL_动画；
- Expression 是否变成难维护黑盒；
- 单对象是否仍可 Local Override。

## 7｜Keyframe Compression QA

目标不是 0 Keyframe，而是 0 Duplicate Keyframe。

检查：
- 是否存在大量重复 Position / Scale / Rotation / Opacity KF；
- 是否可以用 1 个 Master Progress 驱动共享时间；
- 动态目标是否被烘焙成固定坐标；
- 用户是否仍可在 Graph Editor 调主要节奏；
- 是否为了“无关键帧”写成复杂 Expression 黑盒；
- Local Keyframe 是否真的属于该对象独有动作。

通过原则：

**Few meaningful keyframes > many duplicated keyframes.**

## 8｜Native Construction QA

当场景包含大量 Shape / 基础 Transform 时检查：
- 是否用黑 Shape + Blur + Offset 手工模拟普通 Shadow；
- 是否用额外 Shape 模拟普通 Layer Stroke；
- 是否拆大量 Text Layer 模拟 Text Animator；
- 是否用遮挡 Shape 模拟本应使用 Mask / Matte 的 Reveal；
- 是否复制大量结构代替 Repeater / Precomp；
- 是否复制大量同类 Effect，而 Adjustment Layer / Shared Structure 更合理。

不是强制使用“更高级功能”；而是要求技术选择有理由。

## 9｜Editable QA
人工接手时应能快速完成：
- 整体改快 / 改慢；
- Stagger 增减；
- Motion Strength 调整；
- Camera 单独调整；
- 单对象改 Timing；
- Profile 的 Overshoot / Settle 调整；
- 移动 Target 而不重做相关路径；
- 调 Carrier 后 Payload 仍保持关系。

如果必须逐层翻找几十个关键帧，Motion Architecture 不合格。

## 10｜Motion 可测量项｜速度采样法

静帧看不出 Timing / Weight / 有没有「一卡一卡」。**但可以量化，不必只靠肉眼。**

对承担主运动的属性（Position / Scale / Rotation）按固定步长采样，计算相邻采样的位移量：

```js
var L = comp.layer("目标层");
var seq = [], prev = null;
for (var t = t0; t <= t1; t += 0.1) {
  var v = L.position.valueAtTime(t, false);
  if (prev) {
    var d = Math.sqrt(Math.pow(v[0]-prev[0],2) + Math.pow(v[1]-prev[1],2));
    seq.push(d.toFixed(0));
  }
  prev = v;
}
return seq.join(" ");
```

判读标准：

| 序列特征 | 结论 |
|---|---|
| **单调递增** | 加速段，合理（下落 / 起步） |
| **单调递减** | 减速段，合理（吸附 / 收尾） |
| **先增后减、无锯齿** | 标准「加速 → 减速」曲线 ✅ |
| **忽高忽低（如 8 100 5 100 6 99）** | ✗ **关键帧空间分布不均**，或中段 influence 过大导致每个关键帧都「停一下」 |
| **末端突然放大（如 … 43 61 132）** | ✗ 缓动方向做反了：`1-(1-s)^k` 会让末端**时间几乎停住**，应改用 `s^k` |

修正常见做法：
- 沿**弧长**均匀采样关键帧，而不是沿参数 `t` 均匀（贝塞尔参数 ≠ 弧长）；
- 用解析函数做时间映射（`g(s)=s^k` 起步快、末端慢），关键帧之间用 **LINEAR**，
  让速度完全由映射决定；
- 避免为每个关键帧都设高 influence —— 高 influence 等价于「在该帧附近减速」。

**注意**：速度采样是**诊断工具**，不是最终验收。平滑度可量化，
但「有没有味道」仍以 AE 前台人工预览为准。

---

## 11｜通过标准

复杂动画通过 QA 时应同时满足：

**运动有意图 + 对象有差异 + 节奏有主次 + 关系被正确编码 + Native 能力选择合理 + Camera 连续 + 关键帧不无脑复制 + 人工可快速调整。**
