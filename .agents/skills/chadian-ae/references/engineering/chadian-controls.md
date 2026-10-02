# Engineering｜差点控件 Chadian Controls

> 按需模块。它不负责“工程有没有层级”，而负责**是否需要额外建立一层人类可直接操作的控制面 / 参数接口**。
>
> 与 `editable-engineering-gate.md` 的区别：
> - Editable Gate：复杂动画的结构门禁，解决 Parent / Rig / Keyframe / Override；
> - Chadian Controls：Control Surface Pass，解决用户以后最常改什么、从哪里改、是否能保存 / 复用。

## 1｜触发条件

命中以下需求时读取：
- “做个控件 / 控制面板 / 参数面板”
- “以后我自己方便改”
- “把常改参数暴露出来”
- “做成可调模板 / Preset / Style Pack”
- “把这些参数集中到 CTRL”
- “以后不要每次截图让 AI 改”
- 同一资产会反复换文字 / 素材 / 构图 / 颜色 / Motion / Camera

不触发：
- 单层一次性 Patch；
- 只增加一个简单 Slider / Checkbox；
- 纯视觉制作且没有复用 / 人工调参需求。

## 2｜目标

核心流程：

`AI 生成 → 暴露少量关键参数 → 人工快速调整 → 保存预设 / 模板 → 下次复用`

目标不是“所有东西都可调”，而是：

**用户无需重新描述需求、无需截图让 AI 修改，就能独立完成大部分高频微调。**

## 3｜四条默认规则

### Editable First
高概率人工修改的内容优先参数化，而不是写死。

### Minimal Controls
默认只暴露 **5–15 个核心控件**；Quick 层优先 5–8 个。
不要把所有 Transform、Expression、Effect、关键帧都做成 UI。

### Reuse Existing Framework
工程已有 CTRL / Essential Properties / Preset / MOGRT / 控制器时，优先复用。
禁止为了“统一”重新造第二套控制系统。

### Design Controls > Technical Controls
优先暴露设计意图：
- 构图偏移
- 运动强度 / 重量
- 视觉复杂度
- 装饰密度
- 空间感
- 发光 / 材质强度

而不是直接把几十个底层技术参数扔给用户。

## 4｜AE 控件层级

按需建立，不存在的模块不要创建。

```text
CTRL_控制器

QUICK
├─ 5–8 个最常改参数

CONTENT
├─ 文字 / 数值
├─ SLOT 素材
└─ 显示 / 隐藏

LAYOUT
├─ 构图偏移
├─ Scale / Rotation
└─ Spacing

STYLE
├─ 主色 / 背景
├─ 材质 / Glow
└─ 视觉强度

MOTION
├─ Speed / Amount
├─ Overshoot / Weight
└─ Stagger

TIMING
├─ Duration
├─ IN / HOLD / OUT
└─ Marker / Progress

CAMERA
├─ Distance
├─ FOV / Focal Length
├─ Angle
└─ Motion Amount

ADVANCED
└─ 默认不暴露
```

不需要的组不要为了完整性硬建。

## 5｜AE 原生实现优先级

优先使用：
1. Parent / Child Local Transform
2. Null + Expression Controls
3. Essential Properties / MOGRT（确有跨合成或模板需求）
4. Marker / Master Progress / Time Remap
5. 短、非破坏 Expression
6. 必要时脚本 / JSX 生成控制器

典型非破坏绑定：
- Position：`value + offset`
- Rotation：`value + angle`
- Scale：`value * multiplier`
- Follow / Delay：按需 `valueAtTime()`

能通过 Parent + Child Local Transform 完成人工构图调整时，优先它，不额外制造表达式。

## 6｜三层参数

### Level 1｜Quick
默认显示，5–8 个最重要参数。

### Level 2｜Edit
需要精调时使用，总量通常控制在 10–20 个。

### Level 3｜Advanced
Expression、底层 Effect、复杂 Rig、3D、内部算法等。
默认隐藏 / 不主动暴露。

## 7｜Control Surface Pass

只有命中本模块时，在主要视觉 / Motion 已经成立后执行：

1. 找出之后最可能被人工修改的 5–15 个参数；
2. 删除低价值、重复、技术性过强的候选；
3. 复用已有 CTRL / Rig；
4. 为必要参数建立非破坏绑定；
5. 高频素材 SLOT 化；
6. 控件命名中文优先，按功能分组；
7. 测试每个控件的安全范围；
8. 重置到默认值后，画面必须恢复原设计；
9. 用户不看脚本 / Expression，也应知道从哪里改。

**不要在设计尚未成立时先花大量时间做 UI。**

## 8｜Preset / Template Ready

只有用户明确要求模板 / 预设 / 长期复用时再增加：
- 默认值；
- Reset；
- 必要 Preset；
- 可替换 SLOT；
- 最短使用说明；
- 依赖说明；
- 预览。

如果已有统一 Manifest / controls.json / 模板框架，优先接入，不新造平行协议。

## 9｜禁止

- 不把“可编辑”理解成“所有东西参数化”；
- 不每次从零写完整控制面板；
- 不为了 UI 增加大量无意义 Expression / JSX；
- 不建立第二套与现有 CTRL 冲突的入口；
- 不把几十个底层参数直接暴露给用户；
- 不为了接入控件破坏已有动画、Parent、Graph、Expression；
- 不让控制系统比作品本身更复杂。

## 10｜验收

合格的差点控件至少满足：
- 高频修改 30 秒内能找到入口；
- 单个对象 / 构图 / 风格 / Motion 的常见调整不破坏原动画；
- 主要参数数量克制；
- 无重复 CTRL / 重复绑定；
- Reset 后可恢复设计默认状态；
- 人工能独立完成常见微调；
- 为参数化增加的复杂度明显低于以后反复让 AI 修改的成本。
