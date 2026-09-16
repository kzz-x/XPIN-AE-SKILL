# Capability｜Native AE

Agent 应主动考虑 AE 原生能力，不要只会 Transform。

## Text
- Text Animator
- Range Selector
- Expression Selector
- Tracking / Position / Opacity / Blur 动画

逐字 / 逐词动画优先 Text Animator，不拆几十文字层。

## Shape
- Path
- Trim Paths
- Repeater
- Merge / Offset / Round Corners 等可用运算
- Shape Morph

## Mask / Matte
- Mask Path
- Expansion / Feather
- Track Matte
- Alpha / Luma 逻辑

窗口揭示、局部 reveal 优先 Mask / Matte。

## Time
- Time Remap
- Posterize Time
- Precomp
- Marker
- Essential Properties

## Composition
- Adjustment Layer
- Guide Layer
- Parent / Null
- Blend Mode
- Motion Blur
- Layer Style
- Native Effects

原则：
使用最符合 AE 工作方式的原生结构，而不是为了脚本好写，把所有效果退化成基础图层。
