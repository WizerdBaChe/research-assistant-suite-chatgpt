# Research Assistant Suite · ChatGPT/Codex

這個目錄是 research-assistant-suite-chatgpt plugin 的 package root。

它提供一個 research-assistant-suite entry skill，將兩個獨立 capability 接成一次
使用者請求可完成的研究工作流：

- scientific-research-guide：研究階段、方法選擇、實驗設計、統計、V&V、UQ 與
  研究決策的 methodological framing。
- literature-search-extract：學術來源搜尋、定向擷取、access tag、citation
  locator、support check、confidence、gaps 與 search trail。

## Runtime contract

Suite 先產生 methodology framing，再將 evidence_questions 與 verification_needs
轉成 literature service request，最後依 decision question 合併兩個 result contract。
詳細欄位與 worked envelope 見
[orchestration-contract.md](skills/research-assistant-suite/references/orchestration-contract.md)。

這個 package 不複製兩個 companion 的 payload，也不在 manifest 宣稱未經 host schema
確認的 cross-plugin hard dependency。完整工作流需要另外安裝：

- scientific-research-guide-chatgpt
- literature-search-chatgpt

若其中一個不可用，SKILL.md 要求回傳 capability status 與 partial result，不得把
未驗證主張當成已完成的 lane。

## Scope

- Combined request：研究決策 + 文獻/證據任務。
- Pure literature request：交給 literature-search-extract。
- Pure methodology request：交給 scientific-research-guide。
- User retains the research decision; unrequested code/data/file action remains out of scope.

## Files

- [SKILL.md](skills/research-assistant-suite/SKILL.md)：orchestration routing and operating protocol。
- [orchestration-contract.md](skills/research-assistant-suite/references/orchestration-contract.md)：request/result/join schemas。
- [SHARE-NOTES.md](SHARE-NOTES.md)：package provenance、相容性與驗證紀錄。

MIT license。見 [package LICENSE](LICENSE) 與 repository root 的 privacy、terms、support
與 security 文件。
