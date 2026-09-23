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

## 10｜Motion 可测量项｜逐帧速度 / 加速度诊断

静帧看不出 Timing / Weight / 单帧卡顿，但数值采样可以辅助定位。**它是诊断工具，不是统一审美评分器。**

### 先声明预期 Motion Profile

采样前先判断当前 Motion Phase 想要什么，例如：
- `EASE_IN`：总体加速；
- `EASE_OUT`：总体减速；
- `EASE_IN_OUT`：先加速后减速；
- `CONSTANT`：近似匀速；
- `HOLD`：速度接近 0；
- `IMPACT / RECOIL`：允许瞬时峰值与反向；
- `SPRING / BOUNCE`：允许多次方向变化与衰减振荡。

**只有预期 Profile 本身要求单调时，才用“单调递增 / 递减”作为检查条件。** 非单调速度本身不是错误。

### A｜逐帧采样：查卡顿、pop、单帧异常

检测“卡不卡”优先使用：

```js
var dt = comp.frameDuration;
var prev = null;
var prevSpeed = null;
var out = [];

for (var t = t0; t <= t1 + dt * 0.25; t += dt) {
  var v = prop.valueAtTime(t, false);
  if (prev !== null) {
    var d;
    if (v instanceof Array) {
      var sum = 0;
      for (var i = 0; i < v.length; i++) sum += Math.pow(v[i] - prev[i], 2);
      d = Math.sqrt(sum);
    } else {
      d = Math.abs(v - prev);
    }

    var speed = d / dt;
    var accel = (prevSpeed === null) ? null : (speed - prevSpeed) / dt;
    out.push({ t:t, delta:d, speed:speed, accel:accel });
    prevSpeed = speed;
  }
  prev = v;
}
```

逐帧重点找：
- 设计上没有理由的单帧速度尖峰；
- 本应连续运动却突然 0 → 大峰值 → 0；
- EASE_OUT 尾段突然重新加速；
- CONSTANT 段出现明显周期性停顿；
- 没有设计意图的方向瞬间反转。

### B｜粗采样：看宏观速度轮廓

`0.05–0.1s` 或每 2–3 帧的粗采样可以用来观察整体加速 / 减速趋势，但**不能拿来证明没有单帧卡顿**。30fps 下 0.1s 已跨约 3 帧，60fps 下跨约 6 帧，可能把局部异常平均掉。

### 不同属性不要混成一个单位

- Position：像素 / 秒；必要时看路径方向与弧长；
- Scale：百分比 / 秒；
- Rotation：度 / 秒；
- 不要把 Position、Scale、Rotation 的 raw 数值放进同一阈值比较。

如果对象受 Parent / Camera / Expression / 3D Rig 共同驱动，**只采 local Position 可能与屏幕可见速度不一致**。这时采承担真实运动职责的 Master / Rig，或用合适的 comp/world-space 结果做验证。

### 判读原则

| 现象 | 判读 |
|---|---|
| 与声明的 Motion Profile 一致，变化连续 | 通常正常 |
| 无设计理由的单帧 spike / stop-go | 高概率需要检查 |
| EASE_IN / OUT 段明显违反预期单调趋势 | 检查曲线、关键帧空间分布或时间映射 |
| SPRING / BOUNCE / IMPACT 出现非单调 | 可能完全正确，应看振幅、衰减和节奏 |
| 粗采样平滑，但逐帧存在尖峰 | 仍属于实际卡顿风险 |

常见修正方向：
- 路径速度异常 → 检查贝塞尔参数与真实弧长，不默认“参数等距 = 空间等距”；
- 解析 Motion → 明确 `time → progress` 映射方向，避免把反函数缓动判断写反；
- 关键帧过密且每个都高 influence → 检查是否人为制造了反复减速；
- 共享 Motion → 优先修 Master / Rig，而不是逐层抹平曲线。

**最终验收仍以 AE 前台连续播放 + Motion Profile 意图为准。** 数值只能告诉你“哪里值得看”，不能自动决定“有没有味道”。

---

## 11｜通过标准

复杂动画通过 QA 时应同时满足：

**运动有意图 + 对象有差异 + 节奏有主次 + 关系被正确编码 + Native 能力选择合理 + Camera 连续 + 关键帧不无脑复制 + 人工可快速调整。**
