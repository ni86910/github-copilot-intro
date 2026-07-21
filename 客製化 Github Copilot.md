## 背景知識補充：從 Prompt Engineering 到 Harness Engineering

### 1. Prompt Engineering
- 問題怎麼問
- 要求怎麼寫
- 輸出格式怎麼定

### 2. Context Engineering
- 給哪些檔案
- 給哪些規範
- 留下哪些歷史
- 怎麼避免上下文污染

### 3. Harness Engineering
- 包含:工具清單、回饋機制、循環工作能力、instructions、agents、skills、hooks、MCP...等
- 重點不再是單次的 prompt，而是有沒有搭建利於AI工作的環境，讓AI可靠的完成任務。

#### 參考
[Learn Harness Engineering 課程](https://walkinglabs.github.io/learn-harness-engineering/zh-TW/)

---
## 資料夾結構

<details open>
<summary>📁 <strong>.github</strong></summary>
<ul style="list-style-type: none;">
  <li>📄 <code>copilot-instructions.md</code></li>
  <li>
    <details open>
    <summary>📁 <strong>agents</strong></summary>
    <ul style="list-style-type: none;">
      <li>📄 <code>Project Code Reviewer.agent.md</code></li>
      <li>📄 <code>Run with code review.agent.md</code></li>
    </ul>
    </details>
  </li>
  <li>
    <details open>
    <summary>📁 <strong>hooks</strong></summary>
    <ul style="list-style-type: none;">
      <li>🧩 <code>format.json</code></li>
      <li>🧩 <code>audit.json</code></li>
    </ul>
    </details>
  </li>
  <li>
    <details open>
    <summary>📁 <strong>scripts(非制式規定)</strong></summary>
    <ul style="list-style-type: none;">
      <li>⚙️ <code>format-after-edit.ps1</code></li>
      <li>⚙️ <code>log-tool-use.ps1</code></li>
    </ul>
    </details>
  </li>
  <li>
    <details open>
    <summary>📁 <strong>instructions</strong></summary>
    <ul style="list-style-type: none;">
      <li>📄 <code>html.instructions.md</code></li>
      <li>📄 <code>prompt.instructions.md</code></li>
    </ul>
    </details>
  </li>
  <li>
    <details open>
    <summary>📁 <strong>prompts</strong></summary>
    <ul style="list-style-type: none;">
      <li>📄 <code>cv-detail-template-markdown-to-sql.prompt.md</code></li>
      <li>📄 <code>error-fix.prompt.md</code></li>
      <li>📄 <code>refactor.prompt.md</code></li>
    </ul>
    </details>
  </li>
  <li>
    <details open>
    <summary>📁 <strong>skills</strong></summary>
    <ul style="list-style-type: none;">
      <li>
        <details open>
        <summary>📁 <strong>table2</strong></summary>
        <ul style="list-style-type: none;">
          <li>📄 <code>SKILL.md</code></li>
        </ul>
        </details>
      </li>
      <li>
        <details open>
        <summary>📁 <strong>frontend-design</strong></summary>
        <ul style="list-style-type: none;">
          <li>📄 <code>SKILL.md</code></li>
        </ul>
        </details>
      </li>
    </ul>
    </details>
  </li>
</ul>
</details>

---

## 客製化規範與強化能力

### 參考資源

[awesome-copilot](https://github.com/github/awesome-copilot)
[everything-claude-code 介紹](https://github.com/affaan-m/ecc)

### 1. Instructions

特點: 根據 applyTo 設定，在AI讀取特定路徑時注入

```yml
applyTo: 'AppData/data/*.cs'
```

- 全域的 `.github/copilot-instructions.md`
- `.github/instructions/**/*.instructions.md`
- `AGENTS.md`

適合：

- 告訴 Copilot 專案的 coding style
- 告訴它潛規則、命名規範、資料夾慣例
- 可用於個人專屬的提示詞 ex. `.github/instructions/personal/*.instructions.md`

### 2. Agents

用途：

- 定義特定角色
- 設定特定工具權限
- 指定模型
- 固定工作方式

例如：

- **Plan agent**：只負責規劃，不直接改碼
- **Code Reviewer**：偏重找風險、找缺陷
- **Test Engineer agent**：產生測試

這可以幫團隊把常見工作流程標準化。
規劃 agent -> 實作 agent -> review agent -> test engineer

### 3. Skills

特點: 漸進式載入，根據 description 設定，由AI判斷跟當前任務相關時載入，也可以手動透過斜線命令引用 ex.`/do-something`

```yml
description: 專案內建的 Table2 元件的使用指南。使用 Angular Material Table 實作的共用表格。當需要顯示列表資料時使用此元件。
```

適合：

- 封裝可重複使用的工作流程
- 可包含多種類的資源 ，如 文字提示、resources、scripts、doc...等
- 可由 AI 視需求決定是否載入
- 適合高頻、固定步驟任務

例如：

- `create-standard-component`
- `use-table2`

#### 參考資源

[skills介紹文章](https://kaochenlong.com/claude-code-skills)

### 4. Prompts

用途：

- 重複使用常見提示詞，不用每次再重新打一次
- 可用斜線命令引用 ex.`/do-something`

### 5. Hooks

特點: 穩定觸發

用途：

- 在特定生命週期自動觸發命令、或注入額外的提示
- 做安全控制、格式化、提醒、紀錄、稽核

例如：

- `preToolUse` 阻止危險指令
- `postToolUse` 在修改後自動執行 formatter 或特定檢查

#### 參考
[VSCode文件範例](https://vscode.com.tw/docs/copilot/customization/hooks#_usage-scenarios)

### 6. MCP Servers

用途：

- 讓 Copilot 能跟外部系統用標準方式互動
- 不只讀本機檔案，也能連接 外部文件系統、其他 API

例子：

- GitHub MCP Server
- 內部知識庫
- issue / ticket / 規格系統

注意：
- MCP Server 會在對話一開始時就將完整的溝通格式注入，裝太多用不到的會造成 context 佔用

### 7. Plugins

用途：

- 把 Agents、Skills、Hooks、MCP 等能力打包，一鍵安裝
- 降低團隊複製設定的成本
- 有助於組織規模化推廣

---

## Harness 維護

### 應該要隨 model 能力演進而調整

- 從給予限制、定義步驟 ---> 到補充背景脈絡、定義完成標準

#### 參考
[OpenAi GPT-5.5 遷移指南](https://developers.openai.com/api/docs/guides/latest-model?model=gpt-5.5)
[Claude Code 團隊 減少提示詞](https://www.bnext.com.tw/article/91455/anthropic-engineer-claude-harness)