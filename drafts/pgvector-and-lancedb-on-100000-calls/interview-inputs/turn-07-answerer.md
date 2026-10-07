You are answering an interview question as STEPHEN HENDERSON, in first person, his voice:
plain, specific, evidence-first, an experienced field SE/SA and heavy AI practitioner. This
is one isolated call: you do not see other turns, other questions, or any draft.

PURPOSE: your answers are the raw material for a blog post, "pgvector and LanceDB on 100,000
calls". The author's real career experience is larger than his knowledge docs record, so on
experience-and-story questions be LIBERAL, not timid, and reach beyond the documents where it
explains the work. He will clean up or cut anything that overreaches before publication.

RULES
1. Answer only the question asked. First person. No marketing adjectives, no aphorisms,
   no em dashes, no "here's what everyone gets wrong".
2. TECHNICAL NUMBERS must come only from the evidence documents below. Never invent a
   number, latency, recall, or version. If asked for one that is not in the evidence, say
   plainly it is not measured yet / not in hand. Never guess a figure.
3. The claims ledger below says what may be claimed and what never appears. Obey it
   exactly: no client names, no employer names, "a credit union" framing for the project.
   Never write a name from any blocked category (current clients, the 2026 partner-role
   employer and its partners, customers) into your answer.
4. TAG EVERY SUBSTANTIVE CLAIM in your answer with exactly one of:
   [ledger] — supported by the claims ledger
   [repo] — supported by a committed evidence file shown below
   [web] — supported by one of the cited public web sources below
   [stretch] — plausible from experience, but NOT backed by anything in hand. Everything
   that is not squarely in the documents is [stretch]. Say so in the answer itself, briefly,
   and when you stretch name what could prove or correct it (with the author reviewing).
5. Distinguish clearly: what you DID (I/we), what you OBSERVED (the team chose), and what
   belongs to the company/pipeline (never yours).
6. Answer thoroughly enough to mine (150-500 words typical), but do not pad. If a factual
   question cannot be answered from the evidence, say what is missing rather than
   improvising a technical fact.
7. Output ONLY your answer text: no headings like "Answer:", no preamble, no note to the
   orchestrator.

=== CLAIMS LEDGER AND EVIDENCE (attached after this instruction) ===

# EVIDENCE BUNDLE (answerer input)

These are the ONLY evidence documents. Cite numbers from these alone.

## 1. Claims ledger (voice/stephen/claims-ledger.md)

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


## 2. Rendered retrieval results (results/retrieval/README.md — the x80-ivf-pq run only)

## 2026-10-03 · x80-ivf-pq

- git: `cf53abc7d2fd8591abb9c7bd8babed0ed430dca8-dirty`
- hardware: Intel(R) Core(TM) i5-1038NG7 CPU @ 2.00GHz, 8 cores, 15 GB RAM, Linux
- versions: python 3.14.7, model ollama:nomic-embed-text, lancedb 0.39.0, pyarrow 25.0.1, pylance not installed, langchain-postgres 0.0.17, psycopg2-binary 2.9.12, postgres 16.15 (Debian 16.15-1.pgdg12+2), pgvector_ext 0.8.7
- params: backends=["pgvector", "lancedb"], modes=["vector", "fts", "hybrid"], k=10, repeats=3, scale=80, rows=100000, queries=40, ingest_batch=12500, jitter_eps=0.05, index={"pgvector_index": "hnsw", "hnsw_m": 16, "hnsw_ef_construction": 64, "hnsw_ef_search": 40, "sweep": [{"refine_factor": 1}, {"refine_factor": 20}], "lance_index": "ivf_pq"}, labels_sha256=14fc5f3c345dd138

| config | status | index | search | recall@5 | recall@10 | mrr@10 | ann_recall@10 | p50_ms | p95_ms | ingest_s | index_build_s |
|---|---|---|---|---|---|---|---|---|---|---|---|
| pgvector/vector | ok | none (exact scan) | exact | 0.800 | 0.800 | 0.800 | 1.000 | 872.9 | 900.4 | 177.6 | n/a |
| pgvector/fts | not supported: pgvector backend supports mode='vector' only, not 'fts' | n/a | n/a | n/a | n/a | n/a | n/a | n/a | n/a | n/a | n/a |
| pgvector/hybrid | not supported: pgvector backend supports mode='vector' only, not 'hybrid' | n/a | n/a | n/a | n/a | n/a | n/a | n/a | n/a | n/a | n/a |
| pgvector-hnsw/vector | ok | hnsw (cosine, m=16, ef_construction=64) | ef_search=40, iterative_scan=relaxed_order | 0.700 | 0.700 | 0.700 | 0.495 | 3.799 | 92.885 | 177.6 | 27.180 |
| lancedb/vector | ok | ivf_pq (cosine, 316 partitions) | nprobes=default, refine_factor=none | 0.440 | 0.440 | 0.433 | 0.025 | 15.994 | 27.612 | 146.4 | 115.4 |
| lancedb/fts | ok | fts (BM25) |  | 0.675 | 0.675 | 0.675 | n/a | 11.260 | 24.412 | 146.4 | 115.4 |
| lancedb/hybrid | ok | ivf_pq (cosine, 316 partitions) | nprobes=default, refine_factor=none | 0.570 | 0.608 | 0.600 | n/a | 19.495 | 33.024 | 146.4 | 115.4 |
| lancedb[refine_factor=1]/vector | ok | ivf_pq (cosine, 316 partitions) | nprobes=default, refine_factor=1 | 0.450 | 0.440 | 0.450 | 0.025 | 17.470 | 28.538 | 146.4 | n/a |
| lancedb[refine_factor=20]/vector | ok | ivf_pq (cosine, 316 partitions) | nprobes=default, refine_factor=20 | 0.545 | 0.528 | 0.575 | 0.163 | 20.282 | 30.849 | 146.4 | n/a |

**recall@10_by_type**

| config | topical | filter | exact | paraphrase |
|---|---|---|---|---|
| pgvector/vector | 1.000 | 0.700 | 0.600 | 0.889 |
| pgvector-hnsw/vector | 0.818 | 0.800 | 0.500 | 0.667 |
| lancedb/vector | 0.509 | 0.600 | 0.300 | 0.333 |
| lancedb/fts | 0.818 | 0.800 | 1.000 | 0.000 |
| lancedb/hybrid | 0.664 | 0.750 | 0.650 | 0.333 |
| lancedb[refine_factor=1]/vector | 0.509 | 0.600 | 0.300 | 0.333 |
| lancedb[refine_factor=20]/vector | 0.627 | 0.700 | 0.420 | 0.333 |

**mrr@10_by_type**

| config | topical | filter | exact | paraphrase |
|---|---|---|---|---|
| pgvector/vector | 1.000 | 0.700 | 0.600 | 0.889 |
| pgvector-hnsw/vector | 0.818 | 0.800 | 0.500 | 0.667 |
| lancedb/vector | 0.485 | 0.600 | 0.300 | 0.333 |
| lancedb/fts | 0.818 | 0.800 | 1.000 | 0.000 |
| lancedb/hybrid | 0.636 | 0.750 | 0.650 | 0.333 |
| lancedb[refine_factor=1]/vector | 0.545 | 0.600 | 0.300 | 0.333 |
| lancedb[refine_factor=20]/vector | 0.727 | 0.700 | 0.500 | 0.333 |

**p50_ms_by_type**

| config | topical | filter | exact | paraphrase |
|---|---|---|---|---|
| pgvector/vector | 881.0 | 82.406 | 874.9 | 876.3 |
| pgvector-hnsw/vector | 3.767 | 71.103 | 3.349 | 3.477 |
| lancedb/vector | 15.539 | 25.356 | 15.568 | 16.069 |
| lancedb/fts | 12.020 | 22.283 | 10.705 | 7.533 |
| lancedb/hybrid | 19.305 | 31.908 | 19.342 | 18.609 |
| lancedb[refine_factor=1]/vector | 17.308 | 26.975 | 16.862 | 17.038 |
| lancedb[refine_factor=20]/vector | 19.986 | 29.947 | 20.185 | 19.715 |

**p95_ms_by_type**

| config | topical | filter | exact | paraphrase |
|---|---|---|---|---|
| pgvector/vector | 900.4 | 271.8 | 905.8 | 899.2 |
| pgvector-hnsw/vector | 5.357 | 134.7 | 4.329 | 5.589 |
| lancedb/vector | 16.671 | 30.330 | 17.003 | 17.702 |
| lancedb/fts | 15.494 | 25.776 | 13.557 | 10.067 |
| lancedb/hybrid | 22.392 | 36.301 | 22.425 | 20.416 |
| lancedb[refine_factor=1]/vector | 19.940 | 29.856 | 18.876 | 18.506 |
| lancedb[refine_factor=20]/vector | 21.182 | 32.910 | 23.954 | 20.984 |

- embed_unique_docs: 0
- embed_docs_s: 0.000
- embed_query_p50_ms: 44.140

_Notes:_ recall@k is capped recall, |rel ∩ top-k| / min(k, |rel|). Embeddings are precomputed and cached, so ingest_s and latency exclude the embedding model. pgvector builds no vector index (exact scan), so index_build_s is n/a. SYNTHETIC SCALE-UP: the 1250-call corpus replicated 80x with new call_ids; replicas share text and get seeded vector jitter (eps=0.05), so they are near-duplicates, not identical points. All replicas count as relevant, so recall at scale>1 measures retrieval among near-duplicates; compare ingest and latency across scales, not recall.

## 3. Bench design and semantics (retrieval/README.md — key sections)

## Running the bench

`make bench` needs `make up` (Postgres for the pgvector backend; `ARGS="--backends lancedb"`
skips it) and an embedding provider. With `EMBEDDING_PROVIDER=ollama` it uses
`nomic-embed-text` and pulls it on first run if Ollama lacks it: about 270 MB in the `ollama`
volume, skipped once present. Every unique transcript (about 1,060) is embedded once up front,
with a progress line roughly every 10%. On a CPU-only Ollama that step is slow: about 6 minutes
on an 8-core laptop, about 45 minutes on 2 cores.

Reruns reuse the document embeddings cached under `.cache/embeddings/` (gitignored, one file per
embedding model, keyed by text hash), so only the first run pays for the embedding step; `--no-cache` skips the
cache. Ingest goes in batches of `--ingest-batch` rows (default 12,500, the largest single insert proven at x10),
so `--scale 80` never sends 100,000 rows in one call. A backend or mode that fails becomes a one-line
`failed: <error>` row (at most 300 characters), the other backends still run, and the results file is
written either way.

### Fair comparison at scale (`--scale 80`)

A plain `make bench ARGS="--scale 80"` now gives an approximate-vs-approximate comparison, plus the exact
reference:

| row | index | search |
|---|---|---|
| `pgvector/vector` | none (exact scan), the reference | exact |
| `pgvector-hnsw/vector` | HNSW, cosine, `m=16`, `ef_construction=64`, built after ingest (its own `index_build_s`) | `ef_search=40`, `iterative_scan=relaxed_order` |
| `lancedb/vector`, `/hybrid` | IVF-PQ, cosine, sqrt(rows) partitions at >= 100,000 rows, else flat | LanceDB defaults |

Every row records its `index` and `search` settings in the results JSON and table. The settings:

- **pgvector:** `--pgvector-index none` skips the HNSW variant. `--hnsw-m`, `--hnsw-ef-construction` and
  `--hnsw-ef-search` tune it. The HNSW index is a partial index over the bench collection on
  `embedding::vector(<dim>)`, because LangChain's column has no fixed dimension. It is queried with
  bench-only SQL through that expression and dropped afterwards.
- **LanceDB:** `--nprobes`, `--refine-factor` and `--num-partitions` set the IVF-PQ knobs.
- **LanceDB build knobs:** `--num-partitions` and `--num-sub-vectors` (PQ) change the index build.
- **Sweep:** `--sweep` takes `nprobes=...` or `refine_factor=...`. Repeat it to combine the two into a grid,
  for example `--sweep nprobes=20,50,100,200 --sweep refine_factor=1,20`. Each combination is scored on the
  same table, and the run ends with one compact table (recall@10, MRR@10, p50 and p95) that includes the
  pgvector rows for reference.
- **Shared memory:** the Compose `db` service sets `shm_size: 1gb`, because Docker's 64 MB `/dev/shm` is too
  small for a parallel HNSW build at 100,000 rows (`could not resize shared memory segment ... No space
  left on device`). With a small `/dev/shm`, `--pgvector-parallel-workers 0` builds serially; the failure
  row names both fixes.

**What scale runs measure.** Scale runs measure ingest, index build, latency and **`ann_recall@10`**.
Label recall is only meaningful at x1. `ann_recall@10` is the overlap of a vector row's top 10 with a
brute-force exact top 10 that the bench computes itself: the same jittered vectors, the same `where` filter,
and ties at the 10th score counted as hits. On replicated data, label recall mixes replica identity (which of
80 copies came back) with index quality. `ann_recall@10` measures only the index, and an exact scan scores
1.0 by construction.

**More knobs.**
- `--lance-index ivf_pq|ivf_flat|ivf_sq|ivf_hnsw_sq|ivf_hnsw_pq` picks the index type; those are the five
  types the pinned lancedb 0.39 ships.
- `--lance-num-sub-vectors` and `--lance-num-bits` set PQ. They apply to the `*_pq` types only.
- `--sweep ef_search=40,100,200` sweeps pgvector's HNSW on the same index. LanceDB's
  `nprobes`/`refine_factor` grid is swept separately.
- Each row also reports `p50_ms_by_type` and `p95_ms_by_type`.
- Timing starts after one untimed pass over every query.

**Why nprobes did not move recall.** It is applied: the plan shows `ANNIvfPartition ... minimum_nprobes=N,
maximum_nprobes=Some(N)`, and a test asserts it. With 80 near-identical replicas per document, a query's
neighbours sit in one partition, so extra probes find nothing new. On synthetic replicated data, overlap with
the exact top 10 stayed at 2/10 from 1 to all 316 probes. The loss is PQ quantization, which cannot rank
near-identical vectors. `refine_factor`, which re-ranks `k x refine_factor` candidates with exact
distances, or a non-PQ index (`ivf_flat`, `ivf_hnsw_sq`) addresses it.

**Why HNSW p95 >> p50.** The filter queries cause it. With `iterative_scan = relaxed_order`, a selective
`where` keeps walking the graph until k matches pass. In a local x80 run the HNSW p95 was 37.8 ms for filter
queries vs 1.1 ms for every other type. A cold cache is not the cause: timing follows a full warm-up pass.

**Replica jitter.** `--scale N` repeats the same texts with new ids. Identical vectors made k-means
degenerate (empty clusters, "many duplicate vectors"), so for `--scale > 1` each repeat now gets a seeded
jitter of norm `--jitter-eps` (default 0.05, cosine to the original about 0.999), renormalised. The first
copy and every query vector are unchanged, and `--scale 1` is unchanged. `--no-jitter` turns it off.
All replicas still count as relevant, so recall at scale measures ranking among near-duplicates: compare
ingest and latency across scales, not recall.

**What a local run showed.** These are offline hashed embeddings at x80, so the absolute scores mean nothing.
The `nprobes` sweep did not move LanceDB's recall (0.225 at 5, 20 and 50 probes), while `refine_factor=20`
lifted it to the exact-scan level (about 0.30–0.33 vs 0.300). That points at PQ compression rather than
probing as the x80 recall gap. Check it on real embeddings with `--sweep refine_factor=1,5,10,20`.

(section Fair comparison at scale (`--scale 80`) not found)

(section What scale runs measure. not found)

(section Why nprobes did not move recall. not found)

(section Why HNSW p95 >> p50. not found)

(section Replica jitter. not found)

## LanceDB on az:// (ADLS Gen2)

`make lance-azure-check` runs the bench's LanceDB half twice, on a local temp directory and on
`az://lance/<prefix>` in the envs/dev ADLS account, then prints recall@10 and p50 side by side.
Both stores share the same corpus, labels and embeddings (each unique document is embedded once), so the only
difference is the storage. The filter queries exercise the metadata prefilter. The Azure table is
dropped afterwards (`--keep` leaves it), and nothing is written to `results/`.

```bash
az login                                                   # the account must hold Storage Blob Data Contributor
make lance-azure-check ARGS="--dry-run"                    # the plan; touches nothing
make lance-azure-check                                     # account from AZURE_STORAGE_ACCOUNT_NAME or terraform output
```

**Auth.** Entra only; the account has shared keys off, and no account key is ever used.
`retrieval/lance_azure_check.py` passes `azure_storage_account_name` plus
`azure_use_azure_cli=true` as `storage_options`, so Lance's object store (the Rust `object_store`
crate) gets a storage token from `az` for the signed-in user. If that is refused, set
`AZURE_STORAGE_SAS_KEY` to a container-scoped SAS (keep it in 1Password, for example
`op://CMW/azure-ccai`) and rerun; the SAS is used instead.
Blank `AZURE_*` values (a `.env` copied from `.env.example` has empty `AZURE_TENANT_ID`,
`AZURE_CLIENT_ID` and `AZURE_CLIENT_SECRET`) are dropped before connecting, and with `az login` auth so are
those three when they come from `.env`, so they cannot switch the store to a client-secret flow.
Embeddings come from the same `.cache/embeddings/` cache as `make bench` and `make ingest` (`--no-cache` skips it).

**What the results mean.** Recall should match the local numbers exactly, because the data, vectors and queries are the same. A difference would point at the storage path, not the retrieval. Latency is higher on az:// because every read is a network round trip; that gap is the cost of object storage over local disk, not a recall tradeoff.

**Verified (2026-10-03):** a string check of the pinned lancedb 0.39.0 native library shows the Azure object-store options; a full run on az:// against the Azurite emulator matched local disk recall@10 in every mode. **Not run, so not claimed:** a real ADLS account; DefaultAzureCredential.

## 4. Benchmark labels (retrieval/labels/README.md — key sections)

# Retrieval benchmark labels (R3)

`queries.jsonl` holds 40 labeled queries for `make bench`. Each line looks like this:

```json
{"id": "q15", "type": "filter", "query": "escalated credit card limit questions in July",
 "where": {"outcome": "escalated", "date": {"gte": "2026-07-01", "lte": "2026-07-31T23:59:59"}},
 "relevant": ["CALL-…", …],
 "derivation": "category = card_services AND outcome = escalated AND 2026-07-01 <= date <= 2026-07-31T23:59:59"}
```

## How the relevant sets are made

`build_labels.py` computes every relevant set from the seed-42 generator's own output. No retriever is imported or run, and nothing was picked by hand from search results. A query's relevance is a predicate over facts the generator wrote per call:

- `category`, `outcome` and `date`, from `transcripts.json`.
- `member_id`.
- The subject account and its product line (LOB), from `interaction_accounts.json`, `accounts.json` and `products.json`.
- Which template placeholders the call's category speaks aloud, from `CATEGORY_DIALOGUES` in the generator.

`derivation` records that predicate in readable form.

```bash
make seed                                          # deterministic, so the labels are too
python -m retrieval.labels.build_labels            # rewrite queries.jsonl
python -m retrieval.labels.build_labels --check    # exit 1 if queries.jsonl is stale
```

`tests/test_labels.py` re-derives the file and fails if it differs. The tests also check the schema, check that every relevant call passes its query's `where`, and check that every id query's id appears verbatim in each relevant transcript.

## Query types

| type | n | what it tests | how relevance is derived |
|---|---|---|---|
| topical | 11 | Naming a call's subject in the corpus's own words. Four of these add a product facet (home-equity rate, mortgage vs card hardship, fraud on checking) that only the LOB cue words separate. | `category`, plus `lob` for the four product-facet queries |
| filter | 10 | Structured constraints the text cannot carry (outcome and date are never spoken), passed as `where`. Two queries put every constraint in `where` (q12, q13), which tests prefiltering alone. The other eight leave the category to the text, so the backend must rank the right category first inside the filtered pool. | `category` AND the `where` |
| exact | 10 | Verbatim strings, where lexical (FTS) search should win. Six are phrases from exactly one category's template; the builder asserts that no other category's template contains them. Four are ids spoken in the call: two member ids and two "account ending in NNNN" queries. | `category`, or the member or subject account restricted to categories whose template speaks that id |
| paraphrase | 9 | The same intents in other words, where vectors should win. The builder asserts that each query shares at most one content word with its target template; all nine share none. | `category` |

The id queries pick the two members and the two accounts with the most calls that speak the id, breaking ties on the id. That keeps the choice deterministic.

Notes on specific queries:

- "Last week" (q14) means Mon 2026-08-24 to Sun 2026-08-30, the week before the generator's fixed `REFERENCE_DATE` of 2026-09-01.
- "This summer" (q16) means June to August.

## What recall can and cannot measure here

- **The corpus is templated.** With names and figures masked, the 1,250 transcripts reduce to fewer than 30 distinct texts, one to five per category (`docs/design/finetune.md`, "Corpus limits"). Within a category, calls differ only in names, ids, figures and the LOB label words. So:
  - Topical, paraphrase and exact-phrase recall measure category discrimination: whether the top-k are from the right category. Ranking among the many equally relevant calls of one category is arbitrary, and no metric here rewards it.
  - The product-facet queries are the only test of finer distinctions, and those rest on a few label words ("drawn balance", "minimum payment", "ledger balance").
  - Id queries are the only queries with a single right answer per call, and lexical search has a structural edge on them.
- **Outcome is never spoken.** No text-only search can find "escalated" calls. Filter queries therefore rely on `where`, and their scores measure prefiltering plus category ranking inside the filtered pool.
- **Recall is capped.** Most relevant sets are larger than k (up to 179 calls), so recall@k is |relevant ∩ top-k| / min(k, |relevant|), BEIR's capped recall. Under it, a top-k made entirely of relevant calls scores 1.0.
- **The numbers do not transfer.** Real transcripts are not templated. These numbers compare backends and modes on this corpus; they say nothing about absolute quality on real calls.



## 5. Evals

### Evals evidence (single open-question runs, results/evals/README.md, 2026-10-05)
- open-pgvector-vector: n=12, judge 0.625 (scaled score), retriever pgvector, mode vector, k=5.
- open-lance-hybrid: n=12, judge 0.479 (scaled), retriever lancedb, mode hybrid, k=5.
- Both runs: agent_model z-ai/glm-5.3-flash, judge anthropic/claude-opus-5.5, judge_prompt v1, embedding ollama nomic-embed-text. Single runs of 12 items each: too few to rank. The ledger's line: "single runs of 12 (pgvector 0.63, LanceDB hybrid 0.48) are too few to rank" — 0.63/0.48 are the raw doubles rounded; rendered tables show 0.625 and 0.479.


## 6. Web research

### Web research notes (public sources, accessed 2026-10-06)
1. LanceDB Python API reference, lancedb.github.io/lancedb/python/python/ — LanceVectorQueryBuilder.refine_factor: "Set the refine factor to use, increasing the number of vectors sampled. As an example, a refine factor of 2 will sample 2x as many vectors as requested, re-ranks them, and returns the top half most relevant results." Signature: refine_factor(int) -> LanceVectorQueryBuilder.
2. pgvector README (github.com/pgvector/pgvector/blob/master/README.md), HNSW + Iterative Index Scans: with approximate indexes, filtering is applied AFTER the index is scanned; if a condition matches 10% of rows, with HNSW and default hnsw.ef_search=40 only ~4 rows match on average. Iterative index scans (SET hnsw.iterative_scan = relaxed_order) automatically scan more of the index until enough results are found, at better recall than strict ordering.


=== CURRENT QUESTION ===
The first result was a 49x gap that you threw out, and your answer to the architect said the ledger only records "a non-comparable run." Put a number on it: which two rows produced the 49x, what were the two latencies, and what was the one setting or mismatch you can point to in that early run (for example, an index on one side and an exact scan on the other) that you changed to get the corrected rows?
