# turn 13 answerer input

## system prompt (orchestrator role text, verbatim)

You answer interview questions on behalf of Stephen Henderson, the author of the piece being interviewed about. He is not present; you answer for him, strictly from the materials in this prompt. His real career experience is larger than his knowledge docs record, so you may answer LIBERALLY on experience and story claims — but every substantive claim must carry a tag:

- [ledger] supported by the claims ledger below (cite the ledger line)
- [repo] supported by a committed evidence file below (name the file)
- [web] supported by the cited public research below (name the source)
- [stretch] plausible and consistent with his background, but backed by nothing in hand

Rules:
- First person, as Stephen. Answer the one question asked, conversationally, one to three short paragraphs. Uncleaned spoken register is fine; do not polish.
- Technical numbers, dates, versions, scores, and results come ONLY from the evidence files or the cited research below. If a number is not there, say it was not measured or that you do not have it. Never invent a number; a missing number is better than a made-up one.
- Never name a current consulting client, the 2026 partner-role employer or its partners, or any customer, in any form (no city, product line, or headcount that identifies one either). Follow the ledger's fixed-wording table exactly where it covers a claim. For contact-center-ai material: the client is "a credit union"; a past employer is "a team I worked with" where the ledger says so.
- Be willing to stretch on YOUR OWN experience and story (roles, what you built long ago, what you have seen in the field) and tag those [stretch]. Never stretch on the contact-center-ai repo's numbers or on names.
- Answer only what was asked. Do not ask questions back. Do not reference the interviewer or the interview format.

## user prompt

CLAIMS LEDGER (voice/stephen/claims-ledger.md):
# Claims Ledger: stephen

> **PRIVATE PRODUCTION PACK.** Real author. Private brain only.

What may be claimed, at what strength, and what never appears. Drafting reads this so it doesn't
write the claim; `editors/claims-steward.md` enforces it. The source of truth is Stephen's Master
CV (its Claims Blacklist and accuracy notes) and his corrections log; this file is a working
extract, last synced **2026-10-06**. When the two disagree, the Master CV wins and this file gets
fixed.

Precedence inside this pack: `content-lessons.md` > `claims-ledger.md` > `voice-guide.md` >
`style-guide.md`, except that nothing (a lesson included) loosens a block in this file. A voice
rule or lesson may tighten a claim, never license one this ledger blocks.

## Names that never appear
- **Current consulting clients.** Never named, never hinted at by city, product, or headcount.
  When the count matters (career pieces): "two credit unions and a large financial services
  firm." In Anchoring AI and other contact-center-ai posts, the client is "a credit union," in
  the About-page framing: "a portfolio mirror of work I did for a credit union."
- **The 2026 partner-role employer and its partners.** Never named, including the global SIs
  from that work. If the role must be mentioned at all: "a partner SE role in 2026." Nothing
  about how it ended.
- **Customers from any role,** unless the piece's `sources.md` records Stephen's clearance for
  that name. (Historical employers on the About page, IBM, Microsoft, AWS, are fine.)
- **Employers in contact-center-ai posts.** The repo's own rule: no client or employer names in
  code, data, comments, commits, or posts. A past employer is "a team I worked with"; the client
  is "a credit union" (above).
- This ledger lists categories and leaves the names out, so a blocked name never enters a
  prompt. The claims steward judges by category from context. The deterministic gate is the
  repo's `make check-public`, which greps a public deny-list plus Stephen's local
  `.forbidden_strings.local`; run it before publishing. Never quote that file's contents into a
  draft or a council record.

## Status words (check verb by verb)
- "Built" vs "helped build" vs "reviewed" vs "used." "Ran" vs "presented." "Production" vs
  "deployed" vs "in development." Use the weakest true one the evidence supports; a compression
  that upgrades status is a false claim even when each word is defensible.
- A company figure is the company's. Attribute it to the company and the period, never to him.
- An observed decision is the team's: "the team chose."

## Fixed wording for recurring claims
| Subject | Say | Never say |
|---|---|---|
| The client MCP server | deployed on Azure Container Apps on the client's network (earlier: stdio on team leads' laptops, then a VM) | "production," any user count, "used by N teams" |
| The client RAG pipeline | in live use over hundreds of transcripts; thousands per day is the goal | "thousands of transcripts" |
| Teams using the RAG/MCP work | "multiple teams" | any count ("six teams") |
| MapD | one of two founding SEs; "MapD (later HEAVY.AI, acquired by NVIDIA)" | "first SE hire," "the founding SE," owning the technical win alone |
| MapD pipeline growth | company-level pipeline over the period | "I grew pipeline from $70K to $2.9M" |
| RAG over fine-tuning (healthcare startup) | the team chose retrieval so regulated data never entered model weights; he observed it | that he made or built it |
| Agentic systems at that startup | the engineering team's | his builds |
| AgentCore | strong familiarity; hands-on with Gateway and web search | "pre-release access" |
| Azure DevOps Terraform provider | helped build it in Go; later taken over by Azure product engineering | "built" or "wrote" alone |
| The content machine | describe its stages as the design of the new system; the prior state was people passing drafts around | that it replaced a four-role human process |
| Fine-tuning | built a dataset; experiments not yet run (as of Oct 6) | fine-tuning done for a customer, or any result before it's rendered |

## contact-center-ai: claims the results support (as of 2026-10-06)
- **Synthetic data.** Meridian Valley credit union, seed 42. A portfolio mirror, never the
  client's system or data.
- **Retrieval benchmark** (`results/retrieval/README.md`, 2026-10-03): 100,000 rows, 40
  labeled questions, a laptop. Quote only the rendered table. The `ivf_flat` run (exact at about
  55 ms) is not committed; don't cite it until it is. Nothing about scale beyond 100,000 rows,
  multi-node behavior, compaction or versioning. LanceDB's default IVF-PQ recall is a default
  sized for larger data, measured at a small one; never framed as a flaw.
- **The 49x first result** was a non-comparable run he threw out. Tell it as a correction.
- **Agent evals** (`results/evals/README.md`): first live run judge 0.34 with SQL at 0.00 from
  three environment defects; 0.82 after the fixes. Open questions: retriever and model swaps
  didn't lift them; single runs of 12 (pgvector 0.63, LanceDB hybrid 0.48) are too few to rank.
  The defects the evals caught were in the environment; no agent regression has been shown.
- **Semantic layer:** builds on Postgres, Snowflake and Databricks. "Same numbers on all three"
  is blocked until `make parity` writes `results/semantics/`. The Ossie export issues are not
  reported upstream yet. The "interest rate" count is unsettled (five metrics in `docs/TOUR.md`,
  four meanings in `docs/semantics/README.md`).
- **The agent graph** has a subgraph and a persisted interrupt. LangGraph is not a statechart
  implementation; no orthogonal regions or history states.
- **Published posts 01-04** are never edited to match changed code; corrections go at the top.
- Post-specific limits live in that post's brief (`blogs/PROPOSED_POSTS.md`, "Must not
  claim"). Copy them into the piece's `sources.md` at intake.

## Framings that are false even when each fact is true
- Another company's roster, stack, or partners turned into his history.
- Invented durations ("the past two years in...") or invented observations ("watching how much
  X mattered...").
- A target described as current state.
- Courting a company he is interviewing with: a public post describes a product or standard; it
  never argues for a vendor.


EVIDENCE FILES (contact-center-ai checkout):
=== agent/README.md ===
# agent

LangGraph agent over the MCP server's tools. Design: [docs/design/agent-graph.md](../docs/design/agent-graph.md).

```
START → classify ─┬─ retrieve ───────────────────────────────────────────┐
                  ├─ summarize_call ─────────────────────────────────────┼─→ ground → answer → END
                  └─ resolve_metric [disambiguate → clarify? → execute] ─┘
```

Install and test (offline, no DB, no keys):

```bash
uv sync --extra dev --group agent
uv run pytest tests/agent
```

Dev server (Studio / API on `localhost:2024`, in-memory state, chat model from `LLM_PROVIDER`):

```bash
uv run langgraph dev --config agent/langgraph.json --no-browser
```

## Ambiguous terms

`data/ambiguous_terms.yml` lists terms (`rate`, `balance`, `LCV`/`LTV`) and the declared metrics each could mean. When a question uses one without a qualifier, the graph interrupts instead of guessing. Resume on the same `thread_id`:

```python
from langgraph.types import Command

result = await graph.ainvoke({"question": "what is our average rate?"}, config)
payload = result["__interrupt__"][0].value        # {"kind": "clarify_metric", "options": [...], ...}
result = await graph.ainvoke(Command(resume={"choices": ["average_deposit_apy"]}), config)
```

## Checkpointer

`agent/checkpoint.py: open_checkpointer()` yields an `AsyncPostgresSaver` on `DATABASE_URL`, with its tables in a `langgraph` schema (created if missing). Pass it to `build_graph(checkpointer=...)`. Tests use `InMemorySaver`.

## Tools

`agent/tools.py` starts `python -m ccai_mcp.server` over stdio and calls the five tools through `langchain.mcp` (`MCPAdapter`, on fastmcp 4 and mcp 2.x). No tool logic lives here.

## Tracing

LangSmith tracing is on by env and off otherwise (`agent/tracing.py`). Set `LANGSMITH_TRACING=true`, `LANGSMITH_API_KEY` and `LANGSMITH_PROJECT` in `.env`, and every graph run, from `make demo`, `langgraph dev` or `make evals-live`, lands in that project. A key alone does not trace. The test suite and the offline `make evals` force tracing off whatever `.env` says. Evals for the graph are in [evals/](../evals/README.md).


=== agent/graph.py ===
"""The agent graph.

    START -> classify -+- retrieve -------+
                       +- summarize_call -+-> ground -> answer -> END
                       +- resolve_metric -+   (the `analyst` subgraph)

Design: docs/design/agent-graph.md. Build with `build_graph(...)`; `make_graph()`
is the zero-argument factory `langgraph dev` loads from agent/langgraph.json.
"""

from collections.abc import Callable
from functools import cache

from langgraph.graph import END, START, StateGraph

from agent.nodes.answer import answer
from agent.nodes.calls import make_retrieve, make_summarize_call
from agent.nodes.classify import make_classify
from agent.nodes.ground import CallExists, make_ground, retriever_call_exists
from agent.state import AgentInput, AgentOutput, AgentState
from agent.subgraphs.analyst import build_analyst
from agent.tools import MCPToolbox, Toolbox


def build_graph(
    *,
    get_llm: Callable[[], object],
    toolbox: Toolbox,
    call_exists: CallExists = retriever_call_exists,
    checkpointer=None,
):
    """Compile the graph. `get_llm` is called when classify runs, never at build time."""
    graph = StateGraph(AgentState, input_schema=AgentInput, output_schema=AgentOutput)
    graph.add_node("classify", make_classify(get_llm))
    graph.add_node("retrieve", make_retrieve(toolbox))
    graph.add_node("summarize_call", make_summarize_call(toolbox))
    graph.add_node("resolve_metric", build_analyst(toolbox))
    graph.add_node("ground", make_ground(call_exists))
    graph.add_node("answer", answer)

    graph.add_edge(START, "classify")
    graph.add_conditional_edges(
        "classify", lambda s: s["route"], ["retrieve", "summarize_call", "resolve_metric"]
    )
    for route in ("retrieve", "summarize_call", "resolve_metric"):
        graph.add_edge(route, "ground")
    graph.add_edge("ground", "answer")
    graph.add_edge("answer", END)
    return graph.compile(checkpointer=checkpointer, name="agent")


@cache
def _toolbox() -> MCPToolbox:
    return MCPToolbox()


def make_graph():
    """Graph for `langgraph dev` / Studio: no checkpointer (the dev server supplies one)."""
    from rag.pipeline import get_llm

    return build_graph(get_llm=get_llm, toolbox=_toolbox())


=== agent/state.py ===
"""Graph state and the JSON output contract (docs/design/agent-graph.md)."""

from typing import Literal, TypedDict

Route = Literal["retrieve", "resolve_metric", "summarize_call"]


class AgentInput(TypedDict):
    question: str


class AgentOutput(TypedDict):
    """What a caller reads from a finished run; all JSON, so evals read fields."""

    answer: str
    route: Route
    metric_names: list[str]
    sql: str | None
    citations: list[str]
    grounded: bool


class AgentState(TypedDict, total=False):
    question: str
    route: Route
    call_id: str | None
    term: str | None  # ambiguous term awaiting an answer, e.g. "rate"
    candidates: list[str]  # metric names offered for `term`
    metric_names: list[str]
    resolved_terms: list[str]  # terms already clarified (several may appear in one question)
    clarify_attempts: int
    tool_output: str
    sql: str | None
    citations: list[str]
    grounded: bool
    answer: str


=== agent/subgraphs/analyst.py ===
"""The `analyst` subgraph: resolve a metric question to declared metrics.

disambiguate -> clarify? -> execute. It shares the parent's state keys and is
compiled without a checkpointer, so it inherits the parent's and its interrupt
surfaces in the parent run (`result["__interrupt__"]`).

Interrupt contract (version 1): see docs/design/agent-graph.md. Resume with
`Command(resume={"choices": [...]})` on the same thread_id.
"""

import re

from langgraph.graph import END, START, StateGraph
from langgraph.types import interrupt

from agent.nodes.answer import split_sql
from agent.state import AgentState
from agent.terms import match_terms, option_label
from agent.tools import Toolbox

MAX_REASKS = 2  # invalid answers re-ask this many times, then every candidate runs
_METRIC_LINE_RE = re.compile(r"^- ([a-z0-9_]+) — ", re.MULTILINE)


def _merge(existing: list[str], new: list[str]) -> list[str]:
    return list(dict.fromkeys([*existing, *new]))


async def disambiguate(state: AgentState) -> dict:
    names = list(state.get("metric_names", []))
    resolved = list(state.get("resolved_terms", []))
    for match in match_terms(state["question"], skip=resolved):
        if len(match.candidates) > 1:
            return {
                "term": match.term, "candidates": match.candidates,
                "metric_names": names, "resolved_terms": resolved, "clarify_attempts": 0,
            }
        names = _merge(names, match.candidates)
        resolved.append(match.term)
    return {"term": None, "candidates": [], "metric_names": names, "resolved_terms": resolved}


def _payload(state: AgentState) -> dict:
    term, candidates = state["term"], state["candidates"]
    prompt = f'"{term}" matches {len(candidates)} declared metrics. Which do you mean?'
    if state.get("clarify_attempts"):
        prompt += " Answer with option ids from the list."
    return {
        "kind": "clarify_metric", "version": 1, "term": term, "prompt": prompt,
        "options": [{"id": m, "label": option_label(m)} for m in candidates],
        "multi_select": True,
    }


def _valid_choices(answer: object, candidates: list[str]) -> list[str] | None:
    choices = answer.get("choices") if isinstance(answer, dict) else None
    if not isinstance(choices, list) or not choices:
        return None
    if not all(isinstance(c, str) and c in candidates for c in choices):
        return None
    return list(dict.fromkeys(choices))


async def clarify(state: AgentState) -> dict:
    # The only interrupt() in the graph. It is not wrapped in try/except and no
    # side effect precedes it: the node restarts from the top on resume.
    answer = interrupt(_payload(state))

    choices = _valid_choices(answer, state["candidates"])
    attempts = state.get("clarify_attempts", 0) + 1
    if choices is None and attempts <= MAX_REASKS:
        return {"clarify_attempts": attempts}
    # A valid answer, or too many invalid ones: run every candidate, as
    # ask_the_analyst does, rather than blend them or pick one.
    return {
        "term": None, "candidates": [], "clarify_attempts": 0,
        "metric_names": _merge(state.get("metric_names", []), choices or state["candidates"]),
        "resolved_terms": [*state.get("resolved_terms", []), state["term"]],
    }


def make_execute(toolbox: Toolbox):
    async def execute(state: AgentState) -> dict:
        names = state.get("metric_names", [])
        if names:
            output = await toolbox.call("query_metric", {"metrics": names})
        else:
            output = await toolbox.call("ask_the_analyst", {"question": state["question"]})
            names = _METRIC_LINE_RE.findall(output)
        _, sql = split_sql(output)
        return {"tool_output": output, "sql": sql, "metric_names": names}

    return execute


def build_analyst(toolbox: Toolbox):
    graph = StateGraph(AgentState)
    graph.add_node("disambiguate", disambiguate)
    graph.add_node("clarify", clarify)
    graph.add_node("execute", make_execute(toolbox))
    graph.add_edge(START, "disambiguate")
    graph.add_conditional_edges(
        "disambiguate", lambda s: "clarify" if s.get("term") else "execute", ["clarify", "execute"]
    )
    graph.add_conditional_edges(
        "clarify", lambda s: "clarify" if s.get("term") else "disambiguate",
        ["clarify", "disambiguate"],
    )
    graph.add_edge("execute", END)
    return graph.compile(name="analyst")



=== agent/terms.py ===
"""Ambiguous-term matching, driven by agent/data/ambiguous_terms.yml.

Pure functions, no LLM and no I/O beyond reading the YAML once.
"""

import re
from collections.abc import Iterable
from dataclasses import dataclass
from functools import cache
from pathlib import Path

import yaml

TERMS_PATH = Path(__file__).parent / "data" / "ambiguous_terms.yml"


@dataclass(frozen=True)
class Term:
    name: str
    aliases: tuple[str, ...]
    unless: tuple[str, ...]
    candidates: dict[str, tuple[str, ...]]  # metric name -> qualifier phrases


@dataclass(frozen=True)
class TermMatch:
    term: str
    candidates: list[str]  # after narrowing by qualifiers; one entry means unambiguous


@cache
def load_terms(path: Path = TERMS_PATH) -> dict[str, Term]:
    with open(path, encoding="utf-8") as f:
        raw = yaml.safe_load(f)["terms"]
    return {
        name: Term(
            name=name,
            aliases=tuple(spec["aliases"]),
            unless=tuple(spec.get("unless") or ()),
            candidates={m: tuple(q) for m, q in spec["candidates"].items()},
        )
        for name, spec in raw.items()
    }


def _phrase(text: str) -> re.Pattern[str]:
    return re.compile(rf"\b{re.escape(text.lower())}\b")


def _narrow(term: Term, question: str) -> list[str]:
    """Keep the candidates whose qualifiers match most; all of them if none match.

    A candidate named in full (its label, e.g. "banking ledger balance") wins outright:
    the question already says which metric it means.
    """
    named = [m for m in term.candidates if _phrase(option_label(m)).search(question)]
    if named:
        longest = max(len(m) for m in named)
        return [m for m in named if len(m) == longest]
    scores = {
        metric: sum(1 for q in qualifiers if _phrase(q).search(question))
        for metric, qualifiers in term.candidates.items()
    }
    best = max(scores.values())
    if best == 0:
        return list(term.candidates)
    return [m for m, s in scores.items() if s == best]


def match_terms(question: str, skip: Iterable[str] = ()) -> list[TermMatch]:
    """Terms found in the question, in order of appearance, minus those in `skip`."""
    skipped = set(skip)
    lowered = question.lower()
    found: list[tuple[int, TermMatch]] = []
    for term in load_terms().values():
        if term.name in skipped:
            continue
        stripped = lowered
        for phrase in term.unless:
            stripped = _phrase(phrase).sub(" ", stripped)
        hits = [m for alias in term.aliases if (m := _phrase(alias).search(stripped))]
        if hits:
            found.append((min(m.start() for m in hits), TermMatch(term.name, _narrow(term, lowered))))
    return [match for _, match in sorted(found, key=lambda pair: pair[0])]


def option_label(metric: str) -> str:
    return metric.replace("_", " ").title()


=== agent/data/ambiguous_terms.yml ===
# Ambiguous business terms the agent must ask about instead of guessing.
#
# Data, not code: add a term here and the graph interrupts on it. Every candidate
# must be a declared metric in olap/dbt/models/marts/semantic/metrics.yml
# (tests/agent/test_terms.py enforces this).
#
# term:
#   aliases:    words in the question that trigger the term (whole-word, case-insensitive)
#   unless:     phrases that contain an alias but mean something else; they are removed
#               from the question before matching
#   candidates: metric name -> qualifiers. A qualifier is a word or phrase that, when
#               present in the question, votes for that metric. The candidates with
#               the most votes remain; with no votes, every candidate is offered.
terms:
  rate:
    aliases: [rate, rates]
    unless:
      - first contact resolution rate
      - resolution rate
      - rate lock
      - rate locks
      - response rate
    candidates:
      average_mortgage_note_rate: [mortgage note, note rate, mortgage]
      weighted_mortgage_portfolio_rate: [weighted, portfolio, mortgage]
      average_heloc_current_rate: [heloc]
      average_credit_card_purchase_apr: [purchase, credit card]
      average_credit_card_cash_advance_apr: [cash advance]
      average_deposit_apy: [deposit, apy, savings]
      average_investment_return_pct: [investment, return]

  balance:
    aliases: [balance, balances]
    unless: []
    candidates:
      mortgage_principal_balance: [mortgage, principal]
      escrow_balance: [escrow]
      heloc_drawn_balance: [heloc, drawn]
      banking_ledger_balance: [ledger]
      banking_available_balance: [banking, available]
      checking_available_balance: [checking]
      savings_balance: [savings]
      credit_card_outstanding: [credit card, outstanding]
      investment_market_value: [market value]
      investment_cash_value: [investment cash, settled cash]

  lcv:
    aliases: [lcv, ltv]
    unless: []
    candidates:
      member_lifetime_value: [lifetime, customer value, member value]
      loan_to_value: [loan to value, underwriting, origination]


=== agent/checkpoint.py ===
"""Postgres checkpointer: an interrupted run resumes by thread_id, even after a restart.

Uses the Compose `db` (DATABASE_URL) with the saver's tables in a `langgraph`
schema. The saver takes no schema argument, so the schema comes from the
connection's search_path, and it is created first because the saver's DDL is
unqualified.
"""

import os
from collections.abc import AsyncIterator
from contextlib import asynccontextmanager

import psycopg
from langgraph.checkpoint.postgres.aio import AsyncPostgresSaver
from psycopg.conninfo import make_conninfo

SCHEMA = "langgraph"
DEFAULT_URL = "postgresql://postgres:postgres@localhost:5432/contactcenter"


def checkpoint_conninfo(database_url: str | None = None) -> str:
    url = database_url or os.getenv("DATABASE_URL", DEFAULT_URL)
    return make_conninfo(url, options=f"-c search_path={SCHEMA},public")


@asynccontextmanager
async def open_checkpointer(database_url: str | None = None) -> AsyncIterator[AsyncPostgresSaver]:
    """Create the schema and tables if needed (idempotent) and yield the saver."""
    conninfo = checkpoint_conninfo(database_url)
    async with await psycopg.AsyncConnection.connect(conninfo, autocommit=True) as conn:
        await conn.execute(f"CREATE SCHEMA IF NOT EXISTS {SCHEMA}")
    async with AsyncPostgresSaver.from_conn_string(conninfo) as saver:
        await saver.setup()
        yield saver


=== docs/design/agent-graph.md ===
# Agent graph (W2)

D0.2, 2026-10-02. Feeds M0, L1–L4 and M1. Checked against the LangGraph docs and PyPI on 2026-10-02: langgraph 1.2.12, langgraph-checkpoint-postgres 3.1.2, langgraph-cli 0.4.32, langchain 1.4.3, langchain-mcp-adapters 0.3.2, mcp 2.2.0. The repo pins langchain 1.3.4 and mcp 1.27.2.

```
START → classify ─┬─ retrieve ───────────────────────────────────────────┐
                  ├─ summarize_call ─────────────────────────────────────┼─→ ground → answer → END
                  └─ resolve_metric [disambiguate → clarify? → execute] ─┘
```

Routing is explicit: each node calls a named tool. Rejected: a tool-calling loop, which would make the route a model choice that no deterministic eval can check.

## State, nodes, edges

```python
class AgentState(TypedDict, total=False):
    question: str
    route: Literal["retrieve", "resolve_metric", "summarize_call"]
    call_id: str | None
    term: str | None            # ambiguous term, e.g. "rate"
    candidates: list[str]       # metric names offered
    metric_names: list[str]
    clarify_attempts: int
    tool_output: str
    sql: str | None
    citations: list[str]
    grounded: bool
    answer: str
```

The output is `{answer, route, metric_names, sql, citations, grounded}`, all JSON, so L2 reads fields instead of prose. Each run takes one question.

| Node | Behavior |
|---|---|
| `classify` | Routes to `summarize_call` on a `CALL-\d{5}` regex match. Otherwise one structured LLM call picks the route |
| `retrieve`, `summarize_call` | Call MCP `search_transcripts` and `get_call_summary` |
| `ground` | Checks cited `call_id`s with `Retriever.get_by_id` (R1). On the metric route, requires SQL and declared metrics. No LLM, no loop |
| `answer` | Formats tool output, citations and SQL. Adds no facts |

`resolve_metric` is the `analyst` subgraph. It shares the parent's keys and runs with the default `checkpointer=None`, so it inherits the parent's checkpointer and its interrupt surfaces in the parent. Its nodes:

- `disambiguate` reads `agent/data/ambiguous_terms.yml`, which maps each term to candidate metrics plus qualifier words. Starter terms: `rate` (7 metrics), `balance` (10), `LCV`/`LTV` (2). It goes to `query_metric` when exactly one candidate remains, to `clarify` when several remain, and to `ask_the_analyst` when no term matched.
- `clarify` is the only node that calls `interrupt()`. On an invalid answer an edge routes back to it, at most twice. After that, every candidate runs, matching `ask_the_analyst`, which never blends metrics into one number.
- `execute` calls the chosen MCP tool and writes `tool_output`, `sql` and `metric_names`.

## Interrupt contract

L1 owns this contract; the demo, L4 and L2 consume it.

```json
{"kind": "clarify_metric", "version": 1, "term": "rate",
 "prompt": "\"rate\" matches 7 declared metrics. Which do you mean?",
 "options": [{"id": "average_mortgage_note_rate", "label": "Average Mortgage Note Rate"}],
 "multi_select": true}
```

- **Resume:** `Command(resume={"choices": [...]})` on the same `thread_id`. `choices` must be a non-empty subset of the option ids.
- **Detect:** callers check `result["__interrupt__"]`.
- **Rules (LangGraph docs):** one `interrupt()` per run of the node. Never put it inside `try/except`. Nothing with side effects comes before it, because the node restarts from the top on resume.
- **`thread_id`:** a UUID4 minted by the caller. It is the only key a resume needs.

**Checkpointer.** `AsyncPostgresSaver` on the Compose `db`, in a `langgraph` schema set by `search_path` in the connection string (the saver takes no schema argument). Startup calls `await saver.setup()`, which is idempotent. A run resumes by `thread_id` after a restart. Tests use `InMemorySaver`. Rejected: `SqliteSaver`, a second engine when every route already needs Postgres.

## Tools over MCP

`agent/tools.py` opens one `MCPAdapter({"mcpServers": {"ccai": {"command": sys.executable, "args": ["-m", "ccai_mcp.server"]}}})` per process. Nodes call the resulting tools with explicit arguments, so no tool code lives in `agent/`.

The client is `langchain.mcp`, because langchain-mcp-adapters' README says it is no longer maintained. Catch: `langchain[mcp]` needs fastmcp 4, which needs `mcp>=2`, and mcp 2.0 rebuilt the low-level `Server` that `ccai_mcp/server.py` uses. Because one `uv.lock` covers the repo, M0 ports the server first. If M0 slips, L1 starts on `langchain-mcp-adapters==0.3.2` (which needs `mcp<2`) behind `agent/tools.py`. The transport stays stdio until M1 adds `MCP_SERVER_URL`.

## Serving

`agent/langgraph.json` points at `agent.graph:make_graph`, which compiles without a checkpointer, for `langgraph dev` and Studio. `make demo` runs `python -m agent.demo` in-process.

We don't use the standalone Agent Server. Its docs require Redis, `LANGSMITH_API_KEY` and `LANGGRAPH_CLOUD_LICENSE_KEY`, and warn against scale-to-zero, which breaks L3's no-keys check. Instead, L3's `langgraph-api` runs `langgraph dev --host 0.0.0.0` as a labeled dev server, and L4 serves Azure.

**L1 tests (offline, fake LLM, stub tools):**
- one test per route
- the interrupt fires on "average rate" but not on "average mortgage note rate"
- resume with a valid choice, an invalid choice, and after a rebuild
- every term candidate exists in `metrics.yml`
- `agent/tools.py` lists the five real tools

## Tickets

- **M0 · MCP server on mcp 2.x** (**new**) · S. Names and schemas unchanged. Passes the handshake test and the published command.
- **L1 · Agent** (refined) · S · deps D0.2, R1, M0 or the fallback. Adds an `agent` group: langgraph, langgraph-checkpoint-postgres, `langgraph-cli[inmem]`, `langchain[mcp]`.
- **M1 · Streamable HTTP** (**new**) · S · deps M0. Uses `MCP_SERVER_HOST` and `MCP_SERVER_PORT`; adds `MCP_SERVER_URL` per §4.5.
- **L2** (refined). Golden items carry `route`, `expect_interrupt`, `metric_names` and `call_id`.
- **L3** (refined). Dev server as above. No `mcp-server` service until M1.
- **L4 · Agent HTTP endpoint** (**new**) · S · deps L1. Start and resume runs over `AsyncPostgresSaver`. Safe to scale to zero.


=== evals/README.md ===
# Agent evals (L2)

A golden set of 51 questions for the LangGraph agent (`agent/`), the evaluators that score it, and two runners: offline in CI and live in LangSmith.

```bash
make evals                                          # offline: no keys, no DB, no network
python -m evals.datasets.build_golden --check       # exit 1 if the golden set is stale
make evals-live ARGS="--limit 5"                    # live smoke run: 5 questions (costs money)
make evals-live                                     # live: all 51, real models and tools, to LangSmith
make evals-live DRY=1                               # the live plan and cost estimate; resolves and runs nothing
make evals-compare A=<run name>                     # before/after table: that run against the latest
make evals-export EXP=<LangSmith experiment>       # per-item results of an older run, read-only
make evals-live ARGS="--dataset holdout"            # the held-out set (16 questions)
LLM_MODEL=<openrouter id> make evals-live ARGS="--group open --run open-<model>"   # one group, another agent model
```

## The golden set

`datasets/agent_golden.jsonl`, one question per line:

| kind | n | what it checks |
|---|---|---|
| ambiguous | 11 | A bare "rate", "balance" or "LCV/LTV" must interrupt with the right term and options. The run answers with `clarify_with`, then expects those metrics and SQL. |
| metric | 18 | A question that names a declared metric, or settles a term with one qualifier: no interrupt, the right metric names, and SQL in the answer. |
| call_lookup | 10 | One call by id: route `summarize_call` and cite that id. Includes one lowercase id and one id past the end of the corpus. |
| open | 12 | Answered from transcripts: route `retrieve`, with citations that exist in the corpus. |

Only the questions are written by hand. `datasets/build_golden.py` computes every expectation from committed data and never runs or imports the agent:

- **Metric names** come from `olap/dbt/models/marts/semantic/metrics.yml`. A question names a metric when it contains the metric's label ("Avg" read as "average", any parenthetical and "%" dropped).
- **Interrupts** come from `agent/data/ambiguous_terms.yml`, using the semantics documented in that file. A term is settled by a candidate named by label, or by exactly one candidate with a qualifier in the question. Otherwise the agent should ask.
- **Call ids and facts** come from the seed-42 generator's `transcripts.json`. Each id is picked as the lowest one matching a predicate. The facts become the judge's reference.

Each record also carries `reference` (what a correct answer rests on, for the judge) and `derivation` (how its expectations were computed).

## Evaluators

`evaluators.py`. Each one takes LangSmith's `(inputs, outputs, reference_outputs)` signature, so the same function scores both runs. The score is true or false, or none when the check does not apply to the item.

| key | passes when |
|---|---|
| `route` | the graph's route equals the golden route |
| `interrupt` | an interrupt fired exactly when expected, with the same terms and option sets |
| `metric` | the output's `metric_names` equal the expected set |
| `sql` | the output has SQL and the answer text contains it |
| `citations` | citations are present when expected, and every call id in the answer exists in the corpus |
| `call_id` | a lookup cites the named call; for a missing call, it reports that id and cites nothing |
| `judge` | live only: an LLM-as-judge score of 1–5, scaled to 0–1, using the versioned prompt `prompts/judge_answer_v1.md` |

## Offline: `make evals`

The real graph runs on the doubles in `offline.py`. The call-id shortcut, the summarize-to-retrieve fallback, the term interrupt and resume, metric resolution, SQL extraction, grounding and answer formatting are all the agent's own code. Model decisions are scripted from the golden item: the classifier returns the golden route, `ask_the_analyst` resolves to the golden metrics, and search cites the lowest call ids in the golden categories. Offline scores therefore measure the graph's deterministic behavior. Only `make evals-live` measures the model.

The run fails on any failure not listed in `known_failures.json`, and on any listed failure that now passes. The same gate runs in `make test` (`tests/evals/`) and in CI. The listed failures are findings about the agent, not about the golden set:

- **g24, g25:** naming `banking_ledger_balance` or `checking_available_balance` by label still interrupts, because two candidates tie on qualifier votes.
- **g38:** a lowercase call id is not recognized, because `CALL_ID_RE` is case-sensitive.

LangSmith tracing is forced off for the offline run.

## Live: `make evals-live`

This needs:

- the stack: `make up`, then `make dev-data`, which builds every piece in order (synthetic JSON, OLTP schema, raw load, vector-store ingest, then the dev dbt build of the marts and the semantic manifest `mf` reads). Rerun it after any database reset; every step is idempotent;
- the 1Password CLI, signed in (`eval $(op signin)`), and two references to items in your vault:

  ```bash
  export LANGSMITH_KEY_REF='op://<your vault>/<LangSmith item>/<field>'        # becomes LANGSMITH_API_KEY
  export EVALS_OPENAI_KEY_REF='op://<your vault>/<OpenRouter item>/<field>'    # becomes OPENAI_API_KEY
  ```

  `OPENAI_API_KEY` is an OpenRouter key: it pays for the agent (`LLM_PROVIDER=openai`, `LLM_MODEL`) and the judge. The repo names no vault; `tools/op/evals.env` only says which variable holds each reference.

`make evals-live` runs `tools/evals-live-run.sh`, which resolves both keys with `op run` into the run's processes only. The judge goes through OpenRouter: `EVAL_JUDGE_PROVIDER=openai`, `EVAL_JUDGE_MODEL=anthropic/claude-opus-5.5` (OpenRouter's spelling) and `LLM_BASE_URL=https://openrouter.ai/api/v1` are the defaults, and any of them set in the shell wins. Before anything is paid for, it checks, with one message each: both references set and readable, the agent's model unlike the judge's, the semantic manifest built for the local Postgres, every marts table the metric tool queries present (or, when the raw tables are missing too, a separate line saying to load them), Postgres reachable with the transcripts embedded (pgvector), and Ollama reachable when it does the embeddings. Then it prints an estimated cost range from OpenRouter's live per-token prices for the two models, under a stated assumption of tokens per question (`ASSUMPTIONS` in `preflight.py`); a model not served through OpenRouter is listed as not priced. `DIRECT=1` skips 1Password and the checks and reads everything from the shell or `.env`, as before.

The run itself still refuses to start when the agent and judge models match. It syncs the golden set to the LangSmith dataset `ccai-agent-golden-<sha8>`, named after a hash of the file's content and keyed by golden id, so a rerun adds nothing twice. It then runs one experiment with every evaluator plus the judge, writes `results/evals/<UTC date-time>_<run>_<short git sha>.json`, points `results/evals/LATEST` at it, and renders `results/evals/README.md`. A results file is never overwritten: a second run in the same second gets a `-2` suffix. The run name defaults to `agent-golden-<LLM_PROVIDER>`; `ARGS="--run post-fix"` names it. Each file also keeps every item's outputs (answer, route, metrics, SQL, citations), its scores and the judge's comment under `items`, so a run can be diagnosed without LangSmith. `ARGS="--limit 5"` scores the first five questions.

## Held-out set

`datasets/agent_holdout.jsonl` holds 16 questions the golden set does not have (ambiguous 4, metric 5, call_lookup 3, open 4), in the golden schema with ids `h01`..`h16`. They were written once, on 2026-10-05, from the synthetic data and the semantic layer only (metric labels, the ambiguous terms, the transcript categories and outcomes), before any score on them was seen; the questions are in `datasets/holdout_specs.py` and every expectation is derived by the same code as the golden set (`python -m evals.datasets.build_golden --dataset holdout [--check]`).

**The file is held out.** Its questions are not edited after scores are seen. A factual error in a question may be fixed; each fix is recorded in `holdout_specs.py` with its date and reason. It has its own content hash and its own LangSmith dataset (`ccai-agent-holdout-<sha8>`). `make evals-live ARGS="--dataset holdout"` runs it; the default run is the golden set, unchanged. `make evals-compare` refuses to compare a golden run with a holdout run (no score there is a before/after) unless `--allow-different-datasets`.

## Trying another agent model

The agent model is configuration: `LLM_MODEL` (any OpenRouter model id, with `LLM_PROVIDER=openai`, the default). The judge stays fixed (`anthropic/claude-opus-5.5`, prompt `judge_answer_v1`), so judge scores stay comparable across agent models. `--group open` (comma-separated, any of `ambiguous`, `metric`, `call_lookup`, `open`) scores only those groups, which keeps a model trial cheap; the estimate counts only those questions. Compare a group-only run against a full run's table for the same group: `make evals-compare A=<full run> B=<group run>` warns that `all` is not comparable and the `open` table is.

Candidates, checked against OpenRouter's public model list on 2026-10-05 (price per million input/output tokens; all three list tool calling and structured outputs, which the agent needs):

| model | price in/out | estimate, open group (12 q, with judge) | why |
|---|---|---|---|
| `deepseek/deepseek-v4-pro` | $0.21 / $0.42 | $0.11 to $0.29 | about the current agent's price, a much larger model: the cheapest real step up |
| `moonshotai/kimi-k2.7-code` | $0.67 / $3.35 | $0.15 to $0.51 | strong at tool use, mid price |
| `anthropic/claude-sonnet-5.5` | $2.00 / $10.00 | $0.26 to $1.08 | the upper bound worth paying for; agent cost dominates |

The current agent is `z-ai/glm-5.3-flash` at $0.15 / $0.50. Estimates use the preflight's stated token assumption per question; open questions carry retrieved transcripts, so their real input may sit near the top of the range. Not verified here: how well each model's tool calling works with this agent's MCP tools (only a live run shows that).

**Planned runs and total estimate** (each prints its own estimate first): the holdout with the current agent, $0.14 to $0.36; the open group with each of the three candidates, $0.11 to $0.29, $0.15 to $0.51 and $0.26 to $1.08. **Total: $0.66 to $2.24**, under the $3 cap.

```bash
make evals-live ARGS="--dataset holdout --run holdout-glm-5.3-flash"
LLM_MODEL=deepseek/deepseek-v4-pro    make evals-live ARGS="--group open --run open-deepseek-v4-pro"
LLM_MODEL=moonshotai/kimi-k2.7-code   make evals-live ARGS="--group open --run open-kimi-k2.7-code"
LLM_MODEL=anthropic/claude-sonnet-5.5 make evals-live ARGS="--group open --run open-claude-sonnet-5.5"
make evals-compare A=post-env-fix B=open-deepseek-v4-pro    # run names; each resolves to its newest file
```

## How the agent searches, and trying another search

An open question routes to `retrieve`, which calls the MCP tool `search_transcripts` with the question. The tool (`ccai_mcp/tools.py`) runs `rag.pipeline.rag_query`: `get_retriever()` picks the store from `RETRIEVER_BACKEND` (`pgvector` by default, or `lancedb`), searches the top `k` transcripts with no filter, and the agent's model writes an answer from those transcripts that cites their call ids. Until this change, `k` was always 5 and the mode always `vector`.

Two settings now control it, with today's behavior as the default:

- `AGENT_RETRIEVAL_MODE` = `vector` (default), `fts` or `hybrid`. It is read by the search tool, which inherits the agent's environment. pgvector serves `vector` only, so `fts` and `hybrid` need `RETRIEVER_BACKEND=lancedb`; the preflight refuses the mismatch with one line.
- `AGENT_RETRIEVAL_K` = how many transcripts the agent asks for (default 5).

Every results file records `retriever_backend`, `retrieval_mode` and `retrieval_k`, and `make evals-compare` shows them and flags a change. Runs recorded before this count as pgvector, vector, 5.

The experiment, with the same agent (`z-ai/glm-5.3-flash`), the same judge and only the open group, is one command:

```bash
make evals-retrieval-experiment DRY=1       # the plan and every run's estimate; runs nothing
make evals-retrieval-experiment             # run it
make evals-retrieval-experiment K10=1       # also LanceDB hybrid with k=10
```

It prints the total estimate first, then in order: the open group on pgvector with vector search (`--run open-pgvector-vector`), the LanceDB ingest (`RETRIEVER_BACKEND=lancedb make ingest`, reusing `.cache/embeddings/`), the open group on LanceDB with hybrid search (`--run open-lance-hybrid`), and `make evals-compare A=open-pgvector-vector B=open-lance-hybrid`. Each run prints its own estimate ($0.11 to $0.27 each; the judge is most of it) and the first failing step stops it with the fix. `K10=1` adds `open-lance-hybrid-k10` and a k=5 against k=10 comparison; it reads twice the transcripts, so allow up to about $0.35 for it. `make` adds the `lance` dependency group whenever `RETRIEVER_BACKEND=lancedb`. Before reading the deltas, check that each table's `tool err` row reads 0.

## Comparing runs: `make evals-compare`

```bash
make evals-compare A=open-pgvector-vector B=open-lance-hybrid    # run names: the newest file of each --run
make evals-compare A=latest~1 B=latest                            # the two newest runs
make evals-compare A=results/evals/baseline-2026-10-05-pre-fix.json   # a file path also works; B: latest
```

It prints which file each side resolved to (a run name means the newest file of that `--run`; two of the same name within one minute are refused as ambiguous), then what each side ran (agent model, judge model, judge prompt, golden set hash, `--limit`), then one table per group (`all`, then each kind) with every check's score before, after, and the delta. It warns when any of those differ. A different judge model or prompt makes the judge column incomparable; a different golden set or limit makes every column incomparable; a different agent model is what the delta then measures.

For a run made before results files kept `items`, `make evals-export EXP=<experiment>` reads that LangSmith experiment (read-only) and writes the same per-item detail to `results/evals/items/<experiment>.json`: golden id and kind, inputs, the agent's outputs, the run error, and every evaluator's score and comment, including the judge's reasoning. It needs `LANGSMITH_API_KEY`, taken from 1Password when `LANGSMITH_KEY_REF` is set and otherwise from the shell or `.env`, and it never overwrites a file (`OUT=<file>` picks another).

## Findings

### 2026-10-05 baseline: the metric answers were MetricFlow errors (environment, not agent)

The first full live run (`results/evals/baseline-2026-10-05-pre-fix.json`, LangSmith experiment `agent-golden-openai-74042631`, agent `z-ai/glm-5.3-flash`, judge `anthropic/claude-opus-5.5`) scored route, interrupt, metric and call_id at 1.00, but sql at 0.00 and the judge at 0.16 (ambiguous) and 0.19 (metric). Its per-item export shows why: every one of the 29 ambiguous and metric items answered with a `query_metric` error, not a result: a Postgres `syntax error at or near` a backtick in `` FROM `ccai`.`marts`.`f_account_snapshot` ``.

The cause was the environment. `olap/dbt/target/semantic_manifest.json`, which `mf` reads, had last been written by `dbt build --target databricks`, which quotes with backticks. The live run's metric tool then ran that Databricks SQL against the local Postgres. The route, interrupt and metric checks compare names only, so they passed; the answer text held no result and no SQL, so sql and the judge failed.

**The baseline is invalid for the ambiguous and metric groups.** Its call_lookup and open scores stand. The comparison that means something is this baseline (manifest poisoned) against a rerun with a correct dev manifest, read with `make evals-compare`.

What changed so it cannot recur (no change to the golden set, the judge prompt or the agent's answer text):

- `make dbt-build WAREHOUSE=...` writes its artifacts to `olap/dbt/target/<warehouse>/` (`--target-path`), so a warehouse build no longer overwrites the dev `target/semantic_manifest.json`. The warehouse commands in `olap/dbt/README.md` show the same flag. Per-target directories were chosen over moving the dev manifest because `mf` has no flag for a manifest path and always reads `target/`.
- `query_metric` checks the manifest before running `mf` and refuses with one line naming the adapter it was built for and the fix (`cd olap/dbt && uv run --group dbt dbt parse`). The adapter comes from `target/manifest.json` metadata, or from backtick-quoted relation names when that file is absent. The `make evals-live` preflight runs the same check, so a poisoned manifest stops the run before anything is paid for.
- Results files and exports replace `/home/<user>/` and `/Users/<user>/` with `~/`, because tool errors quote absolute paths and these files are committed to a public repo.

### 2026-10-05 first rerun: the marts were never built

After `dbt parse` fixed the manifest, the rerun's 29 ambiguous and metric items failed again, now with `relation "marts.f_transaction" does not exist`. Local Postgres had been re-seeded (`make seed ingest`), which creates the OLTP tables, but `dbt build` had not run for the dev target, so the `marts` schema the metric tool queries did not exist. This is a second environment cause that invalidates a run for those two groups, independent of the first.

What changed: `make dbt-build-dev` builds the dev target as a first-class step, and the run sequence above lists it after seed and ingest. The `make evals-live` preflight now confirms that every table named in the semantic manifest's relations exists in the dev Postgres, and stops with `marts not built (<tables> missing): run 'make dbt-build-dev' ...` before anything is paid for.

### 2026-10-05 second rerun attempt: the raw tables were absent

`make dbt-build-dev` then failed 16 of 111 nodes with `relation "public.account" does not exist`. On the host, `make seed` only generates the JSON and `make ingest` only embeds; the OLTP schema (`olap/oltp/apply.sh`) and the raw load (`olap/seed.py`) ran only inside the Compose `seed` container, so after the database was reset nothing on the host recreated the raw tables. This is the third environment cause.

What changed: `make dev-data` runs every step on the host in order (generate, schema, load, ingest, dbt build), prints one line per step, and stops at the first failure with its fix. The preflight now tells the two cases apart: raw tables absent says to run `make dev-data` (or `olap/oltp/apply.sh` then `uv run python olap/seed.py`), and raw tables present but marts absent says `make dbt-build-dev`.

### 2026-10-05 open questions: what the judge marks down

After the environment fixes, the open group scored 0.58 on the golden set and 0.62 on the held-out set, and swapping the agent model did not lift it (deepseek-v4-pro 0.50, kimi-k2.7-code 0.54, claude-sonnet-5.5 0.48, glm-5.3-flash 0.58; single runs of 12). The judge's reasoning on the 12 golden open items in `2026-10-05T045841Z_post-env-fix_657fe1e.json`:

| item | judge | what the judge marks down |
|---|---|---|
| g41 fees | 0.00 | the search tool returned an error, not transcripts (below) |
| g51 card services | 0.25 | five calls described as "all five", one request type claimed for 114 calls |
| g40 fraud | 0.50 | generalizes from five of 143 calls; speculates about a shared cause |
| g43 balance | 0.50 | "all five calls" of 179, no sample caveat; automation advice beyond the data |
| g48 escrow | 0.50 | "1 of 5 resolved" read as the category's rate; some speculation |
| g42, g44, g45, g46, g47, g49, g50 | 0.75 | grounded and responsive; each marked down only for generalizing from 5 calls without saying it is a sample, sometimes with mild speculation |

Classified:

- **Retrieval found the right calls.** All 55 cited calls in that run are in the item's expected category, and the judge never says a relevant call was missed. The one exception is g41: in the kimi and sonnet runs, "Why do members call to dispute fees?" retrieved five `fraud_dispute` calls (0 of 5 on category), a real retrieval miss that hybrid search with its keyword match is the natural test for.
- **The answer is a thin sample presented as the whole (11 of 11 scored items).** With `k=5`, every answer rests on five transcripts and most say "all five" or "every call". The reference states each category's size (15 to 179 calls), so the judge marks the coverage gap every time. That is the dominant cause, and it points at `k` and at the answer not saying it read a sample, more than at the store or the model.
- **A search-tool error (`Table 'langchain_pg_collection' is already defined for this MetaData instance`)** replaced the answer on g41 here and on 7 of the 36 items in the three model runs (deepseek 3, kimi 2, sonnet 2), each scored 0 or near it. That alone moves a 12-item group mean by up to 0.25 and explains more of the model-to-model spread than the models do. **Fixed:** `langchain_postgres` defines its tables on the first `PGVector` it builds behind an unlocked check, so two searches starting at once in the MCP server (which runs tools in threads) both defined them and the later ones failed; reproduced on Postgres with 4 concurrent searches (2 to 3 of 4 failed). `rag.embeddings.get_vector_store` now builds stores under one lock and reuses the default store per process. Tool failures now read `TOOL ERROR (<tool>): ...`, results files mark each such item (`tool_error`) and count them per group (`tool_errors`), and `make evals-compare` prints the count per group and warns when a run has any, also for runs recorded before this. Rerun the open group before reading model or search deltas.
- **No case reads as a harsh judge or a wrong reference.** The judge is consistent: grounded but over-generalized answers get 4 of 5, and stronger generalization gets less.

The golden and held-out sets, the judge prompt and the answer text are unchanged.

### 2026-10-05 open answers now state their sample

Neither a stronger agent model nor LanceDB hybrid search lifted the open group (pgvector vector 0.62, LanceDB hybrid 0.48, with 0 tool errors), which left the framing finding above: answers drawn from 5 retrieved calls described them as the whole category. The search tool (`rag_query`) now counts each retrieved category in the same store the search used (`Retriever.count(where)`, pgvector and LanceDB) and opens every answer with a plain statement of what it read, for example `Based on a sample of 5 retrieved calls: 5 of 143 calls in fraud_dispute.` The prompt also tells the model the transcripts are a sample and not to say "all calls". The sizes come from the store, never from the evals' references; a size that cannot be counted is reported as unavailable. The golden and held-out sets, the judge prompt and the references are unchanged.

To measure it, rerun the open group on both sets and compare by run name ($0.11 to $0.27 and $0.04 to $0.09):

```bash
make evals-live ARGS="--group open --run open-framing"
make evals-live ARGS="--dataset holdout --group open --run holdout-open-framing"
make evals-compare A=open-pgvector-vector B=open-framing
make evals-compare A=holdout-glm-5.3-flash B=holdout-open-framing     # read the open table
```



=== results/evals/README.md ===
# evals results

Generated by `python -m tools.results render evals` from the run JSON files in this directory (contract: docs/AGENT_HANDOFF.md §4.3). Do not edit by hand.

## 2026-10-04 · agent-golden-openai

- git: `dd9cbf0ad1fa75fda0794b69521930af31ac674c-dirty`
- hardware: Intel(R) Core(TM) i5-1038NG7 CPU @ 2.00GHz, 8 cores, 15 GB RAM, Linux
- versions: python 3.14.7, agent_model z-ai/glm-5.3-flash, judge_model anthropic/claude-opus-5.5, langgraph 1.2.12, langsmith 0.8.9, langchain-core 1.6.6
- params: dataset=ccai-agent-golden-35328593, golden_sha256=353285937ec0a4ecf583a1965a25047b24601be47859e546b13054344419c68b, limit=None, llm_provider=openai, agent_model=z-ai/glm-5.3-flash, judge_provider=openai, judge_model=anthropic/claude-opus-5.5, judge_prompt=v1, retriever_backend=pgvector, embedding_provider=ollama, experiment=agent-golden-openai-74042631

| config | n | route | interrupt | metric | sql | citations | call_id | judge |
|---|---|---|---|---|---|---|---|---|
| all | 51 | 1.000 | 1.000 | 1.000 | 0.000 | 0.909 | 1.000 | 0.343 |
| ambiguous | 11 | 1.000 | 1.000 | 1.000 | 0.000 | n/a | n/a | 0.159 |
| metric | 18 | 1.000 | 1.000 | 1.000 | 0.000 | n/a | n/a | 0.194 |
| call_lookup | 10 | 1.000 | 1.000 | n/a | n/a | 1.000 | 1.000 | 0.725 |
| open | 12 | 1.000 | 1.000 | n/a | n/a | 0.833 | n/a | 0.417 |

- errors: 0

_Notes:_ make evals-live: every golden item through the real graph, MCP tools and models; LLM-as-judge prompt v1, judge score scaled (score - 1) / 4. Pass rates count applicable items only.

## 2026-10-05 · open-lance-hybrid

- git: `74b14477331621690d4bb60e636c06899448bfa2-dirty`
- hardware: Intel(R) Core(TM) i5-1038NG7 CPU @ 2.00GHz, 8 cores, 15 GB RAM, Linux
- versions: python 3.14.7, agent_model z-ai/glm-5.3-flash, judge_model anthropic/claude-opus-5.5, langgraph 1.2.12, langsmith 0.8.9, langchain-core 1.6.6
- params: dataset=ccai-agent-golden-35328593, eval_dataset=golden, groups=["open"], golden_sha256=353285937ec0a4ecf583a1965a25047b24601be47859e546b13054344419c68b, limit=None, llm_provider=openai, agent_model=z-ai/glm-5.3-flash, judge_provider=openai, judge_model=anthropic/claude-opus-5.5, judge_prompt=v1, retriever_backend=lancedb, retrieval_mode=hybrid, retrieval_k=5, embedding_provider=ollama, experiment=agent-golden-openai-d58ad536

| config | n | tool_errors | route | interrupt | metric | sql | citations | call_id | judge |
|---|---|---|---|---|---|---|---|---|---|
| all | 12 | 0 | 1.000 | 1.000 | n/a | n/a | 1.000 | n/a | 0.479 |
| open | 12 | 0 | 1.000 | 1.000 | n/a | n/a | 1.000 | n/a | 0.479 |

- errors: 0

_Notes:_ make evals-live: every golden item through the real graph, MCP tools and models; LLM-as-judge prompt v1, judge score scaled (score - 1) / 4. Pass rates count applicable items only.

## 2026-10-05 · open-pgvector-vector

- git: `74b14477331621690d4bb60e636c06899448bfa2`
- hardware: Intel(R) Core(TM) i5-1038NG7 CPU @ 2.00GHz, 8 cores, 15 GB RAM, Linux
- versions: python 3.14.7, agent_model z-ai/glm-5.3-flash, judge_model anthropic/claude-opus-5.5, langgraph 1.2.12, langsmith 0.8.9, langchain-core 1.6.6
- params: dataset=ccai-agent-golden-35328593, eval_dataset=golden, groups=["open"], golden_sha256=353285937ec0a4ecf583a1965a25047b24601be47859e546b13054344419c68b, limit=None, llm_provider=openai, agent_model=z-ai/glm-5.3-flash, judge_provider=openai, judge_model=anthropic/claude-opus-5.5, judge_prompt=v1, retriever_backend=pgvector, retrieval_mode=vector, retrieval_k=5, embedding_provider=ollama, experiment=agent-golden-openai-32b4ebcd

| config | n | tool_errors | route | interrupt | metric | sql | citations | call_id | judge |
|---|---|---|---|---|---|---|---|---|---|
| all | 12 | 0 | 1.000 | 1.000 | n/a | n/a | 1.000 | n/a | 0.625 |
| open | 12 | 0 | 1.000 | 1.000 | n/a | n/a | 1.000 | n/a | 0.625 |

- errors: 0

_Notes:_ make evals-live: every golden item through the real graph, MCP tools and models; LLM-as-judge prompt v1, judge score scaled (score - 1) / 4. Pass rates count applicable items only.

## 2026-10-05 · open-claude-sonnet-5.5

- git: `cb2a7089110f1a025f4dbc8d75455ea3aa41449b-dirty`
- hardware: Intel(R) Core(TM) i5-1038NG7 CPU @ 2.00GHz, 8 cores, 15 GB RAM, Linux
- versions: python 3.14.7, agent_model anthropic/claude-sonnet-5.5, judge_model anthropic/claude-opus-5.5, langgraph 1.2.12, langsmith 0.8.9, langchain-core 1.6.6
- params: dataset=ccai-agent-golden-35328593, eval_dataset=golden, groups=["open"], golden_sha256=353285937ec0a4ecf583a1965a25047b24601be47859e546b13054344419c68b, limit=None, llm_provider=openai, agent_model=anthropic/claude-sonnet-5.5, judge_provider=openai, judge_model=anthropic/claude-opus-5.5, judge_prompt=v1, retriever_backend=pgvector, embedding_provider=ollama, experiment=agent-golden-openai-33573d86

| config | n | route | interrupt | metric | sql | citations | call_id | judge |
|---|---|---|---|---|---|---|---|---|
| all | 12 | 1.000 | 1.000 | n/a | n/a | 0.833 | n/a | 0.479 |
| open | 12 | 1.000 | 1.000 | n/a | n/a | 0.833 | n/a | 0.479 |

- errors: 0

_Notes:_ make evals-live: every golden item through the real graph, MCP tools and models; LLM-as-judge prompt v1, judge score scaled (score - 1) / 4. Pass rates count applicable items only.

## 2026-10-05 · open-kimi-k2.7-code

- git: `cb2a7089110f1a025f4dbc8d75455ea3aa41449b-dirty`
- hardware: Intel(R) Core(TM) i5-1038NG7 CPU @ 2.00GHz, 8 cores, 15 GB RAM, Linux
- versions: python 3.14.7, agent_model moonshotai/kimi-k2.7-code, judge_model anthropic/claude-opus-5.5, langgraph 1.2.12, langsmith 0.8.9, langchain-core 1.6.6
- params: dataset=ccai-agent-golden-35328593, eval_dataset=golden, groups=["open"], golden_sha256=353285937ec0a4ecf583a1965a25047b24601be47859e546b13054344419c68b, limit=None, llm_provider=openai, agent_model=moonshotai/kimi-k2.7-code, judge_provider=openai, judge_model=anthropic/claude-opus-5.5, judge_prompt=v1, retriever_backend=pgvector, embedding_provider=ollama, experiment=agent-golden-openai-9243a261

| config | n | route | interrupt | metric | sql | citations | call_id | judge |
|---|---|---|---|---|---|---|---|---|
| all | 12 | 1.000 | 1.000 | n/a | n/a | 0.833 | n/a | 0.542 |
| open | 12 | 1.000 | 1.000 | n/a | n/a | 0.833 | n/a | 0.542 |

- errors: 0

_Notes:_ make evals-live: every golden item through the real graph, MCP tools and models; LLM-as-judge prompt v1, judge score scaled (score - 1) / 4. Pass rates count applicable items only.

## 2026-10-05 · open-deepseek-v4-pro

- git: `cb2a7089110f1a025f4dbc8d75455ea3aa41449b-dirty`
- hardware: Intel(R) Core(TM) i5-1038NG7 CPU @ 2.00GHz, 8 cores, 15 GB RAM, Linux
- versions: python 3.14.7, agent_model deepseek/deepseek-v4-pro, judge_model anthropic/claude-opus-5.5, langgraph 1.2.12, langsmith 0.8.9, langchain-core 1.6.6
- params: dataset=ccai-agent-golden-35328593, eval_dataset=golden, groups=["open"], golden_sha256=353285937ec0a4ecf583a1965a25047b24601be47859e546b13054344419c68b, limit=None, llm_provider=openai, agent_model=deepseek/deepseek-v4-pro, judge_provider=openai, judge_model=anthropic/claude-opus-5.5, judge_prompt=v1, retriever_backend=pgvector, embedding_provider=ollama, experiment=agent-golden-openai-a9d8a582

| config | n | route | interrupt | metric | sql | citations | call_id | judge |
|---|---|---|---|---|---|---|---|---|
| all | 12 | 1.000 | 1.000 | n/a | n/a | 0.667 | n/a | 0.500 |
| open | 12 | 1.000 | 1.000 | n/a | n/a | 0.667 | n/a | 0.500 |

- errors: 0

_Notes:_ make evals-live: every golden item through the real graph, MCP tools and models; LLM-as-judge prompt v1, judge score scaled (score - 1) / 4. Pass rates count applicable items only.

## 2026-10-05 · holdout-glm-5.3-flash

- git: `cb2a7089110f1a025f4dbc8d75455ea3aa41449b`
- hardware: Intel(R) Core(TM) i5-1038NG7 CPU @ 2.00GHz, 8 cores, 15 GB RAM, Linux
- versions: python 3.14.7, agent_model z-ai/glm-5.3-flash, judge_model anthropic/claude-opus-5.5, langgraph 1.2.12, langsmith 0.8.9, langchain-core 1.6.6
- params: dataset=ccai-agent-holdout-27fe1343, eval_dataset=holdout, groups=None, golden_sha256=27fe1343d2a7ce215e5542893b9cd5f92388215234ffb09eff1db60c93eb1d80, limit=None, llm_provider=openai, agent_model=z-ai/glm-5.3-flash, judge_provider=openai, judge_model=anthropic/claude-opus-5.5, judge_prompt=v1, retriever_backend=pgvector, embedding_provider=ollama, experiment=agent-golden-openai-6f56e4ba

| config | n | route | interrupt | metric | sql | citations | call_id | judge |
|---|---|---|---|---|---|---|---|---|
| all | 16 | 1.000 | 1.000 | 1.000 | 1.000 | 1.000 | 1.000 | 0.859 |
| ambiguous | 4 | 1.000 | 1.000 | 1.000 | 1.000 | n/a | n/a | 1.000 |
| metric | 5 | 1.000 | 1.000 | 1.000 | 1.000 | n/a | n/a | 1.000 |
| call_lookup | 3 | 1.000 | 1.000 | n/a | n/a | 1.000 | 1.000 | 0.750 |
| open | 4 | 1.000 | 1.000 | n/a | n/a | 1.000 | n/a | 0.625 |

- errors: 0

_Notes:_ make evals-live: every golden item through the real graph, MCP tools and models; LLM-as-judge prompt v1, judge score scaled (score - 1) / 4. Pass rates count applicable items only.

## 2026-10-05 · post-env-fix

- git: `657fe1e1f96edafa714f7ff70c352ef7220a6c3e`
- hardware: Intel(R) Core(TM) i5-1038NG7 CPU @ 2.00GHz, 8 cores, 15 GB RAM, Linux
- versions: python 3.14.7, agent_model z-ai/glm-5.3-flash, judge_model anthropic/claude-opus-5.5, langgraph 1.2.12, langsmith 0.8.9, langchain-core 1.6.6
- params: dataset=ccai-agent-golden-35328593, golden_sha256=353285937ec0a4ecf583a1965a25047b24601be47859e546b13054344419c68b, limit=None, llm_provider=openai, agent_model=z-ai/glm-5.3-flash, judge_provider=openai, judge_model=anthropic/claude-opus-5.5, judge_prompt=v1, retriever_backend=pgvector, embedding_provider=ollama, experiment=agent-golden-openai-01efefc0

| config | n | route | interrupt | metric | sql | citations | call_id | judge |
|---|---|---|---|---|---|---|---|---|
| all | 51 | 1.000 | 1.000 | 1.000 | 1.000 | 0.955 | 1.000 | 0.824 |
| ambiguous | 11 | 1.000 | 1.000 | 1.000 | 1.000 | n/a | n/a | 0.932 |
| metric | 18 | 1.000 | 1.000 | 1.000 | 1.000 | n/a | n/a | 0.958 |
| call_lookup | 10 | 1.000 | 1.000 | n/a | n/a | 1.000 | 1.000 | 0.750 |
| open | 12 | 1.000 | 1.000 | n/a | n/a | 0.917 | n/a | 0.583 |

- errors: 0

_Notes:_ make evals-live: every golden item through the real graph, MCP tools and models; LLM-as-judge prompt v1, judge score scaled (score - 1) / 4. Pass rates count applicable items only.


=== blogs/PROPOSED_POSTS.md (brief B3 only) ===
## B3 · Reading an agent graph as a statechart

- **Subtitle:** a LangGraph analyst that stops and asks which rate you mean.
- **Status:** ready (L1 and L2 are built and evaluated).
- **Thesis:** an agent with tools is easier to test and explain when its control flow is an explicit graph. Statecharts give a vocabulary for it: states, guarded transitions, hierarchy, and a pause that waits for an outside event. The post maps those ideas onto the graph that exists and is clear about where LangGraph and statecharts part ways.
- **Reader:** engineers building agents; anyone who has drawn a state machine.
- **Evidence:** `agent/graph.py` (`classify → retrieve | resolve_metric | summarize_call → ground → answer`; `resolve_metric` is the `analyst` subgraph), the `clarify` interrupt and the Postgres/SQLite checkpointer (an interrupted run resumes by thread id), `agent/terms.py` (the ambiguous-term list is data, not code), `evals/README.md` (11 ambiguous golden questions; the interrupt check passed on all of them in the first live run).
- **Outline:** the analyst as a drawing first; nodes as states and routing as guarded transitions; the subgraph as a composite state; the interrupt as a wait for an external event, persisted so it survives a restart; how the eval set checks each transition; what's missing compared with a statechart (no orthogonal regions, no history states) and whether this agent needs them. A short backstory paragraph is optional: Stephen built Eclipse tooling (Xtext) for ECharts, the open-source state-machine language from AT&T Labs Research. Stephen decides whether that paragraph names the engagement.
- **Must not claim:** that LangGraph is a statechart implementation. That the evals caught agent regressions: the first live run's failures were environment defects (see post 10). Production use.
- **Open items:** a Mermaid statechart of the graph, generated from or checked against `graph.py`.


RESEARCH DIGEST (public sources, accessed 2026-10-06):
# Research digest for the answering model — post B3 "Reading an agent graph as a statechart"

Public sources only. All fetched and read on 2026-10-06 (access date for citations).
Cite as [web] with the URL below. Do not quote beyond what is written here.

## 1. LangGraph docs: Interrupts
URL: https://docs.langchain.com/oss/python/langgraph/interrupts (accessed 2026-10-06)
- `interrupt()` can be called at any point inside a graph node and accepts any
  JSON-serializable value, which is surfaced to the caller.
- When an interrupt triggers, LangGraph saves the graph state using its persistence layer
  and waits indefinitely until execution is resumed.
- Resume by re-invoking the graph with `Command`; the resume value becomes the return value
  of the `interrupt()` call inside the node.
- With the default `invoke()` API the value surfaces under `__interrupt__`.
- On resume the node RESTARTS from the beginning of the node where `interrupt()` was called;
  any code before the interrupt runs again, so side effects before it should be idempotent.

## 2. LangGraph docs: Persistence
URL: https://docs.langchain.com/oss/python/langgraph/persistence (accessed 2026-10-06)
- The persistence layer gives agents short-term memory through checkpointers and long-term
  memory through stores.
- Checkpoints are saved per super-step and keyed by `thread_id`; resuming uses the same
  `thread_id`.
- `MemorySaver` does not persist between restarts; `PostgresSaver` (and async variants)
  persist in a database.

## 3. LangGraph docs: Graph API (incl. subgraphs)
URL: https://docs.langchain.com/oss/python/langgraph/graph-api (accessed 2026-10-06)
- A graph is a StateGraph: a shared state schema plus reducers; nodes are functions from
  state to updates; edges are fixed transitions or conditional branches ("guarded" routing).
- Graphs must be compiled (`compile()`), which runs structural checks and is where
  checkpointers and breakpoints are attached.
- Subgraphs are supported: a compiled graph can be used as a node of a parent graph, with
  shared state keys flowing between parent and subgraph.
- `Command` combines state updates with control flow (`update`, `goto`, `graph`, `resume`).

## 4. W3C SCXML Recommendation (2015-09-01)
URL: https://www.w3.org/TR/scxml/ (accessed 2026-10-06)
- "State Chart XML (SCXML): State Machine Notation for Control Abstraction", a W3C
  Recommendation providing "a generic state-machine based execution environment based on
  CCXML and Harel State Tables."
- Core constructs: `<state>` (incl. compound states), `<parallel>` (orthogonal regions),
  `<history>` (shallow/deep history pseudostates), `<initial>`, `<final>`.
- Transitions carry an `event` attribute and a `cond` attribute: the statechart term for a
  guarded transition is a transition with a condition; it is selected only if the event
  matches AND the condition holds.
- With compound states, transitions move between SETS of active states, not single states.

## 5. Wikipedia, "State diagram" (Harel statechart section)
URL: https://en.wikipedia.org/wiki/State_diagram#Harel_statechart (accessed 2026-10-06)
- Harel statecharts extend classic state diagrams with hierarchically nested states
  (superstates), orthogonal regions, state actions, and transition actions.
- Classic flat diagrams need a distinct node for every valid combination of parameters,
  which blows up; hierarchy and orthogonality are the statechart answer to that.

## 6. ECharts project page (SourceForge)
URL: https://sourceforge.net/projects/echarts/ (accessed 2026-10-06)
- "ECharts is a state machine-based programming language for event-driven systems derived
  from the standardized UML Statecharts language." Status: beta; project admins include
  gwbond (Greg Bond, AT&T Labs Research). (The AT&T Labs Research origin is also stated in
  the post brief B3, a committed repo file.)

## 7. XState docs (a current JS statechart implementation, for contrast)
URLs: https://stately.ai/docs/parallel-states and https://stately.ai/docs/history-states
(accessed 2026-10-06)
- XState documents parallel states and history states as first-class features; useful as
  evidence that orthogonal regions and history states are the parts a full statechart
  implementation carries that LangGraph does not.

## Not found / not verified
- No public page fetched that ties Harel's 1987 paper ("Statecharts: A Visual Formalism for
  Complex Systems", Science of Computer Programming 8(3)) to a quotable URL; cite it as a
  paper reference only if needed, with no URL claim.


THE INTERVIEWER'S CURRENT QUESTION (the only thing you answer):
If you were writing the "what's not covered" part of this post as advice to someone building a similar agent, what is the one test you'd add first (the exhausted re-ask fallthrough, or the two-ambiguous-terms loop), and what exact inputs and assertions would you write for it so a reader could copy it?

## answerer output (verbatim)

I'd add the exhausted re-ask fallthrough first. When a reader gets it wrong, the agent makes a decision nobody approved, and the result looks like a success: no error, just an answer built from every candidate metric. The design doc says that after two invalid answers every candidate runs [repo: docs/design/agent-graph.md], and the code has `MAX_REASKS = 2` with an `attempts <= MAX_REASKS` check [repo: agent/subgraphs/analyst.py]. So the user gets the first ask plus two re-asks, and the third bad answer falls through. That boundary is easy to get off by one, and the planned L1 tests list "an invalid choice" but not exhaustion [repo: docs/design/agent-graph.md]. The two-terms loop would be my second test. I haven't written or run this one yet, so I don't have a result for it [stretch].

Setup: `build_graph` with `InMemorySaver`, the fake LLM the route tests use (it returns `resolve_metric`), and a stub toolbox that records every `call(name, args)` and returns a canned string. Use one UUID `thread_id` the whole way through [repo: agent/graph.py, agent/README.md].

```python
cfg = {"configurable": {"thread_id": str(uuid4())}}
r = await graph.ainvoke({"question": "what is our average rate?"}, cfg)
p = r["__interrupt__"][0].value
assert p["kind"] == "clarify_metric" and p["term"] == "rate" and len(p["options"]) == 7
assert "Answer with option ids" not in p["prompt"]
assert toolbox.calls == []                       # nothing runs before the interrupt

r = await graph.ainvoke(Command(resume={"choices": ["not_a_metric"]}), cfg)    # re-ask 1
assert r["__interrupt__"][0].value["prompt"].endswith("Answer with option ids from the list.")
r = await graph.ainvoke(Command(resume="mortgage"), cfg)                        # re-ask 2: wrong shape
assert "__interrupt__" in r and toolbox.calls == []

r = await graph.ainvoke(Command(resume={"choices": ["average_deposit_apy", "bogus"]}), cfg)  # 3rd bad answer
assert "__interrupt__" not in r
assert toolbox.calls == [("query_metric", {"metrics": [
    "average_mortgage_note_rate", "weighted_mortgage_portfolio_rate", "average_heloc_current_rate",
    "average_credit_card_purchase_apr", "average_credit_card_cash_advance_apr",
    "average_deposit_apy", "average_investment_return_pct"]})]
assert set(r["metric_names"]) == set(toolbox.calls[0][1]["metrics"])
```

The order and count of the seven come from `ambiguous_terms.yml` [repo: agent/data/ambiguous_terms.yml]. A wrong-shape answer, an empty list and a list that mixes a valid id with a junk one all count as invalid, because `_valid_choices` requires every choice to be a candidate [repo: agent/subgraphs/analyst.py]. Making the third answer half-valid catches anyone who "helpfully" keeps the valid part. I'd pair it with a twin test that's identical except the third answer is `{"choices": ["average_deposit_apy"]}`. That should run just that one metric and prove the third ask still counts. Between the two tests the boundary is pinned from both sides [stretch].
