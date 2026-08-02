## 如何節省 token / 用量

### 1. 複雜任務先規劃再實作

使用 Plan mode，提前確認：

- 釐清需求細節、極端案例
- 確認修改方式
- 影響範圍
- 比較不同方案

雖然會多一次對話增加token消耗，但能確認清楚，避免做完又推翻，來回試錯。

### 2. 固定的工作流程寫成工具執行

 - 寫成 script、CLI工具、MCP
   ex. 打包檔案(CICD)、建立基本的前端元件模板、新增後端資料欄位

### 3. 控制上下文範圍

- 只附加需要的檔案、段落
- 避免把無關的大段 log、文件、無用註解都塞進去 ex. `npx tsc --noEmit`型別檢查
- 新的任務應該要考慮開新的 session，避免不相關的上下文汙染、重複送出
  
- **AI 只看得到你給他的東西** ex.看不到程式功能管理的設定、共用代碼
  考慮讓AI有限度的存取資料庫

### 4. 把簡單的雜事交給子代理

- 用 subagent 做大範圍探索，主 Agent 只接收彙整結果、結論，省去過程
- 多一層溝通的 token 消耗
- 減少不必要的過程資訊累積在 context ，減少後續的 token 消耗，也保持 context 乾淨

### 5. 了解 prompt caching 的概念

- 部分平台或模型會對重複上下文有快取機制
- 快取 input 計價約為首次 input 的1/10
- 快取一般 5~10 分鐘不使用會清除，非尖峰時段時最多可保留 1 小時 (OpenAI)

#### 參考
[OpenAI 提示詞快取](https://developers.openai.com/api/docs/guides/prompt-caching)
[Github Copilot 模型計價](https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing#pricing-tables)

### 6. 任務過程盡量不要調整Harness、切換model

以 VS Code 為例，發送對話時時完整的訊息結構依序如下，越不容易被改變的部分放在越前面

1. System - **系統提示詞(包含當前的 model)**
2. Input Messages - **專案結構概述、先前對話紀錄**
3. Request Shape
4. Tools - **可用工具、MCP**
5. User Request - **本次請求**

### 7. 長時間任務要考慮做壓縮/交接，而不是同一個對話用到底

**較長的上下文，收費可能提高**，參考 [Github Copilot 模型計價](https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing#pricing-tables)  
  #### 做法
- `/compact` 總結當前的對話紀錄，破壞快取，但會節省後續對話的 token 消耗
- 或考慮讓當前 session 產出一份「當前狀態 + 決策 + 待辦事項」的交接文件，交接給新的 session
  *(或者工作過程中就持續更新這份文件)*
#### 額外考慮
- 提示詞快取可能過期的時機，ex. 中午午休

### 8. 根據任務複雜度選擇適合的model、thinking effort

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