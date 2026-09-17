# Research Assistant Suite orchestration contract

This contract is the seam between the two bundled companion skills and the suite
coordinator. The full package carries those skills under sibling directories, while this
document defines the one-entry routing and join behavior. Field names are intentionally
close to the existing service contracts so the coordinator can pass returns without an ad
hoc translation layer.

## Ownership

| Field group | Owner | Coordinator responsibility |
|---|---|---|
| stage, completed, missing, next, risks | scientific-research-guide | Preserve and relate to the decision |
| method_guidance, verification_needs | scientific-research-guide | Convert into bounded evidence questions |
| findings, sources, gaps, confidence | literature-search-extract | Keep locators, access levels, and conflicts |
| search_trail, run_id | literature-search-extract | Include in the verification record |
| decision_map, next_action, review_when | research-assistant-suite | Derive transparently from both returns |

The coordinator must not overwrite a companion field with a stronger claim. If the
returns disagree, preserve both values and surface the conflict.

## Combined request envelope

~~~
request:
  purpose: "Decide whether the planned measurement is justified and what to run next."
  research_question: "Which measurement design best distinguishes A from B under C?"
  current_state:
    completed: ["pilot measurement", "initial calibration"]
    available_inputs: ["sample type", "instrument limit", "pilot summary"]
    constraints: ["non-destructive measurement first"]
  desired_decision: "Select the next measurement and its validation checks."
  domain: "user-supplied field label or unknown"
  evidence_need:
    - "Find primary studies comparing the candidate measurement designs."
    - "Extract sample conditions, validation method, and failure limits."
  source_scope:
    source_types: ["papers", "standards"]
    date_range: "user-supplied or none"
    venue_or_author: "user-supplied or none"
    known_seeds: []
    exclusions: []
  requested_output: "evidence-aware next-step decision"
  depth: "standard"
  language: "Traditional Chinese"
~~~

The coordinator should ask at most one question when a missing field changes the search
scope or the methodological decision. It may default presentation details but must not
guess a material, population, model scale, destructive-measurement order, or competing
priority.

## Methodology result

~~~
methodology:
  status: "available"
  stage: "Tier 2 / study design"
  completed: []
  missing:
    - "predefine the primary endpoint"
    - "specify the independent validation route"
  next:
    - action: "compare candidate designs against the endpoint and validation constraint"
      rationale: "the current design cannot distinguish the stated alternatives"
  risks:
    - "a convenient proxy may not establish the target property"
  method_guidance:
    - method: "candidate design A"
      fit_criteria: ["matches endpoint", "preserves sample for validation"]
      disqualifiers: ["violates sample or instrument constraint"]
  evidence_questions:
    - id: "EQ-1"
      question: "Which primary studies validate candidate design A against the target endpoint?"
      extraction_targets:
        - "sample and measurement conditions"
        - "independent validation method"
        - "reported failure limits"
  verification_needs:
    - "current standard or authoritative method guidance, if applicable"
~~~

## Literature service result

~~~
literature:
  status: "partial"
  findings:
    - evidence_question_id: "EQ-1"
      claim: "The candidate has been compared under conditions X and Y."
      support:
        - source_key: "S1"
          locator: "Methods, Section 2.3"
          access_level: "[full]"
      evidence_class: "directly reported"
      confidence: "supported by 2 independent sources"
      conflicts: []
      implication: "The comparison is relevant only when condition X is met."
  sources:
    - key: "S1"
      citation: "Author et al. (2024)"
      identifier: "DOI or other resolvable identifier"
      access_level: "[full]"
      locators_used: ["Methods, Section 2.3", "Table 1"]
  gaps:
    - "No accessible primary source established performance under constraint Z."
  confidence:
    - claim_or_question: "EQ-1"
      assessment: "partial"
      basis: "two sources; one condition is not represented"
      conflicts: []
  search_trail:
    queries: ["query recorded by the literature service"]
    tools_or_databases: ["host-provided search route"]
    boundary: "standard depth; stopped after target coverage and saturation"
  run_id: "evidence-run id when persisted"
~~~

The access tags [full], [partial], [abstract], and [secondary] retain the literature
skill's semantics. A source with [abstract] access cannot support a claim about unobserved
Methods, Results, conditions, or limitations.

## Decision join

The coordinator produces a decision_map rather than flattening all prose into a single
confidence score:

~~~
decision_map:
  - decision: "Select candidate design A for the next run."
    methodological_basis:
      - "matches the primary endpoint"
    supporting_evidence:
      - evidence_question_id: "EQ-1"
        source_keys: ["S1"]
        locators: ["Methods, Section 2.3", "Table 1"]
    limiting_evidence:
      - "constraint Z is not covered by an accessible primary study"
    inference:
      statement: "A is currently the most defensible pilot choice."
      basis: "fit criteria plus the bounded evidence return"
      not_claimed: "general superiority outside the searched conditions"
    user_confirmation_needed:
      - "confirm whether sample preservation outranks throughput"
    status: "provisional"
~~~

Allowed decision statuses:

- supported — the stated action is supported within the documented scope;
- provisional — the action is the best current option but a material gap remains;
- contested — sources or methodological criteria conflict;
- not_supported — the evidence does not support the proposed action;
- blocked — a required companion, input, or verification route is unavailable.

## Final return envelope

~~~
result:
  synthesis_status: "complete | partial | blocked"
  methodology_status: "available | unavailable | partial"
  literature_status: "available | unavailable | partial"
  verdict: "one-sentence current decision"
  decision_map: []
  next_action:
    action: "smallest next action"
    prerequisite: "what must be true first"
    expected_value: "uncertainty or decision risk reduced"
    review_when: "condition that reopens the upstream decision"
  gaps_and_conflicts: []
  verification_record:
    source_keys: []
    run_id: "..."
    source_access_boundary: "..."
    human_checks_remaining: []
~~~

The result must remain useful when either service is partial. A partial result states what
was completed, what is missing, and which conclusion is therefore not yet justified.
