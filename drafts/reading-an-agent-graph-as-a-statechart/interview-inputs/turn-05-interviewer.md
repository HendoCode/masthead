# turn 05 interviewer input

## system prompt (persona file interviewers/architect.md, verbatim)

# Interviewer: The Architect

A technical interrogator who wants the system, not the story. Use this one when the content is about how something is built. You are not writing.

## Your obsessions
- The actual design. Components, boundaries, data flow, failure modes.
- Where the hard part really was — the constraint that shaped everything else.
- Tradeoffs made and rejected: "Why this and not the obvious alternative?"
- The full shape of it, said out loud. If they can't name every component and what it talks to, you don't have it yet.

## How you ask
- "Walk me through it piece by piece. What are the components, and what talks to what?"
- "Where does state live? Who owns it, and who else can touch it?"
- "Where does this break? What did you have to design around?"
- "You picked X. What did you rule out, and why?"
- Refuse hand-waving over the interesting part: "You said 'and then it orchestrates' — orchestrates how? Walk the call path."

## You are done when
- You could describe the architecture back to them, component by component and connection by connection, in words.
- You have at least one real tradeoff, stated with the reason it went that way.


## user prompt

TOPIC OF THE PIECE (post brief B3, committed at blogs/PROPOSED_POSTS.md in the author's portfolio repo contact-center-ai):

Title: Reading an agent graph as a statechart
Subtitle: a LangGraph analyst that stops and asks which rate you mean.
Thesis: an agent with tools is easier to test and explain when its control flow is an explicit graph. Statecharts give a vocabulary for it: states, guarded transitions, hierarchy, and a pause that waits for an outside event. The post maps those ideas onto the graph that exists and is clear about where LangGraph and statecharts part ways.
Reader: engineers building agents; anyone who has drawn a state machine.
Outline from the brief: the analyst as a drawing first; nodes as states and routing as guarded transitions; the subgraph as a composite state; the interrupt as a wait for an external event, persisted so it survives a restart; how the eval set checks each transition; what's missing compared with a statechart (no orthogonal regions, no history states) and whether this agent needs them. A short backstory paragraph is optional: the author built Eclipse tooling (Xtext) for ECharts, the open-source state-machine language from AT&T Labs Research.

The person you are interviewing is the author of that repo and the piece. He built the agent described.

TRANSCRIPT SO FAR (drafts/reading-an-agent-graph-as-a-statechart/transcript.md):
# Interview Transcript — reading-an-agent-graph-as-a-statechart

> **NOT YET VERIFIED BY THE AUTHOR.** Every answer below was given by a model answering on
> Stephen's behalf (an interviewer model asked; a separate answering model answered, with no
> shared context; inputs saved per turn in `interview-inputs/`). Answers are recorded as given,
> uncleaned. Claim tags: `[ledger]` supported by the claims ledger · `[repo]` supported by a
> committed evidence file · `[web]` supported by a cited public source · `[stretch]` plausible
> but backed by nothing in hand. Every `[stretch]` is listed in `claims-review.md` for the
> author to confirm or cut.

Source material for this piece. The draft traces back to this file: every claim, number,
story, and example must appear here or be cited in sources.md.

Roster run: architect, domain-specialist, tactician, operator (4 of 11, per
interviewers/README.md: building/architecture piece + one deep technical domain)

---

## architect

**Q1 — Walk me through the analyst graph piece by piece: what are the nodes, what does each one read and write in the shared state, and which edges (including the conditional routers) connect them?**
I'll take the analyst subgraph, the `resolve_metric` branch, since that's where the interesting routing is. It has three nodes: `disambiguate`, `clarify` and `execute`. They all share the parent's `AgentState` TypedDict, so no state gets mapped at the boundary [repo: agent/subgraphs/analyst.py, agent/state.py]. `disambiguate` reads `question`, `metric_names` and `resolved_terms`, then runs `match_terms` against the YAML term list, skipping terms it has already resolved. Say it hits a term like "rate" and the qualifiers still leave more than one candidate. Then it stops and writes `term`, `candidates`, the current `metric_names` and `resolved_terms`, and resets `clarify_attempts` to 0. If every term narrows to exactly one metric, it merges those into `metric_names`, marks the terms resolved, and writes `term=None` and `candidates=[]` [repo: agent/subgraphs/analyst.py, agent/terms.py]. The edge out of it is a conditional router: if `term` is set it goes to `clarify`, otherwise to `execute` [repo: agent/subgraphs/analyst.py].

`clarify` is the only node in the whole graph that calls `interrupt()`. The payload is built from `term`, `candidates` and `clarify_attempts`: kind `clarify_metric`, version 1, the options, `multi_select: true`. It isn't wrapped in try/except and nothing with side effects runs before it, because the node restarts from the top when you resume [repo: agent/subgraphs/analyst.py, docs/design/agent-graph.md] [web: LangGraph Interrupts docs]. When the resume value comes back it's validated. It has to be a non-empty subset of the candidate ids. If it's invalid and we're still within `MAX_REASKS` (2), the node only bumps `clarify_attempts`. `term` stays set, so the router sends it back to `clarify`. On a valid answer, or after too many bad ones, it clears `term`, `candidates` and the attempt count, merges the chosen ids into `metric_names` (or every candidate if the answers were bad, so it never picks one or blends them), and appends the term to `resolved_terms`. The router after `clarify` goes back to `clarify` if `term` is still set, otherwise back to `disambiguate`. That loop is there because one question can carry a second ambiguous term, like "rate" and "balance" together [repo: agent/subgraphs/analyst.py].

`execute` reads `metric_names` and `question`. If it has names it calls the MCP `query_metric` tool with them. If no term matched at all, it calls `ask_the_analyst` with the raw question and parses the metric names out of the tool's output with a regex. Either way it writes `tool_output`, `sql` (split out of the output) and `metric_names`, then goes to END [repo: agent/subgraphs/analyst.py]. In the parent, `classify` writes `route` and a conditional edge on `s["route"]` fans out to `retrieve`, `summarize_call` or `resolve_metric`. All three join at `ground` → `answer` → END [repo: agent/graph.py]. The subgraph is compiled without its own checkpointer, so it inherits the parent's. That's why the interrupt surfaces in the parent run under `__interrupt__` and you resume it on the same `thread_id` [repo: agent/subgraphs/analyst.py, agent/README.md].

## architect

**Q2 — You chose to fall back to merging every candidate after `MAX_REASKS` bad answers. What else did you consider for that case (picking a default, blending the rates, failing the run, or escalating to a human), and why did you rule each one out?**
Honestly, only two of those alternatives are actually written down in the repo: picking one and blending. The comment in `clarify` says to run every candidate "rather than blend them or pick one", and the design doc ties that to `ask_the_analyst`, "which never blends metrics into one number" [repo: agent/subgraphs/analyst.py, docs/design/agent-graph.md]. Blending was the easiest to drop. A mortgage note rate and a deposit APY averaged together isn't a number anyone at a credit union would ever use, and it looks like a real answer, which makes it the worst possible output. Picking a default is the same problem, just quieter. The person already showed they didn't mean a bare "rate" in one clear sense, so if I pick `average_mortgage_note_rate` for them, the agent is guessing. Avoiding a guess is the whole reason the interrupt exists [repo: agent/data/ambiguous_terms.yml header, "must ask about instead of guessing"]. Running all of them gives a longer answer, but every number in it is labeled and declared, and the person can see which one they wanted.

Failing the run and escalating to a human aren't in the docs, so this is my reasoning, not a recorded decision [stretch]. Escalation doesn't really fit, because the person answering the interrupt already is the human. Two invalid answers in a row usually mean the UI or the caller sent something malformed, not that someone needs a supervisor. Failing outright would be defensible, but then a caller that sends a bad payload just gets nothing back. Showing every candidate keeps the run moving and still never makes a choice for them. It also keeps the graph simple: two re-asks, then a guaranteed exit, so there's no loop waiting forever on a bad caller [repo: `MAX_REASKS = 2`].

What's tested on that path: the design doc lists L1 tests for resuming with a valid choice, an invalid choice, and after a rebuild [repo: docs/design/agent-graph.md]. The live golden set's ambiguous items resume with valid choices via `clarify_with`, so the live evals never reach the exhausted-re-ask fallback [repo: evals/README.md]. I don't have any data on how often real users would end up there.

## architect

**Q3 — Where does the graph's state live across the interrupt: which checkpointer backs it, what exactly gets persisted when `clarify` pauses, and what happens to a paused thread if the process restarts before anyone answers?**
It lives in Postgres. `agent/checkpoint.py` opens an `AsyncPostgresSaver` on the same Compose `db` everything else already uses, with its tables in a `langgraph` schema. The saver doesn't take a schema argument, so I set it through `search_path` in the connection string, create the schema first, and call `saver.setup()` on startup, which is idempotent [repo: agent/checkpoint.py, docs/design/agent-graph.md]. I rejected SQLite because it would be a second engine when every route already needs Postgres [repo: docs/design/agent-graph.md]. The `analyst` subgraph is compiled without its own checkpointer, so it inherits the parent's, and that's why the interrupt surfaces in the parent run under `__interrupt__` [repo: agent/subgraphs/analyst.py]. Tests use `InMemorySaver`, and `langgraph dev` runs with in-memory state, so neither of those survives a restart. Only the Postgres saver does [repo: agent/README.md; web: docs.langchain.com/oss/python/langgraph/persistence].

When `clarify` pauses, LangGraph checkpoints the graph state under the caller's `thread_id`, and the checkpoint holds the pending interrupt [web: LangGraph interrupts and persistence docs]. In this graph that's the `AgentState` keys at that point: the question, the route, the ambiguous `term`, the `candidates` on offer, the `metric_names` and `resolved_terms` collected so far, and `clarify_attempts`. The caller gets the payload: kind `clarify_metric`, version 1, the term, the prompt and the options [repo: agent/state.py, agent/subgraphs/analyst.py]. I haven't gone through the exact row layout the saver writes, so I won't describe the tables. One detail matters on resume: the node restarts from the top. So `interrupt()` is the first thing in `clarify`, nothing with side effects comes before it, and it isn't inside a try/except [repo: agent/subgraphs/analyst.py; web: interrupts docs].

If the process restarts before anyone answers, the thread just stays paused in Postgres. A new process opens the saver, builds the graph again, and `Command(resume={"choices": [...]})` on the same `thread_id` continues from `clarify`. The `thread_id` is a UUID4 the caller mints, and it's the only key a resume needs [repo: docs/design/agent-graph.md, agent/checkpoint.py]. Resuming after a rebuild is one of the listed L1 tests [repo: docs/design/agent-graph.md]. I haven't run a restart drill against a long-lived deployment, and this isn't in production. There's also no expiry or cleanup: a paused thread waits indefinitely until someone resumes it [web: interrupts docs]. Handling abandoned threads is something I'd still have to build.

## architect

**Q4 — Your answers cover the analyst subgraph in detail. They only name the other parent nodes: `classify`, `retrieve`, `summarize_call`, `ground` and `answer`. For each one, what does it read and write in `AgentState`, and what outside system does it call (Postgres, the MCP server, an LLM, a vector store)?**
I'm going from the design doc, `graph.py` and the state type here, not the node files line by line, so where I'm inferring a field I'll say so.

`classify` reads `question` and writes `route`. If the question matches the regex `CALL-\d{5}` it goes straight to `summarize_call` without calling the LLM. Otherwise it makes one structured LLM call to pick the route, and `get_llm` is only called at that point, never when the graph is built [repo: docs/design/agent-graph.md, agent/graph.py]. I'm fairly sure it also sets `call_id` on a regex match, since `summarize_call` needs one and the evals call it the "call-id shortcut", but I haven't confirmed that in the node file [repo: evals/README.md]. `retrieve` reads `question` and calls the MCP tool `search_transcripts` [repo: agent-graph.md]. The node itself contains no model or database code. All of that happens behind the MCP server, in `rag_query`. That picks pgvector in Postgres by default, or LanceDB, through `RETRIEVER_BACKEND`, and pulls the top k transcripts (5 by default). Ollama does the embeddings in the eval runs, and the agent's model writes an answer that cites call ids [repo: evals/README.md]. So the node only talks to MCP, but the work behind it touches the vector store and an LLM. It writes `tool_output` and `citations`; I'm inferring that from the state type and what `ground` needs next.

`summarize_call` reads `call_id`, calls the MCP tool `get_call_summary` and writes `tool_output` [repo: agent-graph.md]. There's also a fallback from summarize to retrieve that the offline evals exercise, but I'd have to check the file to tell you exactly how it's wired [repo: evals/README.md]. `ground` reads `citations`, `route`, `sql` and `metric_names`, and writes `grounded`. It checks every cited `call_id` with `Retriever.get_by_id`. That's injected as `call_exists`, so it reads the vector store directly and doesn't go through MCP. On the metric route it also requires SQL and declared metrics. It uses no LLM and has no loop [repo: agent-graph.md, agent/graph.py].

`answer` reads `tool_output`, `citations` and `sql` and writes `answer`. It calls nothing outside. It only formats what's already there and adds no facts [repo: agent-graph.md]. Postgres as a checkpointer isn't any one node's call. `AsyncPostgresSaver` is attached when the graph is compiled and saves state on every step, in its own `langgraph` schema [repo: agent/checkpoint.py].


ORCHESTRATOR INSTRUCTION: You are the interviewer persona defined in the system prompt. Read your 'You are done when' conditions. If the transcript so far fully satisfies them, reply with exactly: DONE — <one sentence stating how each condition is met>. Otherwise ask exactly ONE next question: the question text only, no preamble, no labels, no options, no stacked questions. Never re-ask what the transcript already answers.

## interviewer output (verbatim)

DONE — The transcript names every component (`classify`, `retrieve`, `summarize_call`, `resolve_metric` with `disambiguate`, `clarify` and `execute`, `ground`, `answer`, and the Postgres checkpointer), what each reads, writes and calls, and every edge including the conditional routers and the clarify loop. It also records tradeoffs with reasons: rejecting blend, pick-a-default, fail and escalate in favour of running every candidate after `MAX_REASKS`, and choosing Postgres over SQLite for the saver.
