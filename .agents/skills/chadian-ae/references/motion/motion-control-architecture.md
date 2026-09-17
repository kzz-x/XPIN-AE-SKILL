# Motion｜Control Architecture

> 目标：让复杂动画既能统一控制，又保留局部特殊性。不要把同类动画复制成几十层散落关键帧。

## 1｜最高原则

**Shared motion goes upward. Unique motion stays local.**

- 共享运动 → 往 Master / Parent / Precomp 提升。
- 独特运动 → 留在 Layer 局部。
- 3 个及以上图层共享同类运动时，优先考虑控制器 / Parent / Expression / Stagger，而不是复制关键帧。

## 2｜三级动画架构

### Level 1｜Master Motion Channels
负责镜头级、场景级、组级和可复用节奏。

推荐：
```text
CTRL_动画
  Master Progress
  In Progress
  Out Progress
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
- 整组 Position / Scale / Rotation / Opacity 等整体运动优先交给 Parent Null。
- Master Progress 负责统一推进共享动作；局部通过 offset / range / expression 派生。
- 不要为了“统一控制”把所有属性强绑到同一个值；不同 Profile 仍应保留差异化响应。
- Slider / Angle / Checkbox 等控制只暴露真正高频可调参数。

### Level 2｜Precomp + Time Remap
负责模块内部动画、重复组件和整体 retiming。

适合：
- 重复 UI 卡片；
- 图标动画；
- 机械子机构；
- 标签 / 标题模块；
- 可复用复杂动作。

规则：
- 组件内部只做一套干净动画。
- 外层通过 Marker / Time Remap / Stretch / Essential Properties / Master Progress 控制节奏。
- 需要整体改快慢时，优先 retime 模块，而不是逐层拖关键帧。
- Precomp 深度以“容易理解和替换”为准，不为动画控制无限套娃。

### Level 3｜Layer Local Motion
只保留真正独特的动作：
- 某个对象单独被点击 / 删除；
- 特殊机械部件旋转；
- 单独数值跳变；
- 特殊 path / morph；
- 局部修饰和 Secondary Action。

局部动画不应重复承担上层已经负责的整体运动。

## 3｜Stagger 架构

3 个以上同类对象错帧时优先参数化：
```text
start = masterStart + indexOffset * stagger
localProgress = remap(masterProgress, start, start + duration)
```

实际实现可以使用 Expression、Marker、脚本生成或预合成时间偏移；重点是：
- Stagger 可统一调节；
- 单个对象允许局部 override；
- 不要求所有间隔绝对相等，可按视觉节奏形成短-短-长等分组。

## 4｜关键帧应该放在哪里

优先级：
1. 镜头 / 场景整体运动 → Parent Null / Rig。
2. 多对象共享逻辑 → CTRL_动画 + Expression / Master Progress。
3. 重复复杂模块 → Precomp 内部一次制作 + 外部 Time Remap / offset。
4. 单对象特例 → Layer 本地关键帧。

如果 10 个图层拥有几乎相同的 Position / Scale 关键帧，默认视为架构问题，除非存在明确的独立编辑需求。

## 5｜Editable First

动画系统必须让人类快速回答：
- 整体快一点 → 改哪里？
- 所有卡片 stagger 大一点 → 改哪里？
- 主体 overshoot 小一点 → 改哪里？
- 只改单个对象 → 改哪里？
- 整个场景向左 / 放大 → 改哪个 Parent Null？

如果这些问题需要逐层查找关键帧，架构还不够好。

## 6｜Expression 使用边界

Expression 用于建立关系，不用于制造不可维护的“黑盒”。

优先：
- 简单线性映射；
- Master Progress；
- Stagger / Delay；
- Parent / token 引用；
- 可解释的 overshoot / damping。

避免：
- 超长、无注释、多重层级互相引用；
- 为了少打几个关键帧写难以人工修改的复杂算法；
- 同一功能存在多套控制器。

复杂 Expression 必须语义清晰，并尽量通过 Effect 名称 / Comment 说明入口。

## 7｜与 Marker 的关系

Marker 表达阶段，Control 表达强度和进度，Precomp / Time Remap 表达模块时长，Layer 表达局部特例。

推荐职责：
```text
Marker = 什么时候发生
Master Channel = 发生到什么程度
Parent Null = 整组怎么移动
Precomp / Time Remap = 模块内部如何被重新定时
Layer Keyframes = 这个对象独有的动作
```

不要把所有职责塞进一种机制。

## 8｜Motion Architecture QA

复杂动画交付前检查：
- 是否存在 3+ 图层复制同类关键帧却没有共享控制？
- 整体运动是否错误地下放到每个子层？
- 是否应该使用 Parent Null？
- 重复模块是否应该 Precomp + Time Remap？
- Master Progress / Stagger 是否真正可调？
- 局部 override 是否仍然可做？
- Expression 是否清晰、无循环、无错误？
- 人类是否能在 30 秒内找到主要动画控制入口？
