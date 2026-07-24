## 背景知識補充：從 Prompt Engineering 到 Harness Engineering

### 1. Prompt Engineering
- 問題怎麼問
- 要求怎麼寫
- 輸出格式怎麼定

### 2. Context Engineering
- 給哪些檔案參考、規範
- 怎麼避免上下文污染

### 3. Harness Engineering
- 包含:工具清單、回饋機制、循環工作能力、instructions、agents、skills、hooks、MCP...等
- 重點不再是單次的 prompt，而是搭建一個讓 AI 能自主觀察、行動、驗證、修正的工作環境，讓 AI 可靠地完成多步驟任務。

#### 參考
[Learn Harness Engineering 課程](https://walkinglabs.github.io/learn-harness-engineering/zh-TW/)
[VSCode 客製化文件](https://code.visualstudio.com/learn/customizations/1-why-customization-matter)

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

### 1. Prompts

**注入時機**: 手動輸入斜線命令引用 `/do-something`

用途：
- 重複使用常見提示詞，不用每次再重新打一次

**refactor.prompt.md**
``` markdown
---
name: "refactor"
description: "將Angular元件遷移至18版寫法"
---
1. 將資料夾底下的元件改成standalone元件，原本所屬的module檔案中刪除相關引用
2. 將angular結構型指令 改成 angular 內建的流程控制語法
```
### 2. Instructions

**注入時機**: 根據 applyTo 設定，在AI讀取特定路徑時注入

``` markdown
---
description: 'Angular HTML 模板規則'
applyTo: '**/*.html'
---
- 優先使用 Bootstrap class，其次是 SCSS，最後才使用行內style。
- 在 Angular 模板中，創建或更新標記時，優先使用新的內置控制流語法，如 `@if` 和 `@for`，而不是 `*ngIf` 和 `*ngFor`。
```

適合：
- 說明專案的 coding style
- 說明潛規則、命名規範、資料夾慣例 `applyTo: 'AppData/data/*.cs'`

備註：
- 會遞迴搜尋 instructions 檔案，可用於資料夾管理 ex. `.github/instructions/**/*.instructions.md`

### 3. Agents

**注入時機**: 切換agent後對話開頭注入提示詞

用途：
- 專屬角色設定，讓 agent/subagent 只做一件事
- 設定一組工具權限

例如：
- **Plan agent**：只負責規劃，不直接改碼
- **Code Reviewer**：偏重找風險、找缺陷
- **Test Engineer**：產生測試

這可以幫團隊把常見工作流程標準化。
plan agent -> 實作 agent -> code reviewer -> test engineer

### 4. Skills

**注入時機**: 漸進式載入，根據 description 設定，由 AI 判斷跟當前任務相關時載入，也可以手動透過斜線命令引用 `/do-something`

``` markdown
---
name: table2

description: 專案內建的 Table2 元件的使用指南。使用 Angular Material Table 實作的共用表格。當需要顯示列表資料時使用此元件。
---
......
```

適合：
- 封裝可重複使用的工作流程、
- 可包含多種類的資源，如 文字提示、scripts、doc...等
- 可由 AI 視需求決定是否載入

例如：
- `create-standard-component`
- `use-table2`

備註：
- vscode 設定 chat.agentSkillsLocations
- 若要將 .github/skills 內入版控，可以考慮將個人 skills 放在 .agents/skills 底下
#### 參考資源

[skills介紹文章](https://kaochenlong.com/claude-code-skills)

### 5. Hooks

特點: 穩定觸發

用途：
- 在特定生命週期自動觸發命令、或注入額外的提示
- 做安全控制、格式化、提醒、紀錄、稽核

例如：
- `preToolUse` 阻止危險指令
- `postToolUse` 在修改後自動執行 formatter 或特定檢查

[[hook資訊整理]]
#### 參考
[VSCode文件範例](https://vscode.com.tw/docs/copilot/customization/hooks#_usage-scenarios)

### 6. MCP Servers

用途：
- 讓 Copilot 能跟外部系統用標準方式互動
- 不只讀本機檔案，也能連接 外部文件系統、其他 API

例子：
- GitHub MCP Server
- 內部知識庫
- mantis、Gitlab issue

注意：
- MCP Server 會在對話一開始時就將完整的溝通格式注入，裝太多用不到的會造成 context 佔用

### 7. Plugins

用途：
- 把 Agents、Skills、Hooks、MCP 等檔案打包，一鍵安裝
- 可用於建立一整套的工作流程
- 降低團隊複製設定的成本,利於推廣

---

## Harness 維護

### 應該要隨 model 能力演進而調整

- 從給予限制、詳細定義步驟 ---> 到補充背景脈絡、目標、成功標準

#### 參考
[OpenAi GPT-5.5 遷移指南](https://developers.openai.com/api/docs/guides/latest-model?model=gpt-5.5)
> **使用結果優先提示可以更有效地執行任務：** GPT-5.5 更擅長從明確的目標出發，遵循約束條件，並將產品意圖轉化為具體的後續步驟。描述預期結果、成功標準、允許的副作用、證據規則和輸出格式。除非具體步驟至關重要，否則避免提供詳細的流程指導。

[Claude Code 團隊 減少提示詞](https://www.bnext.com.tw/article/91455/anthropic-engineer-claude-harness)
>但到了最新一代，又倒回去。模型反而想要更短的提示，因為範例會綁住它的想像力，它其實比你給的範例更有想法。
>Shihipar 說，Claude Code 團隊近期把系統提示砍掉了大約八成，原則從「規定它不能做什麼」改成「給它足夠的背景」，少下禁令、多給脈絡。