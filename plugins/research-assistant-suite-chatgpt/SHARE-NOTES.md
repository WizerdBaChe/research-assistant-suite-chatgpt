# ChatGPT/Codex package notes

## Boundary

This is the public distribution package for a coordination-only OpenAI ChatGPT/Codex
plugin. It is deliberately separate from both standalone companion repositories and
does not vendor either companion skill payload.

The entry skill is research-assistant-suite. The companion skill IDs it addresses are
scientific-research-guide and literature-search-extract.

## Source and design basis

- The methodology lane follows the standalone scientific-research-guide-chatgpt
  package's existing five-gate advisory boundary and its Mode 2 literature delegation.
- The literature lane follows the standalone literature-search-chatgpt package's
  request/result contract: purpose, question, source_types, scope, output_format, depth,
  language → findings, sources, gaps, confidence, search_trail, run_id.
- The suite adds only routing, contract translation, capability-status reporting, and
  decision-question synthesis.

## Portability decisions

- Kept one public trigger for the combined workflow to avoid two independent descriptions
  being selected without an explicit orchestration path.
- Kept pure-literature and pure-methodology requests out of the suite trigger boundary.
- Used a thin coordinator instead of copying both payloads; the standalone packages remain
  the canonical owners of their respective rules.
- Did not add an unverified cross-plugin dependency field to either manifest. Companion
  availability is documented and exposed in the return envelope.
- Added degraded-operation rules for missing, partial, or blocked companion lanes.
- Kept the final output traceable by decision question, source key, locator, access level,
  confidence, conflict, gap, and search trail.

## Package contents

- Two manifests: portable root plugin.json and .codex-plugin/plugin.json fallback.
- One repo-local marketplace entry.
- One orchestration skill and one field-level contract reference.
- Root and package-level MIT license files plus privacy, terms, support, and security pages.

## Verification record

2026-09-18 local package checks:

- quick_validate.py: PASS.
- validate_plugin.py: PASS.
- read_marketplace_name.py: PASS; the repo-local marketplace name matches the plugin name.
- Portable/Codex manifest consistency: PASS.
- Markdown local-link closure: PASS (14 links checked).
- Packaging prescan: CLEAN (regex pass; manual review remains required).
- Public-boundary scan: CLEAN.
- Payload audit: PASS; only the coordinator skill and contract are shipped, with no
  companion payload copy.
- Companion runtime invocation and user acceptance: not established by static package checks.

The package has not been installed into the user's personal Codex marketplace and has not
been pushed to GitHub in this task.
