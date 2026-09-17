# Research Assistant Suite · ChatGPT/Codex

> 中文是預設閱讀版本；English 可在本頁下方展開。

## 這個 repository 是什麼

這是 `research-assistant-suite-chatgpt` 的獨立 public GitHub repository marketplace。
它是一個 self-contained research workflow plugin：使用者只要安裝這一個 plugin，
就能從研究方法判讀、文獻搜尋與證據擷取，一路完成到 evidence-aware next action。

這個 repository 的 `main` 同時是 GitHub source 與 repository marketplace 的發布分支。
GitHub repository marketplace、ChatGPT workspace marketplace、Codex repository marketplace
與 OpenAI universal Plugins Directory 是不同的 discovery surfaces；推到 `main` 不會
自動代表已發布到官方 universal directory。

## 內含的 3 個 workflows

這是一個 plugin，不是三個需要分開安裝的 plugin。bundle 內含三個可被 host 發現的
skill IDs：

| 內部 workflow | Skill id | 責任 |
|---|---|---|
| 綜合入口 | `research-assistant-suite` | 路由、contract translation、證據到決策的 synthesis |
| 研究方法 | `scientific-research-guide` | 研究階段、方法、實驗設計、統計、V&V、UQ |
| 文獻服務 | `literature-search-extract` | 學術搜尋、定向擷取、citation traceability |

完整 workflow 通常從 `research-assistant-suite` 開始；若使用者只需要其中一條 lane，
也可以直接點名同一個 plugin 裡的 `scientific-research-guide` 或
`literature-search-extract`。不需要再安裝兩個 standalone companion plugins。

## 這個 plugin 做什麼

1. 定位研究階段、研究決策、方法限制與證據缺口。
2. 將缺口轉成有界線的 literature request。
3. 找到並擷取帶有 citation locator、access level、gaps、confidence 與 search trail
   的證據。
4. 依 decision question 合併方法學 framing 與文獻結果。
5. 輸出可辯護的 next action、限制、衝突與仍需人工確認的地方。

它保留兩個原始能力的責任邊界：研究方法 skill 不會取代來源搜尋的 citation
verification；文獻 skill 也不會單靠 citation count 替使用者決定研究方法。

## 不做什麼

- 不把 citation-only request 強行變成研究階段診斷。
- 不把單純方法問題強行變成 literature sweep。
- 不包含 credentials、私人 research corpus、Claude hooks、local services 或工作站設定。
- 不替使用者執行未明確要求的 code、data、experiment、file edit 或研究決策。
- 不會因為某個來源無法存取，就以記憶補造 citation、數值或方法結論。

## Package 結構

~~~text
.
├── .agents/plugins/marketplace.json
├── LICENSE
├── PRIVACY.md
├── SECURITY.md
├── SUPPORT.md
├── TERMS.md
└── plugins/research-assistant-suite-chatgpt/
    ├── plugin.json                  # portable Agent Plugins manifest
    ├── .codex-plugin/plugin.json    # Codex compatibility fallback
    ├── LICENSE
    ├── README.md
    ├── SHARE-NOTES.md
    └── skills/
        ├── research-assistant-suite/
        │   ├── SKILL.md
        │   └── references/orchestration-contract.md
        ├── scientific-research-guide/
        │   ├── SKILL.md
        │   ├── domains/
        │   └── references/
        └── literature-search-extract/
            ├── SKILL.md
            ├── connectors/
            ├── verify/
            ├── loop/
            ├── references/
            └── scripts/
~~~

OpenAI 的 portable package 以 plugin root 的 `plugin.json` 為入口；`.codex-plugin/plugin.json`
保留作 Codex compatibility fallback，`skills/` 放置可發現的 workflows。參考
[OpenAI — Package your plugin](https://developers.openai.com/plugins/build/plugins)。

`.agents/plugins/marketplace.json` 是 repository marketplace catalog，不是 plugin payload；
它只負責把 marketplace 指向 `plugins/research-assistant-suite-chatgpt/`。

## 如何安裝與使用

### A. 從 ChatGPT/Codex universal Plugins Directory 安裝

這條路徑要等 plugin 完成 OpenAI 官方送審、審查與發布後才會生效。發布後的使用方式：

1. 在 ChatGPT 的 Plugins/Apps 目錄搜尋 `Research Assistant Suite`。
2. 確認 publisher 與 repository 是 `WizerdBaChe/research-assistant-suite-chatgpt`。
3. 選擇 **Install**。
4. 開一個新的 chat，直接描述研究需求；需要時點名其中一個 skill id。

目前 GitHub `main` 不等於 universal Plugins Directory 已發布。官方送審流程見
[OpenAI — Submit and publish plugins](https://developers.openai.com/plugins/deploy/submission)。

### B. 從 ChatGPT workspace 的 repository marketplace 安裝

這是 workspace 管理員可用的 repository 分發方式，不等同於官方 universal directory。
不同 workspace 版本的 UI 可能把入口標成 **Plugins → Marketplaces** 或
**Plugins → Add → Import marketplace**。

1. 管理員開啟 **Workspace settings → Plugins → Marketplaces**。
2. 匯入 GitHub repository：`WizerdBaChe/research-assistant-suite-chatgpt`。
3. 選擇 `main` branch；若畫面要求路徑，使用 `.agents/plugins`。
4. 在 `research-assistant-suite-chatgpt` marketplace 中安裝
   **Research Assistant Suite · ChatGPT/Codex**。
5. 開一個新的 web chat 測試。

如果一般個人 ChatGPT account 看不到 repository marketplace，通常是帳號或 workspace
surface 的限制，不代表 repository layout 失效；可改用官方 directory（發布後）或
個別 `.skill` upload。

### C. 在 Codex 安裝 repository marketplace

在 PowerShell 執行：

~~~powershell
codex plugin marketplace add WizerdBaChe/research-assistant-suite-chatgpt --ref main --sparse .agents/plugins --sparse plugins
codex plugin add research-assistant-suite-chatgpt@research-assistant-suite-chatgpt
codex plugin list
~~~

預期會看到 `research-assistant-suite-chatgpt` 為 installed、enabled，版本為目前
package manifest 的版本。

若是從本機 checkout 測試 repository marketplace，可在 repository root 執行：

~~~powershell
codex plugin marketplace add 'D:\path\to\research-assistant-suite-chatgpt' --json
codex plugin add research-assistant-suite-chatgpt@research-assistant-suite-chatgpt
~~~

從 GitHub 更新時：

~~~powershell
codex plugin marketplace upgrade research-assistant-suite-chatgpt
codex plugin add research-assistant-suite-chatgpt@research-assistant-suite-chatgpt
codex plugin list
~~~

完成安裝或更新後重開 Codex，並用新的 task/thread 測試，讓 bundled skills 被重新發現。

### D. 在個人 ChatGPT Web 上傳單一 skill

個人 ChatGPT Web 的 upload UI 通常是「skill upload」，不是 repository marketplace import。
這個 full bundle 可依三個 top-level workflow 各自建立一個 `.skill` archive，但這三個
archive 是同一個 plugin 的獨立 upload 入口，不代表要在 marketplace 安裝三個 plugins。

在 repository root 的 PowerShell 建立三個 upload archives：

~~~powershell
$repo = (Get-Location).Path
$skillsRoot = Join-Path $repo 'plugins/research-assistant-suite-chatgpt/skills'
$out = Join-Path $repo 'skill-upload-bundles'
New-Item -ItemType Directory -Force -Path $out | Out-Null

Get-ChildItem -LiteralPath $skillsRoot -Directory | ForEach-Object {
    $zip = Join-Path $out ($_.Name + '.skill')
    Compress-Archive -LiteralPath $_.FullName -DestinationPath $zip -CompressionLevel Optimal -Force
}
~~~

每個 archive 都要保留 `<skill-name>/SKILL.md` 作為 archive root。建立後在 ChatGPT
開啟 **Skills → Create → Upload from computer**，一次上傳一個 `.skill`，再開新的 chat
測試。`skill-upload-bundles/` 是 local ignored build output，不會被推到 GitHub。

若要送交 plugin submission portal，請建立一個完整的 plugin archive；archive root
必須直接包含 `plugin.json`、`.codex-plugin/plugin.json`、`LICENSE` 與 `skills/`，不要把
整個 repository（包括 `.git/`、local runs 或 private material）直接壓縮。送審前請依
[OpenAI — Package your plugin](https://developers.openai.com/plugins/build/plugins) 與
[OpenAI — Submit and publish plugins](https://developers.openai.com/plugins/deploy/submission)
的要求檢查 package。

## 安裝後怎麼用

不用輸入特殊 command。直接描述工作即可，例如：

- 「請用 `research-assistant-suite` 先定位我的研究階段，再找有 citation locator 的文獻，最後給我下一步 V&V 計畫。」
- 「請用 `literature-search-extract` 找 2020–2025 年 silicon photonics edge coupler 的文獻，保留 DOI、table、figure 或 section locator。」
- 「請用 `scientific-research-guide` 根據資料型態、樣本數與假設比較統計方法，不要替我捏造結果。」

如果 plugin 沒有自動選到正確 workflow，直接在訊息中點名 skill id 即可。所有 workflows
都要求在缺少證據時明確說明，不應假設可以讀取使用者的本機資料。

## 與 standalone packages 的關係

本 bundle 內的兩份 companion payload 是經審查後納入的 release snapshots：

- [scientific-research-guide-chatgpt](https://github.com/WizerdBaChe/scientific-research-guide-chatgpt)
- [literature-search-chatgpt](https://github.com/WizerdBaChe/literature-search-chatgpt)

這兩個 standalone repository 仍可獨立安裝，適合只需要單一能力的情況；但如果使用
full bundle，請不要再同時安裝 standalone copies。相同 skill IDs 同時存在於多個 plugin
時，可能造成 duplicate discovery 或 routing ambiguity。

Bundle 內的 snapshots 不會隨 standalone repository 自動更新；每次同步都應更新 bundle
版本、`SHARE-NOTES.md` provenance 與相容性驗證。

## Runtime 與資料邊界

這是 instructions + reviewed helper payloads 的 self-contained package，不提供遠端服務。
完整 evidence lane 仍需要 host 提供 web search、page-fetch 或使用者提供的來源；bundled
skill 需要的 local evidence-run scripts 只在使用者明確要求且 host 允許時寫入
project-local 路徑。

如果 host 無法載入 literature lane，suite 會交付方法學 framing、精確的 evidence request
與 gap，但不會把記憶中的文獻主張包裝成 current evidence。如果 host 無法載入 methodology
lane，suite 會交付來源結果與限制，但不會擅自給出研究方法 verdict。

- [Privacy](PRIVACY.md)
- [Terms](TERMS.md)
- [Support](SUPPORT.md)
- [Security](SECURITY.md)
- [Package notes](plugins/research-assistant-suite-chatgpt/SHARE-NOTES.md)

## 常見問題

### 這是三個 plugin 嗎？

不是。這個 repository 發布一個 `research-assistant-suite-chatgpt` plugin，內部包含三個
skill IDs。完整工作流只安裝一個 plugin；只有在使用個人 Web 的 `.skill` upload surface
時，才會按需求選擇個別 workflow archive。

### GitHub 已經是 public，為什麼 universal directory 搜不到？

GitHub source、Codex repository marketplace、ChatGPT workspace marketplace 與官方 universal
Plugins Directory 是不同 discovery surfaces。GitHub `main` 只代表 source 與 repository
marketplace 可用，不代表官方 directory 已完成 submit、review、publish。

### 安裝後沒有看到新 skill，怎麼辦？

依序確認：

1. Codex marketplace 指向的是 `main`，不是舊的 local checkout。
2. `plugins/research-assistant-suite-chatgpt/plugin.json` 與
   `.codex-plugin/plugin.json` 都是有效 JSON。
3. `skills/` 下有三個 skill roots，且每個 root 都有 `SKILL.md`。
4. 安裝或更新後重開 Codex／ChatGPT，並使用新的 task 或 chat。
5. 沒有同時啟用 standalone copies，避免 duplicate skill IDs。

### 為什麼不是把 references 拆成更多 skills？

目前公開 package 明確包含 3 個 top-level workflows；`references/`、`domains/`、connectors
與 helper scripts 都是該 workflow 的 supporting content，不是額外的 top-level skills。

## 公開送審前 checklist

在 OpenAI submission form 上傳前，請確認：

- publisher identity 已驗證。
- license 已選定為 MIT，而且和兩份 plugin manifest、package `LICENSE` 與 repository root `LICENSE` 一致。
- 已準備 public website、support、privacy、terms URL。
- 已準備 category、starter prompts 與 package description。
- 已準備至少 5 個 positive test cases 與 3 個 negative test cases。
- package 不含 secrets、私人路徑、local-only integration、credentials 或不必要的權限。
- archive root 直接包含 plugin payload，不含 `.git/`、local runs、cache 或 private corpus。

## License

MIT。root 與 plugin package 各自附有同一份 [LICENSE](LICENSE)。

<details>
<summary>English</summary>

### What this repository is

This is the standalone public GitHub repository marketplace for
`research-assistant-suite-chatgpt`. It is a self-contained ChatGPT/Codex plugin with one
orchestration entry point and two reviewed companion capability payloads.

The repository `main` branch is both the GitHub source and the repository-marketplace release
branch. GitHub source, ChatGPT workspace marketplaces, Codex repository marketplaces, and the
OpenAI universal Plugins Directory are separate discovery surfaces. A push to `main` does not
automatically publish the plugin to the universal directory.

### The 3 included workflows

- `research-assistant-suite`: combined routing, contract translation, and evidence-to-decision synthesis.
- `scientific-research-guide`: methodology, study design, statistics, V&V, and uncertainty analysis.
- `literature-search-extract`: scholarly search, targeted extraction, source locators, and evidence traceability.

Install one plugin. The three workflow IDs are independently discoverable inside that plugin;
they are not three plugins that must be installed separately.

### Package layout

~~~text
plugins/research-assistant-suite-chatgpt/
├── plugin.json                  # portable Agent Plugins manifest
├── .codex-plugin/plugin.json    # Codex compatibility fallback
├── LICENSE
└── skills/                      # 3 independently discoverable workflows
~~~

The portable package uses the root `plugin.json`; `.codex-plugin/plugin.json` remains a Codex
compatibility fallback. The repository marketplace catalog is
`.agents/plugins/marketplace.json`. See
[OpenAI — Package your plugin](https://developers.openai.com/plugins/build/plugins).

### Install from the universal Plugins Directory

This route becomes available only after official submission, review, and publication:

1. Search for `Research Assistant Suite`.
2. Confirm the publisher and repository.
3. Select **Install**.
4. Start a new chat and describe the research task, or name a workflow explicitly.

The GitHub `main` branch is not the same thing as a published universal-directory listing. See
[OpenAI — Submit and publish plugins](https://developers.openai.com/plugins/deploy/submission).

### Install from a ChatGPT workspace repository marketplace

1. Open **Workspace settings → Plugins → Marketplaces** as a workspace administrator.
2. Import `WizerdBaChe/research-assistant-suite-chatgpt`.
3. Select the `main` branch and `.agents/plugins` marketplace path when requested.
4. Install **Research Assistant Suite · ChatGPT/Codex**.
5. Start a new web chat to test it.

### Install in Codex

Run in PowerShell:

~~~powershell
codex plugin marketplace add WizerdBaChe/research-assistant-suite-chatgpt --ref main --sparse .agents/plugins --sparse plugins
codex plugin add research-assistant-suite-chatgpt@research-assistant-suite-chatgpt
codex plugin list
~~~

After repository updates:

~~~powershell
codex plugin marketplace upgrade research-assistant-suite-chatgpt
codex plugin add research-assistant-suite-chatgpt@research-assistant-suite-chatgpt
~~~

Restart Codex and use a new task/thread so the updated skills are loaded.

### Upload one skill to personal ChatGPT Web

Individual ChatGPT Web upload is a skill-upload path, not a repository marketplace import.
This bundle has three top-level workflows, so it can produce three independent `.skill` archives:

1. Use `plugins/research-assistant-suite-chatgpt/skills/<skill-name>/`.
2. Keep `<skill-name>/` as the archive root.
3. Keep `SKILL.md` at the skill root.
4. Open **Skills → Create → Upload from computer**.
5. Upload one skill at a time.
6. Start a new chat and test it.

The ignored `skill-upload-bundles/` directory is a local build output and is not part of the
GitHub package. For official submission, build a complete plugin archive whose root contains
`plugin.json`, `.codex-plugin/plugin.json`, `LICENSE`, and `skills/`; do not archive the whole
repository with `.git/`, local runs, caches, or private material.

### Use the plugin after installation

Describe the work naturally, or name a workflow explicitly:

- `research-assistant-suite`
- `scientific-research-guide`
- `literature-search-extract`

The workflows must state when evidence is missing and must not assume access to a user's local
files. Do not co-install the standalone scientific or literature plugin copies with this full
bundle because the same skill IDs would be discovered more than once.

### Troubleshooting and public submission

GitHub source, Codex repository marketplaces, ChatGPT workspace marketplaces, and the universal
directory are separate surfaces. After installation or update, use a new task/chat. Keep the
archive root and `SKILL.md` locations correct, and exclude secrets, private paths, local-only
integrations, executables, caches, and private corpora.

Before submission, verify the publisher identity, MIT license consistency, public website/support/
privacy/terms URLs, category and starter prompts, at least five positive and three negative test
cases, and a clean archive boundary. See the official
[plugin packaging](https://developers.openai.com/plugins/build/plugins) and
[submission](https://developers.openai.com/plugins/deploy/submission) documentation.

</details>

## Official references

- [OpenAI — Package your plugin](https://developers.openai.com/plugins/build/plugins)
- [OpenAI — Submit and publish plugins](https://developers.openai.com/plugins/deploy/submission)
