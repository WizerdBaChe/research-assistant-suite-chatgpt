# ChatGPT/Codex package notes

## Boundary

This is the public distribution package for a self-contained OpenAI ChatGPT/Codex
research workflow bundle. It contains one orchestration skill plus reviewed snapshots of
the scientific-research-guide and literature-search-extract capability payloads.

The entry skill is research-assistant-suite. The bundled companion skill IDs are
scientific-research-guide and literature-search-extract.

## Source snapshots

- Methodology payload: the reviewed scientific-research-guide-chatgpt package snapshot,
  based on the share source anchor 7548f0e.
- Literature payload: the reviewed literature-search-chatgpt package at source commit
  29f902e.
- Orchestration payload: authored in this repository and joined through
  references/orchestration-contract.md.

The bundle carries 51 skill files: 2 orchestration files, 10 methodology files, and
39 literature-service files. Python bytecode, caches, private corpora, credentials,
filled domain profiles, and source-only runtime state were excluded.

## Portability decisions

- Kept one public suite trigger for the combined workflow.
- Kept pure-literature and pure-methodology routing available through the two bundled
  capability IDs without forcing a combined diagnosis.
- Converted the previous thin coordinator into a full bundle so the end user installs
  one plugin.
- Kept the standalone repositories as independent distribution variants and did not add
  an unverified cross-plugin dependency field to the bundle manifest.
- Warned against co-installing standalone copies with the bundle because the same skill IDs
  would be discovered more than once.
- Adapted bundled companion frontmatter, one illustrative directory path, and one blank
  list marker with trailing whitespace to the
  bundle layout; the remaining reviewed payload content is copied from the standalone
  packages.
- Kept degraded-operation rules for a host that fails to load one internal capability.
- Kept the final output traceable by decision question, source key, locator, access level,
  confidence, conflict, gap, and search trail.

## Package contents

- Two manifests: portable root plugin.json and .codex-plugin/plugin.json fallback.
- One repo-local marketplace entry.
- Three skill directories: research-assistant-suite, scientific-research-guide, and
  literature-search-extract.
- The complete literature helper/reference/test payload and the generic methodology
  reference/template payload.
- Root and package-level MIT license files plus privacy, terms, support, and security pages.

## Verification record

2026-09-18 local package checks after full-bundle conversion:

- quick_validate.py: PASS for all three bundled skills.
- validate_plugin.py: PASS.
- read_marketplace_name.py: PASS; the repo-local marketplace name matches the plugin name.
- Portable/Codex manifest consistency: PASS; version 0.2.0 and interface fields agree.
- Markdown local-link closure: PASS (16 links checked).
- Packaging prescan: CLEAN for all three bundled skills (regex pass; manual review still
  required).
- Payload copy audit: PASS; scientific 10 files with three intentional adapter deltas and
  literature 39 files with one intentional adapter delta.
- Literature helper tests: PASS (49 tests).
- Public-boundary scan: CLEAN.
- Runtime invocation and user acceptance: not established by static package checks.
- Repository marketplace README: PASS; documents universal-directory, ChatGPT workspace,
  Codex marketplace, and individual `.skill` upload surfaces, with the one-plugin/three-skill
  boundary kept explicit.
- Branch publication state: local and remote expose only the clean `main` branch.

The package has not been installed into the user's personal Codex marketplace. The full bundle
and repository marketplace guide were pushed to the public GitHub repository
https://github.com/WizerdBaChe/research-assistant-suite-chatgpt on 2026-09-18, with remote
default branch `main`. GitHub license detection is MIT License.
