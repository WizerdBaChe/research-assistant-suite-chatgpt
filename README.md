# Research Assistant Suite · ChatGPT/Codex

> 中文是預設閱讀版本；English 可在本頁下方展開。

這是 research-assistant-suite-chatgpt 的獨立 public GitHub repository marketplace。
它提供一個單一入口，將研究方法判讀與可追溯的學術文獻搜尋接成同一個工作流：

1. 先定位研究階段、研究決策、方法限制與證據缺口。
2. 將缺口轉成有界線的 literature request。
3. 找到並擷取帶有 citation locator、access level、gaps、confidence 與 search trail
   的證據。
4. 把證據接回研究決策，輸出可辯護的下一步與仍需人工確認的地方。

## 這個 plugin 做什麼

- 以一個 research-assistant-suite entry skill 接收「研究決策 + 文獻證據」的複合需求。
- 將方法學工作交給 scientific-research-guide companion skill。
- 將來源搜尋與定向擷取交給 literature-search-extract companion skill。
- 依 decision question 合併兩邊的結果，而不是以 citation count 代替方法判斷。
- 在結果中保留來源 identifier、access tag、section/table/figure/page locator、conflict、
  confidence、gaps 與 search trail。
- 在任一 companion 缺少、來源無法存取或搜尋部分失敗時，回傳明確的 partial result，
  不以記憶補造 citation、數值或方法結論。

這是一個 coordination package，不重新打包兩個 companion 的內容。兩個 standalone
plugin 仍各自維持自己的版本、測試與責任邊界。

## 不做什麼

- 不把 citation-only request 強行變成研究階段診斷。
- 不把單純方法問題強行變成 literature sweep。
- 不宣稱 plugin manifest 具有未經驗證的 cross-plugin hard dependency。
- 不包含 credentials、私人 research corpus、Claude hooks、local services 或工作站設定。
- 不替使用者執行未明確要求的 code、data、experiment、file edit 或研究決策。

## Companion plugins

複合工作流建議同時安裝以下兩個 companion：

| Capability | Plugin package | Skill id |
|---|---|---|
| 研究階段、方法、實驗設計、統計、V&V | scientific-research-guide-chatgpt | scientific-research-guide |
| 學術搜尋、定向擷取、citation traceability | literature-search-chatgpt | literature-search-extract |

目前的 manifest schema 沒有在本 package 內宣稱可直接解析的 cross-plugin dependency
欄位，因此 companion installation 是文件化的 runtime prerequisite，而不是未驗證的
manifest 宣告。若只載入本 suite，skill 仍會分解請求，但會把缺少的 lane 標成
unavailable，只交付有實際能力支援的 partial result。

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
    ├── plugin.json
    ├── .codex-plugin/plugin.json
    ├── LICENSE
    ├── README.md
    ├── SHARE-NOTES.md
    └── skills/research-assistant-suite/
        ├── SKILL.md
        └── references/orchestration-contract.md
~~~

Portable root plugin.json 是 package 的 canonical manifest；.codex-plugin/plugin.json
保留給 Codex compatibility fallback。.agents/plugins/marketplace.json 只負責把
marketplace 指向 plugins/research-assistant-suite-chatgpt/。

## ChatGPT Web

Workspace admin 在 ChatGPT web 開啟 **Workspace settings → Plugins → Add → Import marketplace**，
填入：

~~~text
Source: https://github.com/WizerdBaChe/research-assistant-suite-chatgpt
Path: .agents/plugins
Branch: main
~~~

匯入後安裝 Research Assistant Suite · ChatGPT/Codex，並另外安裝兩個 companion
plugin，再開一個新的 chat。使用時可按 + → More 選 suite，或直接以 @ 選取它。

GitHub repository marketplace、ChatGPT workspace marketplace 與 OpenAI universal Plugins
Directory 是不同的 discovery surface。GitHub source distribution 不會自動使 plugin
出現在 universal Directory；若要提交官方目錄，仍需依該目錄的 skills-only review
流程提供 publisher、support、privacy、terms、starter prompts 與測試案例。

## Codex

在 PowerShell 執行：

~~~powershell
codex plugin marketplace add WizerdBaChe/scientific-research-guide-chatgpt --ref main --sparse .agents/plugins --sparse plugins
codex plugin add scientific-research-guide-chatgpt@scientific-research-guide-chatgpt
codex plugin marketplace add WizerdBaChe/literature-search-chatgpt --ref main --sparse .agents/plugins --sparse plugins
codex plugin add literature-search-chatgpt@literature-search-chatgpt
codex plugin marketplace add WizerdBaChe/research-assistant-suite-chatgpt --ref main --sparse .agents/plugins --sparse plugins
codex plugin add research-assistant-suite-chatgpt@research-assistant-suite-chatgpt
codex plugin list
~~~

更新時，分別升級三個 local marketplace，再重新加入對應 plugin：

~~~powershell
codex plugin marketplace upgrade scientific-research-guide-chatgpt
codex plugin add scientific-research-guide-chatgpt@scientific-research-guide-chatgpt
codex plugin marketplace upgrade literature-search-chatgpt
codex plugin add literature-search-chatgpt@literature-search-chatgpt
codex plugin marketplace upgrade research-assistant-suite-chatgpt
codex plugin add research-assistant-suite-chatgpt@research-assistant-suite-chatgpt
~~~

完成後重新開啟 Codex，並用新的 task/thread 測試，讓新的 skill catalog 被重新發現。

## 使用範例

~~~text
我想研究 silicon photonics edge coupler。請先定位我目前在研究流程的哪一階段，
再找 2020–2025 年比較 fabrication method、coupling efficiency 與 bandwidth 的
主要文獻，最後根據來源條件建議我下一步的量測與 V&V 檢查。每個數值要保留
DOI 以及 table、figure 或 section locator；找不到的地方請列成 gap。
~~~

~~~text
比較這三種量測方法的原始文獻證據，並根據我的樣本限制與非破壞量測優先順序，
建議哪一個適合先做。不要把 abstract-only 證據當成完整 Methods 證據。
~~~

以下需求則應直接選 companion：

- 只要找論文、查 DOI、抽 Methods 或整理 evidence table → literature-search-extract
- 只要設計實驗、選統計檢定、做 V&V 或判斷研究下一步 → scientific-research-guide

## Runtime 與資料邊界

這是 instructions-only 的 coordination package，不提供遠端服務。Host 仍須提供
兩個 companion skill 與可用的 web search/page-fetch 或使用者提供的來源，才能完成
完整的 evidence lane。

如果 literature lane 不可用，suite 會交付方法學 framing、精確的 evidence request
與 gap，但不會把記憶中的文獻主張包裝成 current evidence。如果 methodology lane
不可用，suite 會交付來源結果與限制，但不會擅自給出研究方法 verdict。

- [Privacy](PRIVACY.md)
- [Terms](TERMS.md)
- [Support](SUPPORT.md)
- [Security](SECURITY.md)

## License

MIT。root 與 plugin package 各自附有同一份 [LICENSE](LICENSE)。

<details>
<summary>English</summary>

### What this repository is

This is the standalone public repository marketplace for
research-assistant-suite-chatgpt. It provides one orchestration entry point that connects
methodology-first study framing with evidence-traceable scholarly search and extraction.

### Companion boundary

The suite keeps scientific-research-guide responsible for research-stage diagnosis,
method selection, controls, validation, and uncertainty. It keeps literature-search-extract
responsible for source discovery, targeted extraction, access tags, locators, support
checks, gaps, confidence, and search trails. The suite joins the two returns by decision
question and exposes partial capability status.

The package does not duplicate either companion payload and does not declare an
unverified cross-plugin dependency in its manifest. Install the two companion packages
when the combined workflow is needed.

See the Chinese sections above for ChatGPT Web, Codex, usage, runtime, privacy, support,
and fallback guidance.

</details>
