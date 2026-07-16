## 如何節省 token / 用量

### 1. 複雜任務先規劃再實作

使用 Plan mode，可以先把：

- 改哪些檔案
- 怎麼驗證
- 有哪些風險
- 哪個方案比較好

雖然會多一次對話增加token消耗，但能確認清楚，避免做完又推翻，來回試錯。

### 2. 固定的工作流程寫成工具執行

 - 寫成 script、CLI工具、MCP，ex. 打包上傳、建置、建立基本的前端元件、新增後端資料欄位

### 3. 控制上下文範圍

- 只附加需要的檔案、段落
- 不要把無關的大段 log、文件、無用註解都塞進去 ex. `npx tsc --noEmit`、AppDataName IsRequired 等設定
- AI 只看得到你給他的東西 ex.看不到程式功能管理的設定、共用代碼(MCP)
- 新的任務應該要考慮開新的 session，避免不相關的上下文汙染、重複送出

### 4. 把簡單的雜事交給子代理

- 用 subagent 做大範圍探索、研究，主 Agent 只接收彙整結果、結論，省去過程
- 多一層溝通的 token 消耗
- 減少不必要的過程資訊累積在 context ，減少後續的 token 消耗，也保持 context 乾淨

### 5. 了解 prompt caching 的概念

- 部分平台或模型會對重複上下文有快取機制
- 快取 input 計價約為首次 input 的1/10
- 快取一般 5~10 分鐘不使用會清除，非尖峰時段時最多可保留 1 小時 (OpenAI)

#### 參考
[OpenAI 提示詞快取](https://developers.openai.com/api/docs/guides/prompt-caching)
[OpenAI 模型計價](https://developers.openai.com/api/docs/pricing)

### 6. 任務過程盡量不要調整Harness、切換model

以 VS Code 為例，發送對話時時完整的訊息結構依序如下，越不容易被改變的部分放在越前面

1. System - **系統提示詞(包含當前的 model)**
2. Input Messages - **專案結構概述、先前對話紀錄**
3. Request Shape
4. Tools - **可用工具、MCP**
5. User Request - **本次請求**

### 7. 長時間任務要考慮做交接/壓縮，而不是同一個對話用到底

- `/compact` 總結當前的對話紀錄，破壞快取，但會節省後續對話的 token 消耗
- 或考慮讓當前 session 產出一份「當前狀態 + 決策 + 待辦 + 風險」的交接文件，交接給新的 session
- 若提示詞可能過期，可考慮總結一份交接文件

### 8. 根據任務複雜度選擇適合的model

Anthropic
- 旗艦 Opus、Fable
- 中等 Sonnet
- 輕量 Haiku

OpenAI
- 旗艦 GPT-5.6 Sol (GPT-5.5)
- 中等 GPT-5.6 Terra (GPT-5.4)
- 輕量 GPT-5.6 Luna (GPT-5.4-mini)

或是 Auto 自動選擇
- 根據任務複雜度、模型 API 繁忙情況

---