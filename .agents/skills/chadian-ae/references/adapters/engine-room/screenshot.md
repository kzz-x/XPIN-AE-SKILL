# Engine Room｜Screenshot & Visual Check

> 仅在明显视觉变化需要静帧验收，或 screenshot 出现问题时读取。普通数值 Patch 不加载。

## 正常验收

不要持续逐帧截图。

优先一次代表性 Contact Sheet：

```text
screenshot_frame({compId, times:[t1,t2,t3,...]})
```

建议：

- 通常 3–6 个关键时间点；
- 使用合理 downsample；
- 不连续抓几十帧；
- 静态小 Patch 不截图；
- Screenshot 用于 Pose / Staging / continuity；连续 Motion 手感仍以 AE 前台预览为准。

完整视觉闭环见：
`../../quality/visual-feedback-loop.md`

## 截图实现边界

Raw `run_jsx` 中不要把 `comp.saveFrameToPng(...)` 当常规截图方案；宿主可能有异步写盘 / 对话框边界。

优先 Engine Room：
- `screenshot_frame`
- `screenshot_layer`

## 异常

遇到 screenshot stale / corrupt / timeout：

- 按 Engine Room 返回类型处理；
- 不通过隐藏 / 删除图层“修”截图；
- timeout 可能意味着请求仍在处理，不立即重复发送大量截图。
