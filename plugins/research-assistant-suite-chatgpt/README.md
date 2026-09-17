# Research Assistant Suite · ChatGPT/Codex

這個目錄是 research-assistant-suite-chatgpt plugin 的 package root，也是 self-contained
bundle 的發行邊界。

它提供一個 research-assistant-suite entry skill，將兩個獨立 capability 接成一次
使用者請求可完成的研究工作流：

- scientific-research-guide：研究階段、方法選擇、實驗設計、統計、V&V、UQ 與
  研究決策的 methodological framing。
- literature-search-extract：學術來源搜尋、定向擷取、access tag、citation
  locator、support check、confidence、gaps 與 search trail。

這兩份 companion payload 已經完整放在同一個 plugin 的 sibling skill directories；
使用者只需要安裝這一個 plugin，平常呼叫 research-assistant-suite 即可。

## Runtime contract

Suite 先產生 methodology framing，再將 evidence_questions 與 verification_needs
轉成 literature service request，最後依 decision question 合併兩個 result contract。
詳細欄位與 worked envelope 見
[orchestration-contract.md](skills/research-assistant-suite/references/orchestration-contract.md)。

這個 package 不宣稱未經 host schema 確認的 cross-plugin hard dependency，因為兩個
companion 已經是 bundle 內的實際 payload。若 host 無法載入其中一個 bundled
capability，SKILL.md 要求回傳 capability status 與 partial result，不得把未驗證
主張當成已完成的 lane。

若使用者已經安裝 standalone scientific-research-guide-chatgpt 或
literature-search-chatgpt，啟用 full bundle 時應停用或移除 standalone copy，以免
相同 skill IDs 被多個 plugin 同時發現。

## Scope

- Combined request：研究決策 + 文獻/證據任務。
- Pure literature request：交給 literature-search-extract。
- Pure methodology request：交給 scientific-research-guide。
- User retains the research decision; unrequested code/data/file action remains out of scope.

## Files

- [SKILL.md](skills/research-assistant-suite/SKILL.md)：orchestration routing and operating protocol。
- [orchestration-contract.md](skills/research-assistant-suite/references/orchestration-contract.md)：request/result/join schemas。
- [scientific-research-guide/SKILL.md](skills/scientific-research-guide/SKILL.md)：bundled methodology capability。
- [literature-search-extract/SKILL.md](skills/literature-search-extract/SKILL.md)：bundled literature capability。
- [SHARE-NOTES.md](SHARE-NOTES.md)：package provenance、相容性與驗證紀錄。

MIT license。見 [package LICENSE](LICENSE) 與 repository root 的 privacy、terms、support
與 security 文件。
