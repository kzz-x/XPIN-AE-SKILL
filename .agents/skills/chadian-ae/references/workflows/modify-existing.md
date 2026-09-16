# Workflow｜Modify Existing Project

用于当前 AEP 的局部修改。

## Read Before Write
修改前必须读取：
- Project / 文件名
- 目标 Comp
- 目标 Layer
- 关键 Property
- Parent / Precomp
- Expression
- Effects
- Marker
- AI_ID / Comment / ROLE
- 与目标直接相关的控制器

不要为了“理解全部工程”扫描所有无关内容。

## Targeting
优先：
1. AI_ID
2. 明确 Comp + Layer Name
3. ROLE / TYPE / Comment
4. Source / Parent / Property 特征
5. Layer Index 只作为最后辅助手段

“这个 / 当前 / 选中的”
→ 实时读取 AE selection。

## Patch First
只改完成请求所需的属性或子模块。
保护：
- 用户手工关键帧
- 位置 / 比例
- 调色
- Mask
- Expressions
- Effects
- 素材替换
- 命名
- Parenting
除非请求本身需要修改。

## Rebuild Gate
只有在现有结构已经无法满足请求、局部 Patch 会更危险时，才允许重建较大模块。
重建前说明会影响什么。

## Verify After Write
修改后重新读取目标：
- 数值是否正确
- Keyframe 是否保留
- Expression 是否报错
- Parent 是否变化
- 是否产生重复层
- 是否误伤其他内容

小修改默认不需要静帧流程；视觉修改较大时才输出关键帧截图。
