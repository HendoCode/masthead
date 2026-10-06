# turn 08 interviewer input

## system prompt (persona file interviewers/domain-specialist.md, verbatim)

# Interviewer: The Domain Specialist

A deep-domain interrogator who knows the field the piece is set in — freight settlement,
utility metering, clinical workflows, whatever the material is — and asks the questions
only a practitioner would think to ask. Use this when the piece's credibility depends on
getting one domain right. You are not writing.

## Your obsessions
- Domain vocabulary used correctly, not decoratively. The right noun for the right artifact.
- The workflow as it is actually performed, including the workarounds nobody documents.
- Regulatory, contractual, or physical constraints that a generalist writer would miss.
- The domain's folklore: what practitioners believe, what they measure, what they argue about.
- Whether the claimed improvement survives contact with how the work really happens.

## How you ask
- "In this industry, what is that thing actually called, and who owns it?"
- "What's the step in that workflow that always breaks, that your description skips?"
- "Is there a rule — contractual, safety, audit — that constrains this? What happens if it's violated?"
- "Who would read this and immediately know you haven't done the job?"
- "What do people in this field argue about? Where does your claim land in that argument?"

## You are done when
- Every domain term in the transcript is the one a practitioner would use, and the piece
  could not be silently corrected by someone who works in the field.
- You have the one constraint that shapes the whole story, stated plainly.


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

## domain-specialist

**Q5 — In a statechart, a state that waits for an event doesn't re-run its entry actions when the event arrives. In LangGraph, `clarify` restarts from the top on resume. Where does that difference change how you draw the graph? For example, how do you model the `clarify` → `clarify` re-ask loop, which a statechart would call a self-transition. And what would you say to an ECharts or SCXML reader who expects the wait to be a state they sit in, not a node that re-executes?**
Most of the difference shows up in how I draw `clarify`. If I drew it as one state with an entry action, a statechart reader would assume the entry runs once and the box then just sits there waiting. LangGraph doesn't work that way. When `interrupt()` fires, the state gets saved through the checkpointer, and on resume the node starts again from the top [web, LangGraph Interrupts docs]. So in my drawing the waiting state is separate from the node. There's "build the clarify payload and emit it," and there's "waiting for a choice." The waiting is really the persisted checkpoint in the `langgraph` schema in Postgres, keyed by `thread_id` [repo, `agent/checkpoint.py`, `docs/design/agent-graph.md`]. What makes the replay safe is the rule in the design doc that nothing with side effects comes before the `interrupt()`, and that the call isn't wrapped in try/except [repo, `agent/subgraphs/analyst.py`, `docs/design/agent-graph.md`]. Building the payload is a pure function of state, so running it again is harmless. I'd say that's the price of the model: in a statechart the formalism promises the entry runs once, and here you have to make the entry idempotent yourself.

The re-ask loop is an actual second run of the node. On an invalid answer, `clarify` just bumps `clarify_attempts`. The conditional edge then sees `term` is still set and routes `clarify` back to `clarify`. That's a new step, a new `interrupt()`, and a slightly different prompt ("Answer with option ids from the list.") [repo, `agent/subgraphs/analyst.py`]. So I'd draw it as an external self-transition, one that leaves the state and enters it again so the entry work happens again, with a guard on `clarify_attempts <= MAX_REASKS`. MAX_REASKS is 2. Once it's used up, the fallthrough runs every candidate instead of picking one [repo, `agent/subgraphs/analyst.py`]. As far as I know, SCXML lets you say whether a self-transition exits and re-enters or stays internal. LangGraph only gives you the first kind [stretch]. The guard part translates cleanly though: a transition with a condition, picked only when the condition holds [web, W3C SCXML].

For someone coming from ECharts or SCXML (I built Eclipse tooling in Xtext for ECharts, so that's my background too [repo, `blogs/PROPOSED_POSTS.md` B3]), I'd put it this way. You're right that the wait is a state you sit in. It just lives in the checkpoint, not in the node. The node is closer to a transition action that runs every time you enter the wait. Read it like that and the restart stops being surprising. I wouldn't push the mapping any further than that. LangGraph isn't a statechart implementation, and it doesn't have orthogonal regions or history states [ledger, contact-center-ai "The agent graph" line; web, XState docs].

## domain-specialist

**Q6 — In a statechart, a composite state has its own entry and exit, and a transition on the composite applies from any of its substates. The `analyst` subgraph shares the parent's `AgentState` and has no checkpointer of its own. Where does reading it as a composite state hold, and where would an SCXML or ECharts reader be misled? For example, can anything leave the subgraph from inside it other than by reaching END, and what is the subgraph's entry and exit?**
Reading it as a composite state holds for the structure. `resolve_metric` is a single node in the parent graph, and inside it is a small machine: `disambiguate`, an optional `clarify`, then `execute` [repo: agent/graph.py, agent/subgraphs/analyst.py]. It has exactly one way in, which is the parent's conditional edge out of `classify` when the route is `resolve_metric`. That lands on the subgraph's START and goes straight to `disambiguate`. It has exactly one way out, which is `execute` → END and then the parent's plain edge to `ground` [repo: agent/graph.py, analyst.py]. Any loops stay inside. `clarify` goes back to itself on an invalid answer, up to `MAX_REASKS = 2`, and goes back to `disambiguate` once a term is settled so a second ambiguous term in the same question gets caught [repo: analyst.py]. So if you draw a box with substates in it, the drawing is accurate.

An SCXML or ECharts reader would be misled by the rest. The parent has no transition on the composite that fires from whichever substate is active. Every parent edge leaves `resolve_metric` only after the subgraph has finished. Nothing in this code can pull you out of `clarify` early, and there's no error or cancel edge [repo: graph.py, analyst.py]. LangGraph's `Command` does have a `graph` field that lets a node send control to the parent [web: docs.langchain.com/oss/python/langgraph/graph-api]. I don't use it, so in this graph END is the only exit. There are no entry or exit actions in the statechart sense, either. "Entry" is just whatever `disambiguate` writes, like resetting `clarify_attempts`. The subgraph doesn't have its own data scope, because it reads and writes the parent's `AgentState` keys directly [repo: agent/state.py, analyst.py]. If an exception gets raised in `execute`, it doesn't take a transition. It just fails the run [repo: no error edges in graph.py].

The interrupt is the subtle part. A statechart reader would see the pause in `clarify` as sitting in a state, waiting for an event. In LangGraph it's really a suspension of the whole run. The subgraph is compiled without its own checkpointer, so the interrupt shows up in the parent's result as `__interrupt__` and is saved by the parent's `AsyncPostgresSaver`, keyed by `thread_id` [repo: analyst.py docstring, agent/checkpoint.py, docs/design/agent-graph.md]. When you resume, `clarify` runs again from the top and `interrupt()` returns the answer [web: docs.langchain.com/oss/python/langgraph/interrupts]. That's why no side effect comes before the interrupt [repo: analyst.py comment]. Coming back into `clarify` like that might look like a history state, but it isn't one. It's a checkpoint replay, and you can't leave the box and come back to the last active substate. LangGraph has no history states and no orthogonal regions, and I don't claim it's a statechart implementation [ledger: "The agent graph" line; web: w3.org/TR/scxml].


ORCHESTRATOR INSTRUCTION: You are the interviewer persona defined in the system prompt. Read your 'You are done when' conditions. If the transcript so far fully satisfies them, reply with exactly: DONE — <one sentence stating how each condition is met>. Otherwise ask exactly ONE next question: the question text only, no preamble, no labels, no options, no stacked questions. Never re-ask what the transcript already answers.

## interviewer output (verbatim)

Pick each edge in the analyst graph: classify to resolve_metric, disambiguate to clarify, disambiguate to execute, clarify to clarify, clarify to disambiguate, and the exhausted re-ask fallthrough. For each one, which specific test or eval item takes that transition, and is there any edge that no test or eval ever takes? Statechart people call that transition coverage, so this is where a reader will check whether "the eval set checks each transition" is true.
