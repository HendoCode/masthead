# Claims review — reading-an-agent-graph-as-a-statechart

The author asked for this list: where the model that answered on his behalf stretched, so he
can confirm or cut before anything publishes. Tags: `[ledger]` claims ledger · `[repo]`
committed evidence file · `[web]` cited public source · `[stretch]` plausible but backed by
nothing in hand. The interview transcript keeps every answer uncleaned; the draft carries its
own claim-tag table in the editorial block of draft.html.

**Provenance note:** the claims ledger's source of truth is Stephen's Master CV. No
Master-CV extract was available in this repo (and per instructions none was hunted down
elsewhere), so the ledger extract (last synced 2026-10-06) was the only claims authority used.

## The to-confirm list (every `[stretch]`, 8 items)

| # | Claim | Where it appears | What it strains | Disposition in the draft |
|---|---|---|---|---|
| 1 | Reasoning against failing the run or escalating to a human after exhausted re-asks ("the person answering the interrupt already is the human") | Transcript Q2; draft, "The graph" section | Not a recorded decision; the ledger's status-word discipline (observed vs decided) | Kept, framed as "my reasoning", explicitly "not discussed in the design doc" |
| 2 | "As far as I know, LangGraph gives you only the external kind [of self-transition]" | Transcript Q5; draft, "The re-ask loop" section | The #1 voice rule (evidence first): a capability claim about LangGraph with no source in hand. The SCXML half is `[web]` (w3.org/TR/scxml, internal vs external transition types) | Kept, hedged in the body, and an open GAP in the editorial block. Confirm against LangGraph source or cut |
| 3 | ECharts construct details from memory (nested machines for hierarchy, concurrent machines, guarded transitions, messages on ports) | Transcript Q8 only | The answerer itself flagged it "treat this as memory"; the brief leaves the backstory to Stephen | NOT in the draft. The ECharts paragraph stays minimal (no construct keywords). If it grows, check names against the ECharts docs first (Q8's own condition) |
| 4 | ECharts engagement specifics: year range, sole vs contributor, deliverable scope | Transcript Q15 | The ledger's verb discipline ("built" vs "helped build"). The answerer refused to commit to dates or sole authorship without verifying | NOT in the draft beyond "Earlier in my career I built Eclipse tooling in Xtext for ECharts... I worked on the grammar and editor; I never shipped systems written in ECharts." The verb "built" is author-stated in brief B3. Confirm the verb and whether the paragraph stays at all (brief leaves it to him) |
| 5 | `resolved_by: "user" \| "exhausted"` output field | Transcript Q12; draft, "The boundary test" section | The ledger's false-framing list: a target must not read as current state | Kept, explicitly "I have not built it." Never let an edit read it as shipped |
| 6 | Node-field inferences from Q4: classify setting `call_id` on a regex match, `retrieve` writing `tool_output`/`citations`, the exact summarize-to-retrieve fallback wiring | Transcript Q4 only | "I'm fairly sure... I haven't confirmed" / "I'm inferring" — honest hedges, but unverified | NOT in the draft. The parent-graph paragraph only states what the design doc supports |
| 7 | "My reasoning about the code, not a number": how many rows an edge-only count would miss | Transcript Q13 | None (explicitly not a measurement) | In the draft as mechanism only; no count asserted |
| 8 | The correction narrative itself: "I was reading the design doc's test plan instead of the test file" | Transcript Q14; draft, "Transition coverage" | This is the answering model's account of why Q7/Q10 were wrong. The true reason the panel missed the tests is that the orchestrator's evidence set omitted `tests/agent/` — the model's "I didn't check carefully" framing is a persona-consistent reconstruction, not a documented fact | In the draft in first person. Stephen should confirm he is comfortable owning this framing, or adjust it |

## Upgraded stretches (verified during the loop, no longer to-confirm)

- **draw_mermaid() behavior** (transcript Q11, from memory): verified by running it
  2026-10-06 on a copy of the checkout at b847341 (langgraph 1.2.12). Output and command in
  sources.md, "Local verification runs". The un-checked sub-claim ("an option to expand
  subgraphs") was dropped from the draft.
- **The Q10 boundary test** (drafted as "not yet written"): superseded. The committed test
  exists (`test_after_two_re_asks_every_candidate_runs`), and all 38 agent tests passed when
  run on 2026-10-06. The draft shows the committed test verbatim.

## Corrected errors (where the answering model over-claimed; the author asked to see these)

These were not stretches but wrong statements about the repo, made because the panel's
evidence set lacked `tests/agent/`. All corrected in the council follow-up (Q14) and in the
draft body; the transcript keeps the originals.

1. Transcript Q7: "I can't point you to anything that covers the exhausted re-ask
   fallthrough... I'd call it untested until I add one." Wrong: covered since 2026-10-01.
2. Transcript Q7: the two-term loop "I don't have an item for that either." Wrong:
   `test_two_ambiguous_terms_are_asked_one_at_a_time` exists.
3. Transcript Q7: clarify → clarify "comes from the unit tests only, through the 'resume
   with an invalid choice' case in the L1 test plan" — right direction but understated:
   `test_resume_with_an_invalid_choice_asks_again` plus parametrized malformed-resume tests
   exist and pass.
4. Transcript Q10: presented the boundary test as future work ("I haven't written or run
   this one yet"). It was already committed; the drafted code closely matched the committed
   test. Q14's answer on why ("half-remembering or reconstructing") is itself a `[stretch]`
   (item 8 above).

## What the claims-steward capped and how it resolved

Round 1 claims-steward: 8/10, no hard-cap fails. Its findings (the "18 metric" derivation,
L1-test existence, ECharts verb, results-table metadata) were resolved by enriching
sources.md rows and the two council-routed interview follow-ups. Round 2: 9/10, no hard
fails; residual fix (unless-phrase test name added to the sources row) applied.
