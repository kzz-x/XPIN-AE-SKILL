# Engineering｜Assets & Replacement

## 来源优先级
1. 用户提供的真实素材
2. 官方品牌 / Press Kit / 产品资源
3. 授权清晰的开源 / 公共素材
4. AI / 外部工具生成的图片、视频、3D、纹理
5. AE 原生程序化制作
6. 高质量占位槽

公开可访问 ≠ 可商用。

## 复杂对象
产品 / 人物 / 工厂 / 车辆 / 机械 / 专业符号：
不要为了脚本方便低质量手绘。

## 下载职责
联网搜索 / 下载：
在 AE / JSX 之外先完成。
文件落地后再由 AE 导入。
不要让工程依赖 URL。

## 素材槽
例如：
```text
素材槽_01_钢板
素材槽_02_零件
```

槽内部保留：
- 替换层
- Cover / Contain
- Crop
- Mask / Matte
- 圆角 / Border
- 调色
- Motion
- Blur / DOF

用户只替换“替换这里”。

## 缺失
不能获取：
→ 可生成则生成
→ 否则高质量占位
→ 集中列出用户需要提供什么

命名：
`【待替换】工业机器人.glb`

## 来源记录
Comment 可记录：
```text
SOURCE_TYPE=
SOURCE_NAME=
AUTHOR=
LICENSE=
URL=
MODIFIED=
```
