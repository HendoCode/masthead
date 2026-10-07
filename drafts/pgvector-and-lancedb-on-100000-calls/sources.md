# Sources & Handoff — pgvector-and-lancedb-on-100000-calls

Everything the draft rests on, with citations, plus the council record and the pre-publish
checklist. Written so the author can edit the draft directly without losing provenance.

## Research citations (every figure in the draft traces here)

### Figures table (checked against the rendered files on 2026-10-06)
| Figure in draft | Source row |
|---|---|
| 872.9 / 900.4 ms p50/p95, label recall@10 0.800, ann_recall 1.000, ingest 177.6 s (pgvector exact scan) | `results/retrieval/README.md`, 2026-10-03 · x80-ivf-pq, row `pgvector/vector` |
| 3.799 / 92.885 ms, 0.700, 0.495, ingest 177.6 s, index build 27.180 s (pgvector HNSW) | same file, row `pgvector-hnsw/vector` |
| 15.994 / 27.612 ms, 0.440, 0.025, ingest 146.4 s, index build 115.4 s (LanceDB default IVF-PQ) | same file, row `lancedb/vector` |
| 19.495 / 33.024 ms, 0.608 (LanceDB hybrid) | same file, row `lancedb/hybrid` |
| 11.260 / 24.412 ms, 0.675 (LanceDB BM25 fts) | same file, row `lancedb/fts` |
| 20.282 / 30.849 ms, recall@10 0.528 (recall@5 0.545), ann_recall 0.163 (refine_factor=20); 17.470 / 28.538, 0.440, 0.025 (refine_factor=1); +4.288 ms derived from 15.994 → 20.282 | same file, rows `lancedb[refine_factor=20]/vector` and `lancedb[refine_factor=1]/vector` |
| per-type recall@10: pgvector/vector 1.000 / 0.700 / 0.600 / 0.889; pgvector-hnsw 0.818 / 0.800 / 0.500 / 0.667; lancedb/vector 0.509 / 0.600 / 0.300 / 0.333; lancedb/fts 0.818 / 0.800 / 1.000 / 0.000; lancedb/hybrid 0.664 / 0.750 / 0.650 / 0.333 | same file, table `recall@10_by_type` |
| per-type p50 filter rows: pgvector-hnsw filter 71.103 ms; lancedb/vector filter 25.356 ms; lancedb/hybrid filter 31.908 ms; 3.349-3.477 ms other pgvector-hnsw types | same file, table `p50_ms_by_type`; PG HNSW non-filter types 3.349 / 3.477 |
| query embedding p50 44.140 ms; ~1,060 unique texts embedded | same file, notes + `embed_query_p50_ms`; `retrieval/README.md` |
| params: k=10, repeats=3, scale=80, rows=100000, queries=40, ingest batch 12,500, jitter 0.05, IVF-PQ 316 partitions | same file, `params` |
| hardware + versions: Intel Core i5-1038NG7 @ 2.00GHz, 8 cores, 15 GB RAM, Linux; Postgres 16.15; pgvector_ext 0.8.7; lancedb 0.39.0; python 3.14.7; ollama:nomic-embed-text; git cf53abc7d2fd8591abb9c7bd8babed0ed430dca8-dirty | same file, run header |
| nprobes flat 2/10 overlap from 1 to 316 probes; local hashed run 0.225 at 5/20/50 probes; refine_factor=20 reaching exact-scan level on hashed run | `retrieval/README.md`, sections "Why nprobes did not move recall." and "What a local run showed." |
| label formula (capped recall), ann_recall definition, scale-run semantics, all replicas relevant | `retrieval/README.md`, section "What scale runs measure."; `retrieval/labels/README.md` |
| BM25 all-exact / no-paraphrase; paraphrase target-word assertion (all nine share none) | `results/retrieval/README.md` by-type table; `retrieval/labels/README.md` |
| evals single runs: open-pgvector-vector judge 0.625 n=12, open-lance-hybrid judge 0.479 n=12 (kept OUT of the body; see GAP in draft editorial block) | `results/evals/README.md`, 2026-10-05 runs |
| per-type p95: pgvector-hnsw filter 134.7 ms vs about 5 ms on its other types; lancedb/vector filter p50 25.356 ms and p95 30.330 ms | same file, tables `p95_ms_by_type` and `p50_ms_by_type` |
| langchain-postgres 0.0.17 (the pgvector ingest path) and pylance not installed | same file, run header `versions` |
| embedding cache path and keying: `.cache/embeddings/`, one file per embedding model, keyed by text hash, reused on reruns | `retrieval/README.md`, section "Running the bench" |
| the bench documents an HNSW `ef_search` sweep: `--sweep ef_search=40,100,200` sweeps pgvector's HNSW on the same index | `retrieval/README.md`, section "More knobs." |
| 49x first result was a non-comparable run, thrown out (the body's source for the figure) | `voice/stephen/claims-ledger.md` ("The 49x first result") |
| Timing starts after one untimed pass over every query | `retrieval/README.md`, section "More knobs." |
| A failing backend or mode becomes a one-line failed row; the other backends still run | `retrieval/README.md`, section "Running the bench" |
| 1,250-call corpus and label type counts (topical 11, filter 10, exact 10, paraphrase 9) | `retrieval/labels/README.md`, "Query types" table; `results/retrieval/README.md` rows=1250 at x1 |
| Compose db service `shm_size: 1gb`; Docker default 64 MB too small for a parallel HNSW build at 100,000 rows | `retrieval/README.md`, section "Fair comparison at scale" |
| HNSW index is partial over `embedding::vector` because the LangChain column has no fixed dimension; bench-only SQL; dropped afterwards | `retrieval/README.md`, section "Fair comparison at scale" |
| pgvector mechanism for filter latency: filtering is applied after the index scan; `iterative_scan=relaxed_order` scans more until k matches | pgvector README, section "Iterative Index Scans" — https://github.com/pgvector/pgvector/blob/master/README.md — accessed 2026-10-06 |
| The reproduction command `make bench ARGS="--scale 80"` | `retrieval/README.md`, section "Fair comparison at scale (`--scale 80`)" |
| The refine-factor sweep grid: the run's params record `"sweep": [{"refine_factor": 1}, {"refine_factor": 20}]`; the flag form `--sweep refine_factor=1,20` is the README's documented example | `results/retrieval/README.md` params block; `retrieval/README.md`, section "Fair comparison at scale" ("Sweep") |
| The default and refine_factor rows share the same index cell ("ivf_pq (cosine, 316 partitions)") and one ingest (ingest_s 146.4 on all three rows); the refine rows list index_build_s n/a, the sweep reuses the index | `results/retrieval/README.md` rows `lancedb/vector`, `lancedb[refine_factor=1]/vector`, `lancedb[refine_factor=20]/vector` |
| 17.470 − 15.994 = 1.476 ms ("within 1.5 ms" claim) | derived from `results/retrieval/README.md` rows `lancedb/vector` and `lancedb[refine_factor=1]/vector` |
| `queries.jsonl` schema: query text, optional where, relevant ids, type, derivation | `retrieval/labels/README.md`, top section (the example record) |
| Six exact phrases asserted single-category, four ids chosen deterministically; paraphrase target-word assertion | `retrieval/labels/README.md`, "Query types" table and "Notes on specific queries" |
| Corpus metadata is correct and complete for filters: outcome and date are never spoken and label derivation uses the generator's own fields | `retrieval/labels/README.md`, "Query types" and "How the relevant sets are made" |
| pgvector backend supports mode='vector' only (its fts and hybrid rows say so) | `results/retrieval/README.md`, rows `pgvector/fts` and `pgvector/hybrid` |
- Web sources used during interviews (public sources only):
  - LanceDB Python API reference, `LanceVectorQueryBuilder.refine_factor` — https://lancedb.github.io/lancedb/python/python/ — accessed 2026-10-06
  - pgvector README, HNSW filtering and Iterative Index Scans — https://github.com/pgvector/pgvector/blob/master/README.md — accessed 2026-10-06
- Evidence files (contact-center-ai checkout at /home/firstmate/firstmate/projects/contact-center-ai, read 2026-10-06):
  - `results/retrieval/README.md` (rendered from the run JSONs; 2026-10-03 · x80-ivf-pq run, git cf53abc7d2fd8591abb9c7bd8babed0ed430dca8-dirty)
  - `results/retrieval/2026-10-03_x80-ivf-pq.json`
  - `retrieval/README.md` (bench prerequisites, fair comparison at scale, scale-run semantics, az:// check, DuckDB stretch)
  - `retrieval/labels/README.md` (labels build, query types, what recall can and cannot measure)
  - `results/evals/README.md` (single open-question runs: open-pgvector-vector judge 0.625, open-lance-hybrid judge 0.479; n=12)

## Must not claim
(Copied verbatim-ish from post brief B4, blogs/PROPOSED_POSTS.md, at intake 2026-10-06. Enforced by editors/claims-steward.md.)
1. Anything about scale beyond 100,000 rows, multi-node behavior, compaction or versioning (none were measured).
2. The `ivf_flat` result (exact at about 55 ms p50) until that run is committed to `results/retrieval/`. (The 2026-10-03 · bench-pgvector-lancedb-x80 run exists with ivf_flat rows, but the brief's named run for the post is the x80-ivf-pq run; treat the 55 ms figure as not claimable.)
3. Presenting IVF-PQ's default recall as a flaw: it is a default tuned for larger datasets, measured here at a small one.
4. A ranking from the agent-level finding: on single runs of 12 open questions, LanceDB hybrid judge 0.479 vs pgvector vector 0.625. If mentioned at all: "no lift on a small sample", never a ranking. (Open item: it may belong to post 10 instead.)

## Council record

Council: slop-allergist, voice-guardian, presentation-reviewer, claims-steward (mandatory),
plus technical-reviewer and specificity-auditor for a technical measurement piece (6 total).
Scores by round:

| Round | slop-allergist | voice-guardian | presentation-reviewer | claims-steward | technical-reviewer | specificity-auditor | aggregate |
|---|---|---|---|---|---|---|---|
| 1 | 5 | 6 | 6 | 5 | 6 | 8 | 6.0 |
| 2 | 5 | 6 | 7 | 6 | 6 | 8 | 6.3 |
| 3 | 5 | 7 | 7 | 6 | 6 | 7 | 6.3 |
| 4 | 6 | 6 | 7 | 5 | 6 | 8 | 6.3 |
| 5 | 6 | 6 | 6 | 5 | 6 | 8 | 6.2 |
| 6 | 6 | 7 | 7 | 5 | 6 | 8 | 6.5 |
| 7 | 6 | 7 | 7 | 5 | 6 | 8 | 6.5 |
| 8 | 6 | 7 | 7 | 6 | 6 | 8 | 6.7 |
| 9 | 6 | 7 | 7 | 6 | 6 | 7 | 6.5 |

Terminal state: the loop converged on a reviewable draft that does not reach the 9/10
publishing bar. The standing caps are the author-gated items: the unconfirmed [stretch]
verdict pick, the 49x correction lacking the early run's rows/latencies/settings/date, and
the stack of honest-limit hedges around the PQ diagnosis. The task brief states the
claims-steward cap on [stretch] claims is expected and reported as a to-confirm list
(claims-review.md). Raw editor outputs are archived with the task data, not committed.

Applied fixes across rounds: rewrote all contrast constructions and hedges to the voice
pack; replaced the unsupported dek with measured facts; split dense paragraphs into tables
(config rows, per-type recall, run environment); added the sources rows for every figure
and derived number; added the named verdict via council-routed turn-15; moved publishing
hygiene notes into the editorial block.

## Pre-publish checklist
1. Open GAPs from the draft's editorial block: 49x mechanics; agent-level eval placement; committing the ivf_flat run; scale-1 table; exact-scan parallelism; author to confirm.
2. Clearances: every named person, client, and figure confirmed for publication. The transcript was model-answered on the author's behalf; the author reviews every [stretch] tag in claims-review.md before anything ships.
3. Diagrams: any inlined SVG redrawn from a current source.
4. Run `make check-public` in the contact-center-ai repo (the deterministic blocked-strings gate) before publishing.
5b. The x80-ivf-pq run's git state is `-dirty`. Author decides whether the post discloses that or the run gets rerun from a clean commit.
5. The rendered results file may be replaced by a newer run before publication. Re-read `results/retrieval/README.md` and re-check every figure in the figures table above.

## After you publish
Come back and run Step 7 (Lessons): the machine diffs the published version against the
draft, proposes generalizable lessons, and on an explicit yes appends them to
voice/stephen/content-lessons.md.

## Council record, v2 redraft (2026-10-07)
One inline combined round, no external model; headline-utility added. slop-allergist 9,
voice-guardian 9, presentation-reviewer 9, claims-steward 6, technical-reviewer 9,
specificity-auditor 9, headline-utility 10. Aggregate 8.71. The steward cap is the expected one
(author-unconfirmed `[stretch]` verdict and next-tests); no blocked name, status upgrade, or
unsourced number. Changes: all titles and headings rewritten as literal and functional; the
by-type recall table and the headline results are told as what the run showed, with figures
re-read from results/retrieval/README.md at b847341 (x80-ivf-pq run, 2026-10-03); four `[device]`
analogies added. The soft word band was not used to cut stories or analogies.
