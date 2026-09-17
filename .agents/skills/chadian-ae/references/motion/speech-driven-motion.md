# Motion｜Speech-Driven Motion

> 仅在用户要求“按照口播 / 旁白 / 音频节奏制作动画”、需要从已剪辑音视频中自动找 Motion Beat，或需要把语音时间映射到当前 AE 时间线时加载。

核心原则：

**Timeline Truth > Original Transcript Timing.**  
**Marker First > Speech Assist > Manual Override.**

语音识别用于第一次自动找点；最终动画时序优先落到当前合成 Marker，不长期直接依赖 transcript timestamp。

---

## 1｜优先级：先看 Marker，不要先跑 ASR

开始前先读取当前目标合成与相关音视频层的 Marker。

### A｜Marker First
如果用户已经在时间线上打了足够的 Motion Beat，例如：
```text
人
系统
循环
100%
```
或：
```text
动画开始
切系统
强调100%
结束
```

→ 直接使用这些 Marker。  
→ 不做语音识别，不分析整条源文件。  
→ 用户 Marker 是最高优先级，不覆盖、不擅自移动。

### B｜Speech Assist
只有 Marker 不足、用户明确要求自动理解口播，或需要自动找节奏点时，才提取当前时间线实际使用的音频做 ASR / 节奏分析。

### C｜Manual Override
自动分析后，把有价值的语义点写成 Comp Marker。用户后续移动 / 删除 / 修改 Marker 后，以新的 Marker 为准，不再次用旧 transcript 强行覆盖。

---

## 2｜禁止直接处理整条大型 MP4

口播源文件可能是长时间、大体积 MP4 / MOV。

默认禁止：
- 为识别几秒口播而读取 / 解码整条大型视频；
- 为语音分析完整导出视频；
- 在 AE Render Queue 中为了 ASR 渲染整段；
- 把完整视频复制成另一个巨大代理；
- 已知当前使用区间时仍对整个源文件跑转写。

正确路线：
```text
AE 当前剪辑状态
→ source.file + used source ranges
→ ffmpeg / 本地音频工具只抽取实际使用区间
→ 临时 speech proxy
→ ASR / 节奏分析
→ Source Time 映射回 Comp Time
→ 写 Comp Marker
```

Speech proxy 优先轻量语音格式，例如：
```text
mono
16 kHz 或 24 kHz
WAV / 其他稳定本地音频代理
```

目标是语音识别稳定，不是音质交付。无需为了体积强制转 MP3。

临时代理仅用于分析；任务结束后可清理，除非用户要求保留用于复核。

---

## 3｜只分析当前时间线真正使用的内容

对相关音频 / 视频 Layer 读取：
- `source` / `source.file`
- `inPoint`
- `outPoint`
- `startTime`
- `stretch`
- `timeRemapEnabled`
- Time Remap 属性（若启用）
- Parent / Precomp 时间关系（若相关）

同一大源文件被 Split 成多个 Layer 时，分别计算每段实际 Source Range；可批量抽取这些区间，不转写被剪掉的 NG、废话和未使用部分。

不要仅凭素材文件时长或原始 transcript 猜当前剪辑位置。

---

## 4｜时间映射规则

### 普通 Trim / Split / Slip
无 Stretch、无 Time Remap 时，使用当前 Layer 的真实时间关系把：
```text
Source Time ↔ Layer / Comp Time
```
准确互换。

通常可由 `startTime + inPoint/outPoint` 关系推导当前实际使用区间；以 AE 读取到的真实属性为准，不硬编码固定公式到所有情况。

### Time Stretch
存在 `stretch` 时，必须把倍率纳入 Source ↔ Comp 换算。

不要把 80% / 120% Stretch 当作 1:1 时间。

### Time Remap
启用 Time Remap 时，禁止只用 `startTime` 做线性换算。

应读取真实 Time Remap 属性，在需要的合成时间点求对应 Source Time。倒放、冻结、快慢变化都以 Time Remap 曲线为准。

### Nested Precomp
口播若位于预合成内部，而动画在外层主合成：
- 先获得内层语音时间；
- 再按 Precomp 的 start / stretch / remap 等关系递归映射到目标 Comp；
- 最终 Marker 写到真正驱动动画的目标合成。

若无法可靠解析复杂嵌套时间关系，不要假装准确；退回用户 Marker / 提供轻量口播代理的路线。

---

## 5｜ASR 不是逐字动画器

语音识别可提供：
- sentence timestamps
- word timestamps
- 停顿
- 重音 / 音量变化（工具可用时）
- 语义内容

但 Motion Beat 应先做语义聚类：

```text
“以前 / 是 / 人 / 找 / 零件”
→ [人找零件] 一个 Primary Beat
```

不要每个词都打一个 Marker，也不要逐字机械卡动画。

优先提取：
- 语义转折：以前 / 现在 / 但是 / 所以
- 关键对象首次出现
- 数字 / 结论 / 强调词
- 动作谓词
- 明显停顿
- 信息段落切换

动画密度由 Motion Design 决定，不由词数决定。

---

## 6｜Marker 作为最终 Timing Contract

Speech Assist 得到的结果应优先转成当前目标 Comp 的 Marker，例如：
```text
AUTO_人找零件
AUTO_系统接管
AUTO_循环抓取
AUTO_正确率100%
```

也可以使用简短中文 Marker Comment；关键是语义清晰。

建议区分：
- 用户手工 Marker：保持原名，不覆盖；
- 自动 Marker：`AUTO_...`；
- 最终确认后的 Motion Marker：可去掉 `AUTO_` 或按工程现有命名规范整理。

动画控制应优先引用：
```text
Comp Marker → Motion Phase / Keyframes / Master Progress
```
而不是永久引用外部 transcript 文件。

这样用户改剪辑节奏时，可以通过移动 Marker 快速调整 Motion Timing。

---

## 7｜口播节奏如何影响 Motion

语音时间只提供约束，不要求动画始终同步移动。

根据语义与节奏选择：
- 重要名词出现前可做 anticipation；
- 语义落点处完成主动作；
- 重音可触发强调，但不要每个重音都放大；
- 停顿可作为 settle / hold / visual reset；
- 快速连续口播可压缩 secondary motion；
- 关键结论处可减少运动，让信息 Hold；
- 转折词可以作为场景状态切换点。

不要让整段动画从头到尾一直动。

需要复杂对象差异、Stagger、Camera 或共享控制时，继续按 `motion-principles.md`、`motion-profiles.md`、`motion-control-architecture.md` 执行。

---

## 8｜本地工具职责

AE / MCP 负责：
- 读取时间线真实状态；
- 定位 Layer / Source；
- 读取 / 写入 Marker；
- 制作和验证动画。

本地媒体 / ASR 工具负责：
- 从 `source.file` 抽取实际 used ranges；
- 生成临时 audio proxy；
- 转写与时间戳分析。

不要让 JSX 在运行时联网获取语音服务或素材。

如果本地不存在可用的音频抽取 / ASR 能力：
1. 已有 Marker → 直接按 Marker 做；
2. 否则提示用户打 Motion Marker；
3. 或请用户提供轻量口播音频 / transcript；
4. 不为了绕过限制去完整渲染大型视频。

---

## 9｜完成前检查

- 是否优先使用了用户已有 Marker；
- 是否只分析时间线实际使用的音频；
- 是否误处理了完整大型 MP4；
- Trim / Split / Slip / Stretch / Time Remap 的时间是否正确映射；
- 自动 Marker 是否与当前 Comp 实际口播对齐；
- 是否把逐字 timestamp 合理聚成 Motion Beat；
- 是否保护用户 Marker；
- 动画最终是否以可编辑 Marker / Motion System 为控制入口；
- 用户移动 Marker 后，是否能合理继续调整动画。

最终目标：

**让口播帮助 AI 找到“什么时候该动”，但让 Motion Designer 规则决定“怎么动、动多少、何时停”。**
