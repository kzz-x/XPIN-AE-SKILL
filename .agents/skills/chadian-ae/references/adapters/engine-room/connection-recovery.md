# Engine Room｜Connection Recovery

> **仅在连接 / 端口 / Bridge / Panel 异常时读取。正常任务禁止预读。**

## 0｜第一恢复动作：先恢复启动顺序

当前稳定生产规则：

1. 如果 Premiere Pro 已打开，**先关闭 PR**；
2. 确保 After Effects 已启动；
3. 让 Engine Room / AE MCP 先连接并确认可用；
4. 需要 PR 时再重新打开 Premiere Pro。

不要先反复改 7778、固定端口、重装或追查某个 CEP。只有恢复“AE 先、PR 后”仍异常，才继续下面诊断。

## 1｜典型端口失配

可能出现：

```text
bridgeReachable = Panel 在端口 Y 正常响应
portAgreement   = tool calls 仍指向旧端口 X
```

Engine Room 的初始 op port 通常来自：

```text
AE_MCP_PORT（若设置）
→ port file
→ 默认端口
```

connection refused / unreachable 可能触发重新发现；但如果旧端口 X 被“不是 AE MCP、却能正常响应 HTTP”的服务占用，请求可能变成 404 / 非预期 JSON / 普通 AeError，从而绕过 rediscovery。

在当前已验证环境中，先开 PR 会稳定增加这类冲突风险。底层占用者可能是 PR 带起的 CEP / 本地 HTTP 服务，不必为了完成任务先追查具体插件。

## 2｜诊断顺序

恢复正确启动顺序后仍异常：

1. 运行 `check_setup`；
2. 同时看 `bridgeReachable` 与 `portAgreement`；
3. Panel 在 Y、tool call 在 X → 按 port disagreement 处理；
4. 重新连接 / 重启 MCP Server，让它重新读取当前 port file；
5. 再次 `check_setup`；只有端口一致后继续写 AE。

Panel 已健康时，不要优先重启 AE。

## 3｜固定端口只作为长期方案

只有确认某个端口长期空闲时才考虑同时配置 Server 与 Panel。

Server：

```text
AE_MCP_PORT=<确认空闲的端口>
```

Panel（Windows）：

```text
%USERPROFILE%\.engineroom-ae-mcp\config.json
```

示例：

```json
{
  "port": 7799,
  "allowPortWalk": false
}
```

注意：

- `AE_MCP_PORT` 是 Server 硬 pin；
- Panel 的 `port` 是起始绑定端口；
- 如果 7799 被其他非 AE 服务占用，Panel 仍可能走到 7800，但 Server 被硬锁在 7799，反而制造永久失配。

因此：**pin 前先确认端口真的空闲。**

## 4｜快速判断

| 情况 | 处理 |
|---|---|
| PR 先开后 Engine Room 异常 | 先关 PR，AE / Engine Room 先恢复 |
| X connection refused，Panel 在 Y | 允许 rediscovery / 重连后复查 |
| X 有其他 HTTP 响应，Panel 在 Y | 重连 MCP Server，再查 portAgreement |
| `AE_MCP_PORT=X` 但 Panel 在 Y | 修改/取消 pin，再重连 |
| 无任何 Panel 响应 | 进入 Panel / AE setup |
| 长期重复冲突 | 确认空闲端口后再考虑固定 |

核心：**启动顺序优先于端口调试；`bridgeReachable` 只说明某个 Panel 活着，`portAgreement` 才说明 tool call 真会打到它。**
