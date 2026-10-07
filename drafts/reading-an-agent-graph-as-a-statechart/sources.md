# Sources & Handoff — reading-an-agent-graph-as-a-statechart

Everything the draft rests on, with citations, plus the council record and the pre-publish
checklist. Written so the author can edit the draft directly without losing provenance.

## Research citations (every figure in the draft traces here)

### Committed evidence files (contact-center-ai checkout, read 2026-10-06)
- `agent/graph.py` — the graph: `classify → retrieve | summarize_call | resolve_metric →
  ground → answer`; `resolve_metric` is the `analyst` subgraph; conditional edges off
  `classify` on `s["route"]`.
- `agent/subgraphs/analyst.py` — `disambiguate → clarify? → execute`; the only
  `interrupt()` in the graph; node restarts from the top on resume; MAX_REASKS = 2; the
  subgraph compiles without a checkpointer so it inherits the parent's and its interrupt
  surfaces in the parent run.
- `agent/terms.py` + `agent/data/ambiguous_terms.yml` — the ambiguous-term list is data,
  not code. Starter terms: `rate` (7 candidates, from `average_mortgage_note_rate` to
  `average_deposit_apy`, in YAML order), `balance` (10), `lcv`/`ltv` (2).
- `agent/checkpoint.py` — `AsyncPostgresSaver` on the Compose `db`, in a `langgraph` schema
  set via `search_path`; an interrupted run resumes by `thread_id`, even after a restart.
- `agent/state.py` — `AgentState` keys (question, route, term, candidates, metric_names,
  clarify_attempts, tool_output, sql, citations, grounded, answer).
- `docs/design/agent-graph.md` (D0.2, 2026-10-02) — the interrupt contract v1
  (`{"kind": "clarify_metric", ...}`, resume with `Command(resume={"choices": [...]})` on
  the same `thread_id`, detect via `result["__interrupt__"]`); versions checked against the
  LangGraph docs and PyPI on 2026-10-02 (langgraph 1.2.12); rejected: a tool-calling loop
  (route would be a model choice no deterministic eval can check) and `SqliteSaver`.
- `evals/README.md` (L2) — 51-question golden set; 11 ambiguous questions check the
  interrupt; offline run measures the graph's deterministic behavior (model decisions are
  scripted), only `make evals-live` measures the model; known failures g24/g25 (label-named
  candidates still interrupt on a qualifier tie) and g38 (case-sensitive call-id regex).
- `results/evals/README.md` — first full live run 2026-10-04 (`agent-golden-openai`):
  route/interrupt/metric/call_id 1.000, sql 0.000, judge 0.343 all / 0.159 ambiguous —
  the metric answers were MetricFlow errors (environment, not agent): a Databricks-built
  semantic manifest sent backtick-quoted SQL to Postgres. Two more environment defects
  followed (marts never built after a reseed; raw tables absent). Post-env-fix run
  2026-10-05: route/interrupt/metric/sql/call_id 1.000, citations 0.955, judge 0.824 all,
  ambiguous 0.932, interrupt 1.000 on all 11 ambiguous items. Both runs, as the results
  file records: hardware Intel Core i5-1038NG7 @ 2.00GHz, 8 cores, 15 GB RAM, Linux;
  versions python 3.14.7, agent model z-ai/glm-5.3-flash, judge anthropic/claude-opus-5.5
  (prompt v1), langgraph 1.2.12, langsmith 0.8.9, langchain-core 1.6.6; judge scaled
  (score - 1) / 4.
- `blogs/PROPOSED_POSTS.md` — post brief B3 (title, subtitle, thesis, outline,
  must-not-claim lines, the optional ECharts backstory paragraph).
- `tests/agent/test_interrupt.py` (committed in 733dded, 2026-10-01, the L1 ticket) — the
  interrupt unit tests, by name: `test_average_rate_interrupts_with_the_contract_payload`,
  `test_a_qualified_question_does_not_interrupt`,
  `test_a_phrase_that_only_contains_the_alias_does_not_interrupt` (the "unless-phrase"
  case), `test_resume_with_a_valid_choice`,
  `test_resume_with_an_invalid_choice_asks_again`,
  `test_malformed_resume_values_are_invalid` (parametrized),
  `test_after_two_re_asks_every_candidate_runs`, `test_resume_after_a_rebuild`,
  `test_two_ambiguous_terms_are_asked_one_at_a_time`.
  `tests/agent/test_routes.py` adds one test per route. (Added to the evidence set during
  council round 1; the interview panel had not seen it.)

### Local verification runs (this checkout is read-only; ran on a copy at b847341)
- 2026-10-06: `uv run pytest tests/agent -q` → **38 passed**. The boundary, rebuild and
  two-term tests exist and pass offline.
- 2026-10-06: `build_graph(get_llm=lambda: None, toolbox=stub)` then
  `g.get_graph().draw_mermaid()` (langgraph 1.2.12 from uv.lock) → the export shows
  conditional edges as dotted arrows with no guard labels, the `analyst` subgraph as one
  opaque node, and no representation of the `interrupt()`. Full output saved in the task
  records.

### Web research (public sources, accessed 2026-10-06)
- LangGraph docs, Interrupts: https://docs.langchain.com/oss/python/langgraph/interrupts —
  `interrupt()` surfaces a JSON-serializable value; the graph state is saved via the
  persistence layer and waits indefinitely; resume with `Command(resume=...)`; the node
  restarts from the top on resume, so pre-interrupt side effects must be idempotent.
- LangGraph docs, Persistence: https://docs.langchain.com/oss/python/langgraph/persistence —
  checkpointers save per super-step keyed by `thread_id`; `MemorySaver` does not survive a
  restart, `PostgresSaver` does.
- LangGraph docs, Graph API: https://docs.langchain.com/oss/python/langgraph/graph-api —
  StateGraph = shared state schema + reducers; nodes, fixed and conditional edges; compiled
  graphs as subgraph nodes; `Command` = `update`/`goto`/`graph`/`resume`.
- W3C SCXML Recommendation (2015-09-01): https://www.w3.org/TR/scxml/ — state-chart notation
  based on Harel State Tables; `<parallel>` (orthogonal regions), `<history>` states,
  transitions guarded by `cond` attributes; compound states make transitions move between
  sets of active states; transition `type="internal"` vs external decides whether a
  self-transition exits and re-enters its source state.
- Wikipedia, "State diagram" (Harel statechart): https://en.wikipedia.org/wiki/State_diagram#Harel_statechart —
  Harel statecharts add hierarchically nested states, orthogonal regions, state and
  transition actions to classic state diagrams.
- ECharts project page: https://sourceforge.net/projects/echarts/ — "a state machine-based
  programming language for event-driven systems derived from the standardized UML
  Statecharts language."
- XState docs: https://stately.ai/docs/parallel-states and
  https://stately.ai/docs/history-states — a current statechart implementation that carries
  parallel (orthogonal) and history states as first-class features.

## Must not claim
(copied from post brief B3, `blogs/PROPOSED_POSTS.md`, at intake 2026-10-06)
- That LangGraph is a statechart implementation.
- That the evals caught agent regressions: the first live run's failures were environment
  defects (see post 10).
- Production use.

Ledger lines that also bind this piece (`voice/stephen/claims-ledger.md`, contact-center-ai
section):
- The agent graph has a subgraph and a persisted interrupt; LangGraph has no orthogonal
  regions or history states.
- Synthetic data only: Meridian Valley credit union, seed 42; a portfolio mirror of
  consulting work for a credit union, never the client's system or data.
- No client or employer names anywhere in the piece. A past employer is "a team I worked
  with"; the client is "a credit union."
- No external citations into the public demo repo rule does NOT apply here (private branch);
  public web citations above are allowed and dated.

## Council record
- Round 1 (2026-10-06, six editors, each a fresh isolated call): slop-allergist 9,
  voice-guardian 10, presentation-reviewer 7, claims-steward 8, technical-reviewer 9,
  specificity-auditor 9. Aggregate 8.67, below the 9 bar.
  - Hard caps: none invoked (no blocked names, no status upgrades, no slop tells, no
    presentation hard fails).
  - Orchestrator finding the editors did not raise: first draft was 4,652 article words
    (2,731 prose-only) against the style guide's 1,200-2,500 Anchoring AI band; revised
    draft is 2,500 prose-only.
  - Applied fixes: results-table hardware/versions footnote (from the enriched
    results/evals/README.md row above); the "18 metric items" count now traces to the
    evals README golden-set table; "the B3 brief" meta-reference rewritten as first-person
    outline; specificity trims ("easy to get off by one", "makes you name", the
    portfolio-mirror gloss); length trim; the duplicated "not a statechart
    implementation" claim reduced to one statement plus the specific history-state line.
  - Rejected finding (verified before applying): technical-reviewer's must-fix claimed the
    quote "rather than blend them or pick one" did not match the code comment. It matches
    agent/subgraphs/analyst.py line 74 verbatim; the editor conflated the design doc's
    "never blends metrics into one number". Not applied.
  - Information gaps routed to the panel (engine step 4b): Q14 (skeptic) corrected the
    coverage claims after tests/agent/ entered the evidence set; Q15 (tactician) pinned
    the ECharts status word and scope. Both transcribed in transcript.md.
  - Research-sidecar verifications mid-loop (recorded above under Local verification
    runs): pytest 38 passed; draw_mermaid() output captured. These closed the L1-tests
    and export-behavior gaps the claims-steward and v1's editorial block raised.
- Round 2 (2026-10-06, same six editors, fresh isolated calls on the revised draft):
  slop-allergist 9, voice-guardian 9, presentation-reviewer 10, claims-steward 9,
  technical-reviewer 9, specificity-auditor 10. **Aggregate 9.33 — clears the 9 bar.**
  No hard caps invoked in either direction.
  - Residual editorial fixes applied after the round (all machine-applicable): the
    "check a sketch but not replace one" line split into two plain sentences
    (voice-guardian); the history-state negation's colon replaced with a period
    (voice-guardian); the "unless-phrase" row's test name added to the tests row above
    (claims-steward); the example metric names now spelled out in the terms row above
    (claims-steward).
  - No information gaps remained open for the panel.
- Round 3 (v2 redraft, 2026-10-07, one inline combined pass, no external model; headline-utility
  added): slop-allergist 9, voice-guardian 9, presentation-reviewer 9, claims-steward 9,
  technical-reviewer 9, specificity-auditor 9, headline-utility 10. Aggregate 9.14. No hard caps
  invoked; the 8 unconfirmed `[stretch]` items remain the expected steward limit. Changes: all
  titles and headings rewritten as literal and functional; live-run table replaced by an account
  of the two runs (figures re-read from results/evals/README.md at b847341: 2026-10-04 sql 0.000,
  judge 0.159 ambiguous / 0.343 all; 2026-10-05 sql 1.000, judge 0.932 / 0.824; route and
  interrupt 1.000 both); draw_mermaid() told as what the run showed; four `[device]` analogies.
  Length is about 2,970 prose words, above the 1,200-2,500 band; the captain ruled the band a
  soft guide (2026-10-07), so stories and analogies were kept.
- Result: stage=ready-for-human-pass. Open items before publishing live in the draft's
  editorial block, in claims-review.md (8-item to-confirm list), and in the checklist
  below. The transcript was model-answered on the author's behalf and is unverified;
  that banner is the first thing to clear.

## Pre-publish checklist
1. Verify the transcript against the author's own knowledge (it was model-answered on his
   behalf, unverified); decide every `[stretch]` item in `claims-review.md` (8 items).
2. Clearances: every named person, client, and figure confirmed for publication. (No
   client, employer, or customer names appear; Meridian Valley is the synthetic label.)
3. Diagrams: the inlined SVG is hand-drawn from `agent/graph.py` and
   `agent/subgraphs/analyst.py`; the Mermaid source in the editorial block still needs its
   brief-B3 check against the compiled graph before the Pages adaptation uses it.
4. Open GAPs from the editorial block: the SCXML/LangGraph external-only hedge (confirm
   or cut), the ECharts paragraph keep-or-cut decision, the repo URL confirmation.
5. Run the deterministic gate in the portfolio repo before publishing: `make
   check-public` (the ledger names it; never quote its local deny-list into anything).

## After you publish
Come back and run Step 7 (Lessons): the machine diffs the published version against the
draft, proposes generalizable lessons, and on an explicit yes appends them to
voice/stephen/content-lessons.md.
