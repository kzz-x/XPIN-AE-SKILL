# 动画｜控制架构（Control Architecture）

> 目标：让复杂动画既能统一控制，又保留局部特殊性；优先建立关系与共享控制，不把同类动画复制成几十层散落关键帧。

## 1｜最高原则

**Shared motion goes upward. Unique motion stays local.**

**Relationships should be encoded, not manually synchronized.**

- 共享运动 → 往 Master / Parent / Precomp 提升。
- 对象关系 → 用 Parent / Attach / Target / Constraint / Dynamic Reference 表达。
- 独特运动 → 留在 Layer 局部。
- 3 个及以上图层共享同类运动时，优先控制器 / Parent / Expression / Stagger，而不是复制关键帧。

---

## 2｜Level 0：Relationship Before Motion

进入 Master / Precomp / Local 分配之前，先做 Relationship Scan：
- 谁控制谁？
- 谁跟随谁？
- 谁携带谁？
- 谁指向谁？
- 谁必须保持相对关系？
- 哪些位置 / 尺寸应该动态引用？
- 哪些是共享运动？
- 哪些才是真正独立运动？

如果关系存在，先建立 Parent / Null / Attach / Target / Constraint / Dynamic Reference，再设计动画。

禁止为了让两个对象“看起来同步”，分别给它们复制近似关键帧。

详细：`relationship-rigs.md`

---

## 3｜三级动画架构

### Level 1｜Master Motion Channels
负责镜头级、场景级、组级和可复用节奏。

推荐：
```text
动画控制｜CTRL_MOTION
  Master Progress
  入场进度｜In Progress
  出场进度｜Out Progress
  Motion Strength
  Speed / Duration Scale
  Stagger
  Overshoot Strength
  Settle Strength

NULL_场景运动
NULL_主体组
NULL_UI组
NULL_镜头Rig
```

要求：
- 整组 Position / Scale / Rotation / Opacity 等整体运动优先交 Parent Null。
- Master Progress 统一推进共享动作；局部通过 offset / range / expression 派生。
- 不把不同 Motion Profile 强绑成同一个响应。
- 只暴露真正高频可调参数。

### Level 2｜Precomp + Time Remap
负责模块内部动画、重复组件和整体 retiming。

适合：重复 UI、图标动画、机械子机构、标签 / 标题、可复用复杂动作。

规则：
- 组件内部只做一套干净动画。
- 外层用 Marker / Time Remap / Stretch / Essential Properties / Master Progress 控制节奏。
- 整体改快慢优先 retime 模块，不逐层拖关键帧。
- Precomp 深度以容易理解和替换为准。

### Level 3｜Layer Local Motion
只保留真正独特动作：
- 单对象被点击 / 删除；
- 特殊机械部件旋转；
- 单独数值跳变；
- 特殊 path / morph；
- 局部 Secondary Action。

局部动画不重复承担上层已经负责的整体运动。

---

## 4｜Keyframe Compression

目标不是零关键帧，而是：

**零重复关键帧，保留少量有意义、可在 Graph Editor 调整的主关键帧。**

判断一个值是否应该烘焙成 Keyframe 前：
1. 它是否来自另一个对象？
2. 是否属于可计算关系？
3. 是否与其他对象共享 Progress？
4. 用户以后是否可能移动目标？
5. 用户是否需要 Graph Editor？

动态关系 → Expression / Parent / Rig。
共享时间 → Master Progress。
真正独有动作 → Local Keyframes。

不要把当前布局坐标误当永久动画数据。

---

## 5｜Stagger 架构

3 个以上同类对象错帧时优先参数化：
```text
start = masterStart + indexOffset * stagger
localProgress = remap(masterProgress, start, start + duration)
```

实际可用 Expression、Marker、脚本生成或预合成时间偏移。

要求：
- Stagger 可统一调节；
- 单个对象允许 override；
- 不强制绝对等间隔，可按视觉层级分组。

---

## 6｜关键帧应该放在哪里

优先级：
1. Relationship / Constraint → Parent / Attach / Target / Expression / Rig。
2. 镜头 / 场景整体运动 → Parent Null / Rig。
3. 多对象共享逻辑 → 动画控制｜CTRL_MOTION + Expression / Master Progress。
4. 重复复杂模块 → Precomp 内部一次制作 + 外部 Time Remap / Offset。
5. 单对象特例 → Layer 本地 Keyframe。

如果多个图层拥有几乎相同的 Transform Keyframes，默认视为架构警报，除非确有独立编辑需求。

---

## 7｜Editable First

人类应能快速回答：
- 整体快一点 → 改哪里？
- Stagger 大一点 → 改哪里？
- 主体 Overshoot 小一点 → 改哪里？
- Target 换位置 → 是否自动适配？
- Carrier 移动 → Payload 是否继续跟随？
- 只改单个对象 → 改哪里？
- 整个场景移动 → 改哪个 Parent Null？

如果这些问题需要逐层查关键帧，架构还不够好。

---

## 8｜Animation / Adjustment 分离

目标：**动画成立后，人工仍能单独改一个对象，而不用碰原始关键帧。**

优先结构：
```text
00_CTRL
└─ NULL_组动画 / Rig        ← 主动画
   └─ 视觉图层              ← 本地构图 / 人工微调
```

如果 Visual Layer 必须自己保留独有关键帧，又需要额外人工 Offset，则给该属性提供非破坏控制：

```jsx
// Position：Point Control / 3D Offset 视维度选择
value + effect("位置偏移")("Point")

// Rotation
value + effect("旋转偏移")("Slider")

// Opacity
clamp(value + effect("透明度偏移")("Slider"), 0, 100)

// Scale：比例控制通常比单纯相加更稳
s = 1 + effect("缩放比例")("Slider") / 100;
value * s
```

规则：
- 能用 Parent + Child 本地 Transform 解决，优先它；这样可直接在 Comp 中拖动 Child；
- 必须在同一属性叠加调整时，再用 `value + offset` / multiplier；
- 不为了 Offset 改写、平移或复制原始关键帧；
- Shared Motion 在父级，Unique Motion 在本层，Manual Adjustment 有独立入口；
- Expression 保持短、可读、可关闭，不制造黑盒。

## 9｜Expression 使用边界

Expression 用于建立关系，不用于制造黑盒。

优先：
- Dynamic Target / Follow / Connector；
- 简单线性映射；
- Master Progress；
- Stagger / Delay；
- Parent / Token 引用；
- 可解释 Overshoot / Damping。

避免：
- 超长、无注释、多重互相引用；
- 为了少打几个关键帧写难修改算法；
- 同一功能存在多套控制器。

复杂 Expression 要有清晰入口和必要注释。

---

## 10｜与 Marker 的关系

推荐职责：
```text
Relationship = 对象之间怎么关联
Marker = 什么时候发生
Master Channel = 发生到什么程度
Parent Null = 整组怎么移动
Precomp / Time Remap = 模块如何重定时
Layer Keyframes = 对象独有动作
```

不要把所有职责塞进一种机制。

---

## 11｜Motion Architecture QA

复杂动画交付前检查：
- 是否存在 3+ 图层复制同类关键帧？
- 是否存在 Carrier / Payload、Follow、Target 等关系却仍靠手工同步？
- 整体运动是否错误地下放到子层？
- 是否应该 Parent / Precomp + Time Remap / Master Progress？
- 移动 Target 后相关运动是否仍成立？
- Master Progress / Stagger 是否真正可调？
- 局部 override 是否保留？
- 人工微调是否有明确入口：Child Local Transform 或 `value + offset` / multiplier？
- Expression 是否清晰、无循环、无错误？
- 人类是否能在 30 秒内找到主要控制入口？
