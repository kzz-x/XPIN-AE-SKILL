# Asset First｜AE 现成资产优先

用于 AE 设计与 BUILD 前的独立资产门禁。它和 Reference First 不同：

- Reference First：决定“画面 / 动作应该怎么设计”；
- Asset First：决定“有没有成熟资产可以直接拿来用”。

核心原则：

**Search → Select → Localize → Import → Build**

凡现实中高度可能已有成熟资产的对象，不允许因为 Shape / JSX / Blender 更容易写就直接从零重画。

## 1｜强制触发

以下对象默认必须先完成 Asset Search：
- 有明确语义的 Icon / Symbol / UI Asset；
- Logo / 品牌标识 / 官方产品 PNG / SVG / Press Kit；
- Animated Icon / Lottie / Icon Morph；
- Emoji / 标准符号 / 工业符号；
- 通用 3D Icon / 3D Model / Props；
- HDRI / Texture / Material；
- 成熟 UI Component / 图表组件 / AE Template / Motion Asset。

以下通常无需 Asset Search：
- 圆 / 线 / 矩形等无语义基础几何；
- 已锁定视觉中的自定义装饰形状；
- 用户已有工程内的小 Patch；
- 明确数据决定的简单图表；
- 用户明确要求原创且不依赖真实身份的元素。

**简单不等于可以跳过。**
一个 cloud-upload 图标很简单，但属于成熟语义资产；一个普通圆则不是。

## 2｜搜索顺序

1. 当前 AEP / 项目资产 / Style Pack / Template
2. 用户提供资产
3. 官方品牌 / Press Kit / 产品资源
4. 结构化开源 Asset Registry / 官方开源库
5. 授权清晰的专业素材库
6. AI / AE / Blender 自制
7. Placeholder

优先 Agent 友好的来源：API / JSON Registry / CLI / MCP / npm / raw SVG / direct download。

具体来源按需读取：`../assets/source-registry.md`。

## 3｜搜索完成条件

至少满足：
- 使用中文 + 英文 / 常见同义词检索；
- 找到 1–3 个真实候选，或明确记录没有合适候选；
- 核对格式、许可、视觉适配和可编辑性；
- 决定直接用 / 改造 / Morph / 转 Shape / 转 Footage / Placeholder / 放弃。

禁止：
- 编造不存在的图标 ID / 模型 / 下载地址 / 许可；
- 仅因为自己能画就跳过搜索；
- 把搜索缩略图或不明来源截图当最终素材；
- 让最终 AEP 依赖实时 URL。

## 4｜AE 实现优先级

找到资产后再决定实现：
- SVG / AI → 优先保留矢量，必要时转 Shape；
- Animated SVG / Lottie → 先判断能否直接复用；不兼容时转可控 AE 结构或预渲染；
- Morph icon → 优先使用成熟 path morph 生成路径，再转 AE Shape Path；
- PNG / Footage → 进入标准素材槽，保留 Crop / Matte / Motion；
- 3D / HDRI / Texture → 先本地化，再交 AE / Blender 对应 Adapter；
- Template / Component → 先检查依赖、许可与风格一致性，再局部复用。

## 5｜Asset Manifest Gate

正式 BUILD 前，命中对象必须形成极短 Asset Preflight：

```text
ASSET PREFLIGHT
READY | cloud-upload | Tabler | SVG | MIT
READY | mic→document | Morphicons | path morph
PLACEHOLDER_APPROVED | industrial-connector | no suitable open asset
```

合法状态：
- `READY`
- `USER_PROVIDED`
- `PLACEHOLDER_APPROVED`
- `NOT_APPLICABLE`

`UNSEARCHED` 不能进入正式 BUILD。

如果上游 `chadian-post-GTP-project` 已提供 `ASSET_MANIFEST / LOCKED_ASSETS`，直接继承，不重复发散搜索；只补当前 AE 实现真正缺失的部分。
