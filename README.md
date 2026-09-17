# Research Assistant Suite · ChatGPT/Codex

> 中文是預設閱讀版本；English 可在本頁下方展開。

這是 research-assistant-suite-chatgpt 的獨立 public GitHub repository marketplace。
它是一個 self-contained research workflow plugin：使用者只要安裝這一個 plugin，
就能從研究方法判讀、文獻搜尋與證據擷取，一路完成到 evidence-aware next action。

## 一次安裝，三個內部能力

這個 bundle 內含三個 skill：

| 內部能力 | Skill id | 責任 |
|---|---|---|
| 綜合入口 | research-assistant-suite | 路由、contract translation、證據到決策的 synthesis |
| 研究方法 | scientific-research-guide | 研究階段、方法、實驗設計、統計、V&V、UQ |
| 文獻服務 | literature-search-extract | 學術搜尋、定向擷取、citation traceability |

平常只需要選擇或呼叫 research-assistant-suite。它會在同一個 plugin 內調度另外
兩個 bundled capability；不需要再安裝或手動呼叫另外兩個 standalone plugin。

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

## 與 standalone packages 的關係

本 bundle 內的兩份 companion payload 是經審查後納入的 release snapshots：

- scientific-research-guide-chatgpt：
  https://github.com/WizerdBaChe/scientific-research-guide-chatgpt
- literature-search-chatgpt：
  https://github.com/WizerdBaChe/literature-search-chatgpt

這兩個 standalone repository 仍可獨立安裝，適合只需要單一能力的情況；但如果
使用 full bundle，請不要再同時安裝 standalone copies。相同 skill IDs 同時存在
於多個 plugin 時，可能造成 duplicate discovery 或 routing ambiguity。

Bundle 內的 snapshots 不會隨 standalone repository 自動更新；每次同步都應更新
bundle 版本、SHARE-NOTES provenance 與相容性驗證。

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
            ├── loop/
            ├── references/
            ├── scripts/
            └── verify/
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

匯入後只安裝 Research Assistant Suite · ChatGPT/Codex，再開一個新的 chat。使用時
可按 + → More 選 suite，或直接以 @ 選取它。

## Codex

只需要加入這一個 repository marketplace：

~~~powershell
codex plugin marketplace add WizerdBaChe/research-assistant-suite-chatgpt --ref main --sparse .agents/plugins --sparse plugins
codex plugin add research-assistant-suite-chatgpt@research-assistant-suite-chatgpt
codex plugin list
~~~

更新時：

~~~powershell
codex plugin marketplace upgrade research-assistant-suite-chatgpt
codex plugin add research-assistant-suite-chatgpt@research-assistant-suite-chatgpt
~~~

完成後重新開啟 Codex，並用新的 task/thread 測試，讓 bundled skills 被重新發現。

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

如果只需要單一能力，也可以明確指定 bundled skill id：

- 只找論文、查 DOI、抽 Methods 或整理 evidence table → literature-search-extract
- 只設計實驗、選統計檢定、做 V&V 或判斷研究下一步 → scientific-research-guide

## Runtime 與資料邊界

這是 instructions + reviewed helper payloads 的 self-contained package，不提供遠端
服務。完整 evidence lane 仍需要 host 提供 web search/page-fetch 或使用者提供的
來源；bundled skill 需要的 local evidence-run scripts 只在使用者明確要求且 host
允許時寫入 project-local 路徑。

如果 host 無法載入 literature lane，suite 會交付方法學 framing、精確的 evidence
request 與 gap，但不會把記憶中的文獻主張包裝成 current evidence。如果 host 無法
載入 methodology lane，suite 會交付來源結果與限制，但不會擅自給出研究方法 verdict。

- [Privacy](PRIVACY.md)
- [Terms](TERMS.md)
- [Support](SUPPORT.md)
- [Security](SECURITY.md)

## License

MIT。root 與 plugin package 各自附有同一份 [LICENSE](LICENSE)。

<details>
<summary>English</summary>

### What this repository is

This is the standalone public repository marketplace for research-assistant-suite-chatgpt.
It is a self-contained bundle with one user-facing orchestration entry point and the
reviewed scientific-research-guide and literature-search-extract capability payloads.

### Installation model

Install this plugin only for the complete workflow. The two standalone repositories remain
available for single-capability installations, but co-installing them with the bundle can
create duplicate skill IDs and ambiguous routing.

The suite frames the study decision, translates evidence gaps into a bounded literature
request, consumes traceable findings, and returns a decision map with source locators,
access levels, confidence, conflicts, gaps, and verification status. It does not invent
citations, results, or methodological certainty.

See the Chinese sections above for ChatGPT Web, Codex, usage, runtime, privacy, support,
and snapshot-update guidance.

</details>
