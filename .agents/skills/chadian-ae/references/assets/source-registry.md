# Asset Source Registry｜Agent 可检索资产源

只在 Asset First 命中时读取。这里保存“入口和用途”，不把整库内容塞进上下文。

## Icon / Symbol
- **Iconify**：通用总入口；优先按实际 icon ID 取 SVG / JSON，不编名称。
- **Lucide**：常用干净线性 UI icon。
- **Tabler Icons**：大量线性 SVG，适合技术 / UI 语义。
- **Iconoir**：线性 SVG 补充。
- **Phosphor Icons**：多权重 / duotone；同画面统一权重。
- **OpenMoji**：Emoji / 公共符号。

## Brand
- **品牌官网 / Press Kit**：第一优先。
- **Simple Icons**：品牌 SVG 补充；仍需核对最新品牌规范与具体许可。

## Animated Icon / Morph
- **AnimateIcons**：优先检索现成 animated SVG/icon；若可用 MCP/CLI，优先程序化检索。
- **svg-animated-icons**：Agent 友好的 animated icon 目录。
- **Morphicons**：stroke icon 之间 path morph；适合状态 A→B，避免手画中间形态。

## 3D
- **3dicons**：通用 3D icon / render。
- **OpenSource3DAssets**：结构化 3D registry；优先 JSON / metadata + direct model。
- **Kenney**：可复用 2D / 3D / UI / game assets。

## HDRI / Texture / Material
- **Poly Haven**：HDRI / Texture / Model。
- **ambientCG**：PBR Texture / Material。

## Font
- 当前 Style Pack / 项目批准字体优先。
- **Fontsource / Google Fonts**：需要新增字体时再查；中文覆盖、字重和授权单独核对。

## 选择规则
1. 当前项目已有 > 用户提供 > 官方 > 结构化开源库 > 其他授权清晰来源。
2. 同一镜头尽量统一一个 icon family，不把不同默认线宽和端点风格直接混贴。
3. 只记录本任务实际采用和备选的 1–3 个结果，不把整个目录输出给用户。
4. 下载后必须本地化，并把来源 / LICENSE / 修改范围写入素材记录。
