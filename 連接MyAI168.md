[MyAI 168開發者文件](https://www.myai168.com/futaba/developer/)
## Copilot CLI

[Copilot CLI BYOK 文件](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/use-byok-models)

### 1. 在 copilot cli 登出 github
``` bash
# 開啟copilot cli
copilot
# 登出github
/logout
```

### 2. 設定環境變數

| 環境變數                        | 要設定的值                                                    |  必填 | 說明                                                                    |
| --------------------------- | -------------------------------------------------------- | --: | --------------------------------------------------------------------- |
| `COPILOT_PROVIDER_TYPE`     | `openai`                                                 |   否 | 提供者，預設值為`openai`，視情況替換成`anthropic`。                                   |
| `COPILOT_PROVIDER_BASE_URL` | `https://www.myai168.com/futaba/api/openai/v1/responses` |   是 | API 的基底端點。                                                            |
| `COPILOT_PROVIDER_API_KEY`  | `你的 API Key`                                             |   是 | 取得方式：開發者中心-取得API金鑰。                                                   |
| `COPILOT_MODEL`             | `gpt-5.6-terra`                                          |   是 | 要交由 Copilot CLI 使用的模型識別字，也可留空，執行時再指定 `copilot --model gpt-5.6-terra`。 |
### 3. 開啟copilot
``` bash
copilot
#或是指定模型
copilot --model gpt-5.6-terra
```

## VSCode
[VSCode BYOK文件](https://code.visualstudio.com/docs/agent-customization/language-models#_bring-your-own-language-model-key)


