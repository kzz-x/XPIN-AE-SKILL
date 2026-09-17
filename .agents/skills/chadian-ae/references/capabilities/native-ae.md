# Capability｜Native AE Decision

Agent 应像熟练 AE 用户一样选择实现方式，而不是只会 Transform / Shape / Keyframe。

核心：

**先识别需求语义 → 优先选择最符合 AE 工作方式、最好修改的原生机制 → 最后才手工模拟。**

---

## 1｜Native Feature Before Manual Construction

每次创建新元素、效果或动画结构前先问：
1. 这个需求本质是什么？
2. AE 是否已有直接表达它的原生能力？
3. 用户以后最可能修改什么？
4. 哪种方案修改成本最低？
5. 是否正在用多个基础 Shape / Transform / Keyframe 模拟一个现成功能？

只有原生能力无法满足视觉、动画或可编辑性需求时，才手工构建。

**“Shape / JSX 比较好写”不是技术选择理由。**

---

## 2｜常见决策

### 描边
普通图片 / Precomp / Text 外轮廓：
→ 优先考虑 Layer Style / Stroke / 合适 Native Effect。

如果需要 Trim Paths / Dash / Path Animation / 几何本身就是主体：
→ Shape Stroke 更合理。

不要默认额外画一层 Shape 模拟所有描边。

### 阴影
普通视觉投影：
→ Drop Shadow / Layer Style / Native Effect。

需要特殊透视、独立形变、艺术化阴影：
→ 才考虑独立 Shadow Layer。

不要默认：黑 Shape + Blur + Offset。

### 文字动画
逐字 / 逐词：
→ Text Animator + Range Selector / Expression Selector。

不要默认拆成几十个 Text Layer。
只有每个字确实需要独立排版 / 3D / 素材化时才拆。

### Reveal / 裁切
内容显示范围变化：
→ Mask / Track Matte / Alpha / Luma Matte。

不要默认画背景色 Shape 假装遮挡。

### 重复元素
规则性重复：
→ Repeater。

独立内容但共享结构：
→ Precomp / Essential Properties / Shared Rig。

不要默认复制大量相同结构。

### 整组运动
多个元素保持固定相对关系：
→ Parent / Null。

不要给每层复制相同 Position / Scale / Rotation Keyframes。

### 多层统一视觉效果
统一调色 / Blur / Grain / 后期：
→ 优先 Adjustment Layer 或共享结构。

不要无理由把同一 Effect 复制到大量图层。

### 自动尺寸 / 排版
文字框、标签底板等需要随内容变化：
→ `sourceRectAtTime()` + Padding + Expression / Essential Properties。

不要把文字宽度和背景框宽度维护成两套独立值。

### 指向 / 朝向
A 始终面向 B：
→ Target Relationship / Vector / atan2 / Look-at Rig。

不要手工同步 Rotation。

### 连线
节点 A 与节点 B 之间的线：
→ Start / End 实时引用两个节点位置。

不要把节点和线端点分别打动画。

### Camera
真实空间运动：
→ Camera Rig + Target Null。

二维推拉：
→ Transform / Parent Null。

不要为了显得高级强行 Camera。

---

## 3｜基础能力索引

### Text
- Text Animator
- Range Selector
- Expression Selector
- Tracking / Position / Opacity / Blur 动画

### Shape
- Path / Morph
- Trim Paths
- Repeater
- Merge / Offset / Round Corners

### Mask / Matte
- Mask Path / Expansion / Feather
- Track Matte
- Alpha / Luma

### Time
- Time Remap
- Posterize Time
- Precomp
- Marker
- Essential Properties

### Composition
- Adjustment Layer
- Guide Layer
- Parent / Null
- Blend Mode
- Motion Blur
- Layer Style
- Native Effects

---

## 4｜Manual Construction Gate

决定不用直接对应的 Native Feature、而要手工搭基础层模拟时，内部必须能回答：

> 为什么原生机制不适合？

合理原因：
- 原生功能无法满足特定动画要求；
- 原生结果质量不足；
- 需要特殊可编辑结构；
- 需要与其他系统共享参数；
- 当前工具接口无法稳定调用该能力。

不合理原因：
- Shape 更容易生成；
- 多打关键帧更直接；
- 不熟悉 AE 其他功能。

---

## 5｜Relationship Trigger

如果需求出现 Attach / Follow / Carry / Target / Connect / Auto Layout / Dynamic Bounds / Shared Motion，不要只停在 Native Capability 选择。

继续读：
`../motion/relationship-rigs.md`

---

## 6｜目标

优先选择：

**语义正确 + AE 原生 + 修改成本低 + 可继续动画**

而不是：

**最容易自动生成。**
