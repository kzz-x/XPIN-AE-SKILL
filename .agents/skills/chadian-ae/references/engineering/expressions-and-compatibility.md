# Engineering｜Expressions & AE26 Compatibility

## Expression
必须：
- 简短
- 可读
- 可复用
- 必要时中文注释
- 有 fallback
- 避免逐帧重扫描
- 避免无意义 sampleImage()
- 避免复制同一长表达式到大量图层

Expression 不应替代本应由关键帧 / Graph 完成的动画设计。

## AE26 中文版
JSX 访问底层属性优先 `matchName`：
```text
ADBE Transform Group
ADBE Position
ADBE Scale
ADBE Opacity
ADBE Slider Control
ADBE Color Control
ADBE Checkbox Control
ADBE Angle Control
ADBE Point Control
ADBE Layer Control
```

不要依赖：
“Transform / 变换 / Position / 位置”。

## Defensive
- missing font → fallback
- missing footage → placeholder / report
- missing plugin → native fallback / report
- unsupported property → skip + explain
- repeated run → avoid duplicate garbage

## Performance
避免：
- 数百无意义 Shape
- 大量重复 Expression
- 大面积实时高采样 Blur / Glow
- 所有层 Motion Blur
- 所有层 3D
- 巨量路径点
- 每帧复杂字符串查找

优先：
Precomp / shared control / reuse / vector assets。
