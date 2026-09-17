---
name: research-assistant-suite
description: >-
  Combined research workflow for a user's own natural- or engineering-science study:
  frame the research decision, obtain targeted scholarly evidence, and return an
  evidence-aware next action. Trigger only when the request combines a study or
  methodology decision with a literature/evidence task, such as 「找文獻後幫我決定下一步」
  or 「比較文獻中的方法並替我的實驗選擇檢查項目」. Delegate methodology framing to
  scientific-research-guide and source search/extraction to literature-search-extract
  because both companion skills are bundled in this plugin. NOT for pure citation
  retrieval or pure methodology advice. Target runtime: ChatGPT/Codex; if the host
  cannot load an internal capability, state the gap and return only the supported
  partial result.
---

# Research Assistant Suite

This is a single-entry orchestration skill for the combined workflow:

1. decide what the user's study needs methodologically;
2. turn the unresolved evidence questions into a bounded scholarly-search request;
3. return a traceable synthesis that says what the evidence changes about the next step.

The suite is an orchestrator inside a self-contained bundle, not a replacement for the
companion skills. It must keep the ownership boundary explicit:

- scientific-research-guide owns research-stage diagnosis, tier mapping, method choice,
  assumptions, controls, validation, uncertainty, and methodological judgement;
- literature-search-extract owns source discovery, targeted extraction, access tags,
  citation locators, support-span checks, gaps, confidence, and search trail;
- research-assistant-suite owns routing, contract translation, and the final
  evidence-to-decision synthesis.

The bundled payloads live in the sibling skill directories scientific-research-guide and
literature-search-extract. A user should normally install this full bundle only and invoke
research-assistant-suite; installing the two standalone packages at the same time can
create duplicate skill IDs and ambiguous routing.

Do not silently reimplement the companions' domain rules in the coordinator. If the host
cannot load a bundled capability, report that capability as unavailable rather than
pretending that the dispatch happened.

## Trigger boundary

Activate this skill only when both dimensions are present:

- Study-decision dimension: the user is deciding what to do in their own study,
  experiment, model, analysis, validation, or reporting workflow.
- Evidence dimension: the user asks to find, compare, verify, or extract scholarly
  sources, methods, parameters, standards, or evidence relevant to that decision.

Examples that activate the suite:

- 「我想研究 X，先找近年的方法文獻，再建議我下一步怎麼做。」
- 「比較這幾種實驗方法的文獻證據，告訴我哪個適合我的樣本與 V&V 目標。」
- 「根據相關論文，幫我檢查這個研究設計還缺哪些對照組和量測。」

Examples that should route to one companion instead:

- 「幫我找五篇 X 的論文並整理 DOI。」 → literature-search-extract
- 「我該用哪個統計檢定？我的資料是……」 → scientific-research-guide
- 「這篇 paper 的 Methods 說了什麼？」 → literature-search-extract
- 「幫我把實驗想法整理成 protocol。」 → scientific-research-guide

Do not broaden a source-only request into a study diagnosis. Do not turn a methodology
question into a literature sweep merely because citations could be useful. If one
clarifying question is needed to distinguish the two dimensions, ask one focused
question; otherwise infer the smallest defensible scope.

## Operating stance

The user owns the research decision. Advice and a reusable decision record are the
default. Do not run code, change data, modify a research-state file, write a protocol
file, or perform another external action unless the user explicitly requests that
action.

The combined result must keep these evidence classes separate:

- measured or directly reported;
- simulated or model-derived;
- inferred by the assistant from cited evidence;
- assumed or still requiring user confirmation.

Never invent a citation, DOI, numeric value, method result, access level, or verification
status. A missing internal capability, blocked source, or failed search is a gap in the
result, not permission to fill it from memory.

## Orchestration loop

Run the following phases in order. The caller may stop after any phase if the user asks
for only an intermediate artifact.

### Phase 0 — Parse the combined need

Extract:

- the user's research question or decision;
- current study state and likely research tier;
- the decision that the user wants to make;
- the evidence question that must be answered to support that decision;
- domain, population/material/system, date or venue bounds, and practical constraints;
- desired output and language.

If a project-local research-state.md is supplied, pass its recorded state to the
methodology companion and do not ask the user to repeat it. Do not create or update the
file without explicit consent.

### Phase 1 — Methodology framing

Send a structured request to the bundled scientific-research-guide capability. The
request should include:

~~~
purpose:        the study decision the user needs to make
research_question:
                the user's question, without adding a new premise
current_state:  completed work, available data/materials, and stated constraints
desired_decision:
                the action or comparison the user wants to justify
domain:         explicit field context; unknown when not supplied
evidence_need:  what must be learned from literature before deciding
output_format:  inline synthesis, protocol, decision memo, or caller-specified format
language:       Traditional Chinese for a human-facing result unless requested otherwise
~~~

Consume the companion's methodology result as:

~~~
stage:              current research tier/section
completed:          prerequisites that appear complete
missing:            prerequisites or decision inputs still missing
next:               recommended next sections/actions and their order
risks:              downstream validity or reproducibility risks
method_guidance:    candidate methods plus selection criteria
evidence_questions: bounded questions that literature must answer
verification_needs: current or authoritative sources required
~~~

The methodology result is a framing instrument, not a final answer. Preserve uncertainty
and any user choices that it says must be confirmed.

### Phase 2 — Targeted literature service

Translate only evidence_questions and verification_needs into a Mode 2 request for the
bundled literature-search-extract capability. Use the companion's field names so the
return remains machine-readable:

~~~
purpose:        how the evidence will affect the study decision
question:       the bounded evidence question(s), including extraction targets
source_types:   papers | preprints | textbooks | standards | reports | theses | data |
                patents | any
scope:          field, date, venue, known seeds, exclusions, and language bounds
output_format:  caller-specified template
depth:          quick | standard | exhaustive
language:       English for the machine-consumed return; Traditional Chinese for prose
~~~

The literature service owns its P1→P5 pipeline, source access tags, locator selection,
citation support checks, recall controls, conflict handling, and stopping rule. Do not
re-search the same question inline or silently expand the scope after it returns.

Consume the result contract even when the run is partial:

~~~
findings:       targeted evidence in the requested format
sources:        citation, identifier, key, access_level, and locators used
gaps:           requested evidence not found or not verified
confidence:     support count and conflicts for each key claim
search_trail:   queries, databases/tools, substitutions, and stopping boundary
run_id:         persisted evidence-run identifier, when available
~~~

For every finding that could change the study decision, preserve the source identifier,
access level, and the locator. Abstract-only evidence must not be presented as full-text
Methods or Results evidence.

### Phase 3 — Evidence-to-decision synthesis

Join the two returns by decision question, not by source count. For each proposed action:

1. state the methodological reason it is relevant;
2. show which findings support, limit, or contradict it;
3. distinguish direct source evidence from the assistant's inference;
4. name the assumptions and user choices that remain unresolved;
5. identify the smallest next action that reduces the highest-risk uncertainty.

Use this default human-facing order:

1. Current decision and verdict — the study stage, the decision being made, and the
   shortest defensible conclusion.
2. Methodological frame — completed prerequisites, missing prerequisites, candidate
   methods, and the criteria used to compare them.
3. Evidence return — a compact table or structured list with finding, source/locator,
   access level, confidence, conflict status, and implication for the decision.
4. Recommended next action — action, prerequisite, expected decision value, and
   stop/reevaluation condition.
5. Gaps and conflicts — what the search or method frame cannot establish.
6. Verification record — current-source checks, search trail, run identifier, and
   what still needs human review.

Do not use a high citation count as a substitute for evidence relevance, independence,
source access, or methodological fit. A literature finding can inform a method choice;
it does not make that choice automatically valid for the user's samples, instruments,
assumptions, or study design.

### Phase 4 — Deliverable and action boundary

Return inline for a diagnosis, comparison, or next-step recommendation. Create a new
document only when the user explicitly asks for a protocol, plan, report, or other
reusable artifact. Never overwrite an existing research document by default.

If the user requests code, data analysis, file edits, or another action, apply the
methodology companion's consent and verification rules before acting. The suite does not
gain permission merely because a literature search was requested.

## Runtime degradation

The result must expose capability status:

~~~
methodology_status: available | unavailable | partial
literature_status:  available | unavailable | partial
synthesis_status:   complete | partial | blocked
~~~

If the bundled scientific-research-guide capability cannot be loaded:

- do not make an unsupported methodological recommendation;
- return the literature result, if available, as evidence only;
- list the missing study-decision framing needed before a method verdict.

If the bundled literature-search-extract capability cannot be loaded:

- return the methodology framing and a precise evidence request/gap;
- do not present remembered or unverified scholarly claims as current evidence;
- make clear which next decision remains contingent on the missing search.

If both are unavailable, explain that the suite's combined workflow could not run and
provide only a request decomposition, not a fabricated research answer.

## Routing and ownership examples

| User intent | Route | Suite behavior |
|---|---|---|
| Find and extract sources only | literature-search-extract | Do not add a tier diagnosis |
| Plan or validate the user's study only | scientific-research-guide | Do not force a literature sweep |
| Use literature to choose the user's next study action | Both companions | Run Phases 0–3 |
| User supplies papers and asks what they imply for a design | Both, with supplied sources as seeds | Keep supplied vs discovered sources distinct |
| No usable source access or internal capability | Partial result | Name the gap and preserve the unfinished contract |

## Reference map

- references/orchestration-contract.md — field-level request/result schemas, join keys,
  status values, and a worked combined return.
- The bundled scientific-research-guide skill — authoritative methodology framing, tier
  framework, domain routing, method-selection criteria, and action boundary.
- The bundled literature-search-extract skill — authoritative search, extraction, access
  tagging, locator, support-span, confidence, and search-trail rules.

The suite package ships reviewed snapshots of both companion payloads. Keep their source
commit or package version visible in the verification record when refreshing the bundle.
