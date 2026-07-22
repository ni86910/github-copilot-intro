根據官方 [GitHub Copilot hooks reference](https://docs.github.com/en/copilot/reference/hooks-reference) 文件，針對 **Copilot CLI / VS Code** 使用情境整理如下：

## 生命週期鉤子總表（CLI / VS Code 適用）

| 鉤子名稱                    | 觸發時機                       | 可取得的主要參數 / 資訊                                                                                     | 可執行的動作                                                                          | 備註                                          |
| ----------------------- | -------------------------- | ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- | ------------------------------------------- |
| `sessionStart`          | 新對話或恢復對話開始時                | `sessionId`、`timestamp`、`cwd`、`source` (`startup`/`resume`/`new`)、`initialPrompt`                 | 執行 script、注入 `additionalContext`                                                | 新互動對話才會觸發 `prompt` 類型                       |
| `sessionEnd`            | 對話結束時                      | `sessionId`、`timestamp`、`cwd`、`reason` (`complete`/`error`/`abort`/`timeout`/`user_exit`)         | 執行 script（通知性質）                                                                 | 無法影響結束行為                                    |
| `userPromptSubmitted`   | **User 送出訊息時**             | `sessionId`、`timestamp`、`cwd`、**`prompt`（使用者本次輸入）**                                               | 執行 script（通知性質）                                                                 | 可記錄/稽核，無法修改                                 |
| `userPromptTransformed` | 使用者訊息轉換成 model 可見內容後、寫入歷史前 | `sessionId`、`timestamp`、`cwd`、`prompt`、**`transformedPrompt`**                                    | 執行 script、回傳 `modifiedTransformedPrompt` 改寫 model 看到的內容                         | 不影響 timeline 顯示                             |
| `preToolUse`            | **每次 tool 執行前**            | `sessionId`、`timestamp`、`cwd`、**`toolName`（即將執行的工具）**、**`toolArgs`（即將執行的參數/指令）**                  | 執行 script、回傳 `permissionDecision` (`allow`/`deny`/`ask`)、回傳 `modifiedArgs` 修改參數 | `deny` 會阻止 tool 執行；exit 2 或非零都會阻擋           |
| `postToolUse`           | Tool 成功執行後                 | `sessionId`、`timestamp`、`cwd`、`toolName`、`toolArgs`、**`toolResult.textResultForLlm`**             | 執行 script、回傳 `modifiedResult` 替換結果、回傳 `additionalContext` 追加提示                  | 可改變 model 看到的執行結果                           |
| `postToolUseFailure`    | Tool 執行失敗後                 | `sessionId`、`timestamp`、`cwd`、`toolName`、`toolArgs`、**`error`**                                   | 執行 script、exit code `2` 回傳 `additionalContext` 提供恢復建議                           | 常見於錯誤處理/重試提示                                |
| `agentStop`             | 主 agent 完成一輪回應時            | `sessionId`、`timestamp`、`cwd`、`transcriptPath`、`stopReason`、`stop_hook_active`                    | 執行 script、回傳 `decision` (`block`/`allow`)、`reason`                              | `block` 強制再執行一輪；連續 8 次會被中斷                  |
| `subagentStart`         | 子 agent 被建立前               | `sessionId`、`timestamp`、`cwd`、`transcriptPath`、`agentName`、`agentDisplayName`、`agentDescription`  | 執行 script、注入 `additionalContext` 給子 agent                                       | `general-purpose` agent 不觸發                 |
| `subagentStop`          | 子 agent 完成時                | `sessionId`、`timestamp`、`cwd`、`transcriptPath`、`agentName`、`agentDisplayName`、`stopReason`        | 執行 script、回傳 `decision` (`block`/`allow`)、`reason`                              | 同 `agentStop` 可強制繼續                         |
| `errorOccurred`         | 執行過程發生錯誤時                  | `sessionId`、`timestamp`、`cwd`、**`error`** (`message`/`name`/`stack`)、`errorContext`、`recoverable` | 執行 script（通知性質）                                                                 | 無法阻止錯誤                                      |
| `preCompact`            | 上下文壓縮（手動/自動）即將開始前          | `sessionId`、`timestamp`、`cwd`、`transcriptPath`、`trigger` (`manual`/`auto`)、`customInstructions`   | 執行 script（通知性質）                                                                 | 可用 `matcher` 過濾 trigger                     |
| `permissionRequest`     | 權限服務詢問前（規則引擎、使用者確認之前）      | `toolName`、`toolArgs` 等權限請求上下文                                                                    | 執行 script、回傳 `behavior` (`allow`/`deny`)、`message`、`interrupt`                  | CLI pipe 模式特別有用；`interrupt: true` 可停止 agent |
| `notification`          | CLI 發出系統通知時（非同步）           | `sessionId`、`timestamp`、`cwd`、`message`、`title`、**`notification_type`**                           | 執行 script、回傳 `additionalContext` 注入為使用者訊息                                       | fire-and-forget，不會阻塞                        |

## 重點摘要

| 使用目的 | 建議鉤子 |
|---|---|
| 取得 User 本次送出的訊息 | `userPromptSubmitted` 的 `prompt` |
| 改寫 model 看到的內容 | `userPromptTransformed` 的 `modifiedTransformedPrompt` |
| 攔截/批准/修改即將執行的 tool | `preToolUse` 的 `permissionDecision` / `modifiedArgs` |
| 修改 tool 成功後的回傳結果 | `postToolUse` 的 `modifiedResult` / `additionalContext` |
| 強制 agent 繼續思考不回覆 | `agentStop` / `subagentStop` 的 `decision: "block"` |
| 在對話開始注入系統提示 | `sessionStart` 的 `additionalContext` 或 `prompt` 類型 |
| 記錄/稽核 tool 使用 | `preToolUse` / `postToolUse` |
| 系統通知自動處理 | `notification` 的 `additionalContext` |