# Claims review — pgvector-and-lancedb-on-100000-calls

Everything in this piece was answered by a model on Stephen Henderson's behalf. This file
lists every `[stretch]` claim (plausible but not backed by a committed file, the ledger, or a
cited public source), where it appears, and which ledger line or status word it strains. The
author reviews this list before anything publishes; the claims steward caps the draft until
then (expected per the task brief: the cap on `[stretch]` claims is the mechanism, not a bug).

## The verdict (the big one)

- **Claim:** "I pick pgvector with its HNSW index" for the vector-search path at 100,000 rows,
  with flips: a mostly filtered workload moves the pick to LanceDB's vector row, and a path
  needing hybrid or BM25 in one store flips it outright.
- **Where:** transcript turn-15 (council-routed question); draft "Choosing at this size";
  editorial claims register row "Named verdict".
- **Strains:** ledger "Status words" (a first-person pick the author has not made);
  "Framings that are false even when each fact is true" only in the worst reading (a vendor
  pick published while courting; the ledger says a public post describes a product or
  standard, never argues for a vendor). The cited numbers behind the pick are `[repo]`; the
  pick itself is the model's. **Author action: confirm, reword as "the numbers favor X under
  condition Y", or cut.**

## Field-experience claims (contact-center practitioner observations)

- **Claim:** QA and compliance analysts look for disclosure wording, dispute reason codes,
  and outcome flags; the "exact" label type is the closest analogue to a required disclosure
  line.
- **Where:** transcript turn-04; the draft's by-type table exposition relies on the
  exact-type framing.
- **Strains:** ledger "Framings that are false" (invented observations). **Author action:
  confirm from field work or attribute, or cut.**

- **Claim:** In field time on the contact-center side, agent-entered disposition codes are
  the least reliable metadata; a practitioner would test filters on noisy metadata before
  trusting the filtered latency figures.
- **Where:** transcript turn-05; draft "Next tests" item 5 (noisy-metadata variant).
- **Strains:** ledger "Framings that are false" (invented observation), "Attribution"
  (observed vs did). **Author action: confirm, add the specific, or cut.**

- **Claim:** A practitioner reading the two recall columns would assume both share one
  ground truth and subtract them.
- **Where:** transcript turn-06. Kept OUT of the draft body; lives only in the transcript.
- **Strains:** invented observation. **Author action: confirm or drop.**

## Methodology readings the draft kept

- **Claim:** Tie handling among the 80 near-duplicate replicas is the likely reason HNSW's
  filter label recall (0.800) can pass the exact scan (0.700).
- **Where:** draft "Two recall numbers" ("I did not test why... my hypothesis"); transcript
  turns 10-11.
- **Strains:** nothing directly; it is stated as a hypothesis. **Author action: confirm the
  wording reads as a hypothesis, or supply the replica-collapse test result.**

- **Claim:** `ann_recall@10` is a ranker-reproduction metric; two equally good retrievals can
  return different replicas and score low, so 0.025 and 0.495 mix index quality with replica
  identity.
- **Where:** draft "Index settings and their costs"; transcript turn-10.
- **Strains:** no ledger block; it strains the brief's "Must not claim" only if read as a
  flaw verdict on LanceDB (the body keeps the required framing). **Author action: confirm.**

- **Claim:** The default IVF-PQ recall is "sized for larger data, measured at a small one"
  and no committed test isolates that from the replication structure.
- **Where:** draft "Index settings and their costs"; transcript turn-12.
- **Strains:** none; this is the ledger's own fixed wording plus a stated limit. Listed here
  because the answerer originally glided past the "no committed evidence" part; the draft
  now carries both. **Author action: none required beyond a read.**

- **Claim:** Planning reads: 134.7 ms filter p95 is the real bound for a filtered workload;
  a single run per config is too few to establish the 4.3 ms refine cost.
- **Where:** transcript turn-14; draft "Next tests" and refine paragraph.
- **Strains:** ledger "Time" (dated status). **Author action: confirm the planning phrasing.**

## Proposals that are plans, not results

- **Claim:** The six "Next tests" (scale-1 rerun, committed non-PQ run, replica-collapse
  rescore, ef_search sweep, memory/disk footprint, larger corpus sizes).
- **Where:** draft "Next tests"; transcript turns 9, 10, 11, 12.
- **Strains:** ledger "Time" and "Status words" ("planned", not run; targets not current
  state). The draft says "planned, not run" for the larger-sizes item and "as of 2026-10-06"
  for the uncommitted controls. **Author action: confirm the list is his; anything he would
  not run comes out.**

- **Claim:** The narrowed thesis from turn-13 (index and settings are the dominant visible
  latency lever within one backend in this run).
- **Where:** transcript turn-13 only; the draft dek was reworded to latency facts so this
  does not ship as a claim.
- **Strains:** would strain nothing if kept; it was dropped for the unsupported comparative
  form. **Author action: decide whether the thesis sentence returns once scale-1 recall
  exists.**

## Non-claims kept for trace

- turn-01 `[stretch]` tag: the answerer declined to name the retriever class from memory.
  Not a claim; the class name is in `retrieval/base.py` and can be confirmed from the repo.

## How the council responded (caps to expect until the author acts)

- claims-steward rounds 1-9: capped at 5-6 throughout on the unconfirmed pick and the
  unsourced-number/unsourced-row findings; the figures and rows were fixed across rounds, so
  the terminal cap is the pick plus the 49x row details.
- slop-allergist: capped at 6 mainly on the 49x section (a number whose rows, latencies, and
  settings are not in any cited file) and on a couple of borderline contrast constructions,
  later fixed.
- technical-reviewer: 6, on the pick leaning on columns the bench disclaims at scale and on
  things only the author can supply (ef_search choice, parallelism).
- voice-guardian 7, presentation-reviewer 7, specificity-auditor 7-8.
- Aggregate across the final rounds sits near 6.5/10 and does not reach the 9/10 publishing
  bar; per the task brief, that is the expected terminal state for a draft carrying
  author-unconfirmed `[stretch]` claims. The council record lives in
  `drafts/../sources.md` and the raw editor outputs are in the task's data directory,
  `ccai-post-pgvector-lancedb/council/`.
## Teaching devices (`[device]`, 4 items, v2 redraft 2026-10-07)

Editorial analogies, not claims; none carries a number or evidence. Each is followed in the
body by where it stops being accurate.

| # | Analogy | Concept it teaches | Where it stops (stated in the body) |
|---|---|---|---|
| 1 | Teacher's answer key vs speed-reader | Label recall vs ann_recall | The slow read scores 0.800 against the key |
| 2 | Library with "see also" notes | Exact scan vs HNSW | ef_search candidate list (left at 40) |
| 3 | Nearest open coffee shops | Post-filtering, filtered-query tail | The walk is inside the index; not isolated by the bench |
| 4 | Coarse facial features and twins | PQ on near-duplicate replicas | Mechanism differs; PQ diagnosis untested here |

No new first-person anecdote was added. The `[stretch]` items are unchanged (2 register rows: the
named verdict and the next-tests list), and they remain what holds the claims-steward at 6.
