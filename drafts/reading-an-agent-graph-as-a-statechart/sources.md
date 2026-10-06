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
  not code. Starter terms: `rate` (7 candidate metrics), `balance` (10), `lcv`/`ltv` (2).
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
  2026-10-05: judge 0.824 all, ambiguous 0.932, interrupt 1.000 on all 11 ambiguous items.
- `blogs/PROPOSED_POSTS.md` — post brief B3 (title, subtitle, thesis, outline,
  must-not-claim lines, the optional ECharts backstory paragraph).

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
- Round 1: (not run)

## Pre-publish checklist
1. (open GAPs from the draft's editorial block land here)
2. Clearances: every named person, client, and figure confirmed for publication.
3. Diagrams: any inlined SVG redrawn from a current source.
4. Interview basis: the transcript was answered by a model on the author's behalf; every
   `[stretch]` claim in `claims-review.md` needs the author's confirm/cut decision before
   publication.

## After you publish
Come back and run Step 7 (Lessons): the machine diffs the published version against the
draft, proposes generalizable lessons, and on an explicit yes appends them to
voice/stephen/content-lessons.md.
