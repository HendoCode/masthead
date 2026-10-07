# Interview Transcript — pgvector-and-lancedb-on-100000-calls

> **BANNER: This transcript was answered by a model on Stephen Henderson's behalf. It has NOT
> been verified by the author.** Substantive claims are tagged `[ledger]`, `[repo]`, `[web]`,
> or `[stretch]`. `[stretch]` claims are plausible but not backed by anything in hand; the
> author will review them before anything publishes (see `claims-review.md`).

Source material for this piece. The draft traces back to this file: every claim, number,
story, and example must appear here or be cited in sources.md.

Roster run: architect, domain-specialist, tactician, skeptic (4 of 4, per interviewers/README.md fit: building/architecture + one deep technical domain + claims stress-test).
Interview isolation: each interviewer call and each answerer call is a separate one-shot process
(see ISOLATION-PROOF.md in the task's data dir); exact per-turn prompts live in interview-inputs/.

---

## turn-01 · architect

**Q — verbatim as asked**

Walk me through the benchmark harness piece by piece: what are the components between a labeled question and a recall number, and what talks to what? Specifically, where does the single retriever interface sit, what does each backend (pgvector exact, pgvector HNSW, LanceDB IVF-PQ, LanceDB BM25) implement behind it, and where do the embeddings, the 100,000 transcripts, and the 40 labels each live?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

Start from the labels. Forty labeled queries live in `retrieval/labels/queries.jsonl`, one record per query with the query text, an optional `where` clause, the relevant call ids, its type, and a derivation predicate [repo]. `retrieval.labels.build_labels` computes those relevant ids from the seed-42 generator's own output, not from any retriever result [repo]. The underlying corpus is 1,250 generated calls. At scale 80 the harness replicates those texts 80 times with new call_ids and a seeded vector jitter of 0.05, giving 100,000 rows [repo]. Those 100,000 rows live in the stores after ingest, not as 100,000 distinct transcript files on disk [repo]. Ingest runs in batches of 12,500 rows, so a scale 80 run never sends 100,000 rows in one call [repo].

The embeddings live separately in `.cache/embeddings/`, one file per embedding model keyed by text hash. Only the roughly 1,060 unique transcript texts get embedded once; reruns reuse the cache. The run in hand used ollama nomic-embed-text, and ingest and latency timings exclude the embedding step [repo]. Query vectors are produced at query time through the same model; the reported query embedding p50 was 44.140 ms [repo].

The single retriever interface sits between that shared input pipeline and the store clients. The contract is visible in the results table: each backend plus mode is one row with the same keys, gets a query, optional `where`, and k=10, and returns ranked call ids. A backend or mode that fails becomes a one-line failed row without stopping the others [repo]. The harness handles embedding, batching, index build, timing, and scoring outside that contract. The exact class name is not quoted in the evidence in front of me, so I will not name it; the retrieval bench runner is where I would confirm it [stretch].

Behind that interface, pgvector implements vector mode only. `pgvector/vector` uses no index and does an exact scan, which is the reference row [repo]. `pgvector-hnsw/vector` builds a partial HNSW index over `embedding::vector` after ingest, cosine, m=16, ef_construction=64, then queries with ef_search=40 and `iterative_scan=relaxed_order` [repo]. It talks to the Compose Postgres, whose `shm_size` is set to 1gb because Docker's default shared memory is too small for a parallel HNSW build at 100,000 rows [repo]. LanceDB implements vector and hybrid on `ivf_pq`, cosine, with sqrt(rows) partitions, which is 316 at 100,000 rows, and default nprobes [repo]. `refine_factor` can be set to sample more candidates and re-rank them with exact distances [web]. LanceDB `fts` is the BM25 lexical path, separate from the vector index [repo]. The hybrid row lists `ivf_pq` as the vector-side index; the table does not give me more detail than that [repo].

After a backend returns top-k, the harness computes capped recall@5/10 as |relevant ∩ top-k| / min(k, |relevant|), plus MRR@10 and p50/p95 latency. At scale it also computes ann_recall@10 by comparing a backend's top 10 against a brute-force exact top 10 the bench computes itself over the same vectors, filter, and tie handling; an exact scan scores 1.0 by construction [repo]. Label recall is meaningful at scale 1, not on the replicated 100,000 rows [repo].

## turn-02 · architect

**Q — verbatim as asked**

You said label recall is only meaningful at scale 1, not on the replicated 100,000 rows. Walk me through what that does to the headline numbers: at scale 80, each text exists 80 times with new call_ids and 0.05 vector jitter, so which relevant ids are the 40 labels checked against, and how does the recall@10 of 0.80 for the exact scan come out of that? Where does that design constrain what "recall against the labels" can claim in the post?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

At scale 80 the 40 labels still carry the relevant call ids produced by the seed-42 generator at scale 1. The bench then treats every replica of a relevant call as relevant too, so the scoring pool is the whole 80-copy family tied to each label, not just the original id. [repo] The first copy and the query vectors are unchanged; the other copies get seeded jitter at eps 0.05, so they are near-duplicates, not identical points. [repo]

That changes what recall@10 means. It is the capped recall formula, |relevant ∩ top-k| divided by min(k, |relevant|), with k set to 10. [repo] The pgvector/vector exact scan gets recall@10 0.800 overall. By type it is topical 1.000, filter 0.700, exact 0.600, paraphrase 0.889. [repo] Those numbers say whether the top 10 hits came from the relevant near-duplicate families; they do not say the bench recovered the single original call id, and they do not measure category discrimination the way the scale-1 labels do. [repo]

So the 0.80 is not "recall on 100,000 calls" in any real sense. It is label recall on an 80x replication with jittered near-duplicates and all copies counted as relevant. [ledger] My constraint for the post is to keep that distinction explicit: scale runs support ingest, index build, latency, and ann_recall@10 comparisons, where the exact scan scores ann_recall@10 1.0 by construction. [repo] Label recall is only meaningful at scale 1. [repo] I can present the 0.80 only as a reference point on this synthetic duplicated corpus, not as retrieval quality against the labels, and definitely not as evidence about how pgvector would perform on real transcripts. [ledger]

## turn-03 · architect

**Q — verbatim as asked**

You said the 49x first result was thrown out, but nothing in the harness walkthrough explains it. What was the original setup that produced it, which component was wrong (the interface, index build, filter handling, or the timing path), and what did you change in the design so the comparison between pgvector and LanceDB became like-for-like?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

The 49x figure came from an early run before the harness enforced a fair comparison, and I threw it out because the two backends were not measuring the same thing. That is the whole of what the ledger carries: a non-comparable run, reported and then withdrawn as a correction. [ledger] Which component produced it, whether it lived in the interface, the index build, the filter handling, or the timing path, is not recorded in the evidence I have for this call, so I will not assign it to one of those four. What would settle it is the original run's parameters and result row, if the commit history still shows them, with the author reviewing before publication. The harness walkthrough does not explain it because the walkthrough documents the corrected design, and the correction line lives only in the ledger. [ledger]

What I can say concretely is what the current design does to keep the rows comparable, because that is committed in the bench README. [repo]

Every row records its index and search settings. pgvector gets two rows: an exact scan as the reference, and an HNSW row at m=16, ef_construction=64, ef_search=40, built after ingest with its own index_build_s. LanceDB rows run IVF-PQ with the same class of knobs exposed, nprobes, refine_factor, and num_partitions. So the comparison is approximate versus approximate, with one exact reference, instead of two unnamed defaults pretending to be comparable. [repo]

The timing path was cleaned up in two ways. Embeddings are precomputed and cached, and ingest and latency timings exclude the embedding model, with embed_query_p50_ms reported separately so the model cost is visible but not smuggled into a backend's latency. Timing starts after one untimed pass over every query, so a cold cache cannot count against either side. [repo]

Ingest goes in batches of 12,500 rows, so the 100,000-row run never sends everything in a single call. [repo] And ann_recall@10 is computed by the bench itself against a brute-force exact top 10 on the same jittered vectors, with the same where filter and tie handling, so index quality is scored against one reference rather than label recall on replicated data. [repo]

Two build-side fixes make the pgvector HNSW row meaningful at all: the shared-memory setting raised to 1 GB so the parallel HNSW build does not fail at 100,000 rows, and the index is a partial index over embedding::vector(dim) because the LangChain column carries no fixed dimension. [repo]

The filter path turned out to matter in a measured way later, since HNSW p95 sits far above p50 because of filter queries walking the graph under relaxed_order. But that is a finding of the corrected harness, not an explanation of the run I threw out. [repo]

So the change was not one knob. It was making the harness declare index and search state per row, and isolating ingest, index build, and query timing so the two backends have to answer the same question under the same conditions.

## turn-04 · domain-specialist

**Q — verbatim as asked**

In the semantic-search and retrieval-evaluation world, the "four question types" you labeled (topical, filter, exact, paraphrase) are your own taxonomy, and the call-center side has its own: what does a contact-center QA or compliance analyst actually look for in a transcript, and which of those four types maps to a real lookup they'd run, such as finding calls by disclosure phrase, by dispute reason code, or by "customer said they'd cancel"? Specifically, is "exact" a verbatim-phrase lookup like a required compliance script, and would a practitioner consider BM25 finding every exact phrase but no paraphrase an expected result or a surprising one?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

In the benchmark I built, the four types were a test of retrieval mechanisms, not a claim about how QA teams think. The exact type is the verbatim-phrase lookup, so the closest real call-center analogue is a required disclosure line or an id spoken in the call. [repo] In the 40 labeled queries, six exact queries are phrases the builder asserts appear in only one category's template, and four are ids spoken in the call. [repo]

From my field time on the contact-center side, QA and compliance analysts look for several things: required disclosure wording, dispute reason codes and outcome flags, intent signals like "customer said they'd cancel," and date or account constraints. [stretch] Mapping them to my four types, disclosure wording maps to exact, intent wording maps to paraphrase, and reason codes map most naturally to filter, because those codes usually live in structured call-record fields rather than in the transcript text. [stretch] I do not have a dispute reason code query in the 40, so that specific mapping is an extension, not something my results cover. [stretch] In my labels, outcome and date are never spoken, so filter queries are the only way to find them. [repo]

On BM25, the LanceDB FTS row scored exact recall@10 at 1.000 and paraphrase at 0.000. [repo] That is expected, not surprising. The paraphrase queries were built to share no content words, or at most one, with their target template, so BM25 has no term overlap to exploit. [repo] A QA analyst who has used keyword search would expect that; the surprise would come only if someone expected BM25 to do semantic matching. [stretch]

The practical point is that BM25 alone is the wrong tool for paraphrase. In the same x80 run, LanceDB hybrid scored 0.650 exact and 0.333 paraphrase, and vector-only scored 0.300 exact and 0.333 paraphrase. [repo] So for a transcript search that must catch both verbatim disclosures and varied cancellation language, I would run exact plus a semantic or hybrid pass rather than one lexical index. [stretch]

The caveat is that my exact phrases are template lines, not a real required compliance script. Whether real agents repeat a disclosure verbatim often enough for BM25 to be sufficient is not in my evidence; that needs real transcripts and QA labels to test. [stretch]

## turn-05 · domain-specialist

**Q — verbatim as asked**

You said filter queries cover outcome and date because those are never spoken in the transcript. In a real contact center, who owns those fields (agent disposition codes, CRM or QA-system metadata, ASR-derived tags), and how reliable are they in practice? If a practitioner knew those fields are often agent-entered and wrong or missing, would your 71 ms vs 25 ms filtered-query comparison still describe a lookup they'd actually run, or would they say the filter should be tested on noisy metadata?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

In the bench, outcome and date are never spoken in the transcripts, so the filter queries pass them as `where` [repo]. The labels are computed from the generator's own output, deterministic, with no retriever involved [repo]. Outcome in the bench is a generator field, not a real disposition code [repo]. The 71.103 ms and 25.356 ms are the p50 filter rows for pgvector-hnsw/vector and lancedb/vector [repo]. That comparison is a clean-metadata comparison: the filter is correct and complete by construction [repo].

In the real systems I have worked on, those fields have different owners and different failure modes [stretch]. Agent disposition codes are usually agent-entered at wrap-up, often from a picklist, and they are the least reliable: rushed wrap-up, coarse codes, and missing entries are common [stretch]. CRM fields are sometimes agent-entered and sometimes synced from a system of record, so reliability depends on whether the field is required and validated [stretch]. QA-system metadata is usually sampled after the fact and is not available for live retrieval on every call [stretch]. ASR-derived tags are probabilistic and need confidence thresholds, and they fail on accents and domain terms [stretch]. This is from experience, not from anything in the evidence bundle; a labeled sample of real calls with the same fields checked against transcripts or QA review would prove or correct it, with me reviewing [stretch].

A practitioner who knows those fields are often agent-entered and wrong or missing would say the filter should be tested on noisy metadata, and I would agree [stretch]. The 71 vs 25 comparison describes retrieval mechanics when the filter is already correct, not the lookup a practitioner would actually run if outcome is wrong or missing [stretch]. The bench's own README says the numbers do not transfer to real calls [repo]. I would present the clean numbers as a baseline and run a separate noisy-metadata variant before claiming anything about real filtered-query cost; that variant is not measured yet [stretch].

## turn-06 · domain-specialist

**Q — verbatim as asked**

In the retrieval-benchmark world, "recall" gets used for at least three different things, and you're reporting two of them: label recall (capped, against generator-derived relevant ids) and ann_recall@10 (against the exact scan). When a practitioner reads "recall@10 0.80" for the exact scan next to "ANN recall 0.50" for HNSW, what do they assume each number means, and which pairing would make them say you've mixed up the metrics? Specifically, why does an exact scan score only 0.80 against the labels while HNSW scoring 0.50 against the exact scan isn't a contradiction, and how does your table keep a reader from subtracting one from the other?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

In my experience, a practitioner usually assumes both figures share the same ground truth and that the lower number is a loss against the higher. I do not have a document in hand proving that assumption, so it is [stretch] and the author should review it.

The exact scan row's recall@10 0.800 [repo] is label recall: capped recall against generator-derived relevant call IDs, computed as |relevant ∩ top-k| / min(k, |relevant|) [repo]. The relevant sets are predicates over category, outcome, date, member, and account product line, not vector distance [repo]. So an exact vector scan can return the true nearest neighbors and still score 0.800 against the labels, because label relevance is not identical to vector proximity.

The HNSW row's ann_recall@10 0.495 [repo] is a different metric. It is the overlap between the HNSW top 10 and the brute-force exact top 10 on the same jittered vectors, same where filter. The exact scan scores 1.0 there by construction [repo]. That number says how much of the exact vector ranking the approximate index preserved. It says nothing about labels.

So there is no contradiction. The exact scan's 0.800 is its label performance, and its 1.000 is its ann_recall reference. HNSW's 0.495 is its index fidelity compared to that reference. The same-metric comparisons are the clean ones: HNSW label recall 0.700 against exact label recall 0.800 [repo], and HNSW ann_recall 0.495 against exact ann_recall 1.000 [repo]. The pairing that would make a reader think the metrics are mixed is subtracting 0.495 from 0.800 and saying HNSW keeps only about 0.305 of relevant results. HNSW's measured label recall is 0.700 [repo], not the product of the two numbers.

The table prevents that subtraction by keeping recall@10 and ann_recall@10 in separate columns and marking the exact scan row's index as none, exact scan [repo]. A reader comparing across rows within one column gets a valid comparison; a reader subtracting across columns is comparing two different reference standards.

## turn-07 · tactician

**Q — verbatim as asked**

The first result was a 49x gap that you threw out, and your answer to the architect said the ledger only records "a non-comparable run." Put a number on it: which two rows produced the 49x, what were the two latencies, and what was the one setting or mismatch you can point to in that early run (for example, an index on one side and an exact scan on the other) that you changed to get the corrected rows?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

I can give you the 49x itself and the correction status from the ledger: the first result was a non-comparable run that I threw out, and it should be told as a correction. [ledger] The two row names, the two latency values, and the specific setting from that early run are not in the evidence in front of me, so I will not put numbers on them.

The corrected run I can quote is the committed 2026-10-03 x80-ivf-pq table. pgvector/vector is an exact scan with no index: p50 872.9 ms, p95 900.4 ms. pgvector-hnsw/vector is HNSW cosine with m=16 and ef_construction=64, queried with ef_search=40: p50 3.799 ms, p95 92.885 ms. lancedb/vector is IVF-PQ cosine with 316 partitions on defaults: p50 15.994 ms, p95 27.612 ms. [repo] Those are the corrected rows; they are not the 49x run.

The one mismatch the corrected design fixes is exact scan versus approximate index. The bench now keeps pgvector/vector as the exact reference and makes the approximate rows explicit, and the scale comparison is approximate-against-approximate plus that exact reference. [repo] If you want me to point at the early run's knob, the safest statement is that it likely compared an exact scan on one side with an indexed approximate scan on the other, but that is a stretch [stretch]; the early results file or run log would prove or correct it, with the author reviewing before we print it.

## turn-08 · tactician

**Q — verbatim as asked**

You've said the corrected rows show LanceDB IVF-PQ at 0.44 recall@10 by default and 0.53 with `refine_factor=20`. What exactly did you change between those two runs besides `refine_factor`, what did it cost in p50 latency (the default is 16 ms), and did you try `nprobes` or a different partition count first and abandon it?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

Between the default `lancedb/vector` row and the `lancedb[refine_factor=20]/vector` row, the only thing I changed was `refine_factor`, from none to 20. Index type stayed `ivf_pq`, cosine, 316 partitions; `nprobes` stayed at default; corpus, labels, cached embeddings, ingest batch, and hardware were all identical. Both rows carry the same index cell, and `ingest_s` is unchanged at 146.4 s. The refine rows list `index_build_s` as n/a, not a new build. [repo]

The p50 latency cost was 15.994 ms to 20.282 ms, so roughly 4.3 ms added, about a 27% increase at the median. [repo]

I did sweep `nprobes`, and I did not abandon it; the sweep remains usable in the bench. The recorded reason it did not help is the synthetic x80 structure: each document has 80 near-duplicate replicas, a query's neighbors sit in one partition, and extra probes find no new candidates. The notes record the exact-top-10 overlap flat at 2/10 across the probe range, and an earlier local x80 run with offline hashed embeddings stayed at 0.225 at 5, 20, and 50 probes. That points to PQ quantization as the recall loss, not probing. [repo]

A different partition count was not tried in the committed rows. Every row shows 316 partitions, the LanceDB default at 100,000 rows. The bench exposes a `--num-partitions` knob, so that is a not-yet-run option, not something I dropped. [repo]

## turn-09 · tactician

**Q — verbatim as asked**

You said the sweep showed exact-top-10 overlap flat at 2/10 across the probe range, and that PQ quantization is the cause. What did you actually do to test that diagnosis, such as raising `num_sub_vectors`, switching to `ivf_flat`, or any other change, and what recall number did each try produce?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

I did not raise `num_sub_vectors` and I did not switch to `ivf_flat` in any committed run, so I have no recall number for either change. An `ivf_flat` run exists only as uncommitted work, and I will not cite a number from it. [ledger]

What I actually changed was the probe count and then the refine factor. The probe sweep came first: the bench notes record that exact-top-10 overlap stayed at 2/10 from 1 probe up to all 316, and a local nprobes sweep held recall at 0.225 at 5, 20, and 50 probes. [repo] That flat result is what pointed the diagnosis at PQ quantization rather than partition probing. [repo]

The refine factor sweep is the committed test that presses on the PQ diagnosis. In the x80 `ivf_pq` table, `lancedb/vector` with no refine factor produced recall@10 0.440 and ann_recall@10 0.025. [repo] `refine_factor=1` reproduced the same numbers. [repo] `refine_factor=20` raised recall@10 to 0.528 and ann_recall@10 to 0.163. [repo]

A local run on offline hashed embeddings had `refine_factor=20` reach the exact-scan level, but I treat that as diagnostic, not as a committed result. [repo] The notes name `ivf_flat` or `ivf_hnsw_sq` as the non-PQ checks that would test the diagnosis more directly; I have not done those in a committed form. [repo]

## turn-10 · skeptic

**Q — verbatim as asked**

You say PQ quantization is the cause of LanceDB's low recall, but the only committed evidence is that probing doesn't help and `refine_factor=20` helps partially (ann_recall 0.025 to 0.163, still far from exact). A hostile reader would say that's equally consistent with the 80-near-duplicate-replica structure defeating any top-10 overlap metric, since exact and approximate searches can legitimately return different replicas of the same call. How do you know that ann_recall@10 of 0.025 to 0.495 reflects index quality and not replica tie-breaking, and what, short of the uncommitted `ivf_flat` run, shows the gap isn't an artifact of scoring top-10 ids on a corpus where each document has 80 near-identical copies?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

I would not claim that the committed evidence proves it. The reader's alternative is consistent with everything I have committed so far [stretch].

What the bench does commit is a specific definition: ann_recall@10 is the overlap between a vector row's top 10 and a brute-force exact top 10 computed on the same jittered vectors and the same where filter, with ties at the 10th score counted [repo]. That makes it a ranker-reproduction metric, not a semantic-utility metric [stretch]. On the x80 corpus each underlying document has 80 near-identical replicas, so two equally good results can return different call ids and score low [repo].

The committed run gives LanceDB vector ann_recall 0.025, refine_factor=1 0.025, refine_factor=20 0.163, and pgvector HNSW 0.495, against exact 1.0 [repo]. The only committed pointer toward PQ rather than tie-breaking is the refine_factor lift plus the README's explanation that neighbours sit in one partition, extra probes find nothing, and PQ quantization cannot rank near-identical vectors [repo]. That is a mechanism and a partial lift, not a controlled test that cleanly separates the two explanations [stretch].

The uncommitted ivf_flat run is the cleanest control I know of and is not evidence in hand [ledger]. Another control I have not run or committed is to score overlap after collapsing the 80 replicas to their seed document id [stretch].

So I would narrow the published claim. I can say the approximate indexes disagree with the exact scan on this synthetic corpus [repo]; I cannot say, from the committed run, how much of that gap is index quality versus replica tie-breaking [stretch]. The README already warns that scale runs compare ingest and latency, not recall [repo], and I would treat the ann_recall numbers the same way until one of those controls lands [stretch].

## turn-11 · skeptic

**Q — verbatim as asked**

The label-recall numbers (0.80 exact, 0.70 HNSW, 0.44 and 0.53 IVF-PQ) are the ones your thesis rests on, yet you've conceded ann_recall can't separate index quality from replica tie-breaking, and that label recall is "meaningful only at scale 1." You also count all 80 replicas of a relevant call as relevant, and the filter queries are only 0.70 even for the exact scan. If a hostile reader says "your headline table ranks indexes on metrics you've admitted don't measure index quality on this corpus," what in the table survives that objection, and which numbers would you cut or relabel before publishing?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

I would split the objection in two. The label-recall columns are the vulnerable part, not the whole table. The bench's own README says scale runs measure ingest, index build, latency and `ann_recall@10`, and that label recall is meaningful only at `--scale 1` [repo]. Counting all 80 replicas as relevant means label recall at x80 ranks replica copies rather than index quality [repo]. The filter row shows the contamination directly: pgvector-hnsw filter recall@10 is 0.800 while the exact scan is 0.700, so the exact row is not an upper bound on the ANN row [repo]. That alone means those columns should not rank the indexes.

What survives is `ann_recall@10`. It is not label recall. It overlaps each approximate top 10 with a brute-force exact top 10 computed on the same jittered vectors with the same `where` filter, and counts ties at the tenth score as hits [repo]. Exact scan is 1.000 by construction, pgvector-hnsw is 0.495, LanceDB ivf_pq vector is 0.025, and ivf_pq with refine_factor 20 is 0.163 [repo]. That still carries the near-duplicate-vector stress and the PQ quantization finding, but not the replica tie-breaking defect [repo].

The mechanical columns also survive: p50_ms, p95_ms, ingest_s and index_build_s answer latency and cost, not label relevance [repo].

Before publishing, I would cut recall@5, recall@10, mrr@10 and the label by_type tables from any x80 headline table, and move label recall to the x1 table where it is meaningful [stretch]. For filter queries at x80 I would either drop them or report the ANN rows as deltas from the exact-scan ceiling, not as standalone recall [stretch]. The README's "What scale runs measure" wording is what would prove or correct those cut choices, with the author reviewing before publication [stretch].

## turn-12 · skeptic

**Q — verbatim as asked**

The 0.44 versus 0.53 IVF-PQ result and your "default tuned for larger datasets" framing are both load-bearing in the post, but you've conceded that ann_recall can't separate PQ error from replica tie-breaking, so what committed evidence says the default's low recall comes from IVF-PQ being tuned for larger data and not from your 80x-replicated corpus defeating it, and what exact sentence would you publish about LanceDB's recall that survives that objection?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

I'll be blunt: I don't have committed evidence that isolates "the default is tuned for larger data" as the cause separate from the 80x replication. What I have is narrower and worth publishing precisely.

The bench notes define ann_recall@10 as the overlap of a vector row's top 10 with a brute-force exact top 10 on the same jittered vectors, same filter, and ties at the 10th score counted as hits [repo]. They also say label recall is only meaningful at x1, and at scale the replicas make recall measure ranking among near-duplicates, not recall on real data [repo]. On this copy, neighbors sit in one partition, so extra probes find nothing new; overlap with the exact top 10 stayed at 2/10 from 1 to all 316 probes [repo]. The README attributes the remaining loss to PQ quantization, which cannot rank near-identical vectors, and says refine_factor or a non-PQ index addresses it [repo]. The local nprobes sweep did not move recall, while refine_factor=20 lifted it toward exact-scan level, and the note says to check that on real embeddings [repo].

None of those notes says the default is sized for larger data as the measured cause. The only committed wording for that framing is the ledger line: LanceDB's default IVF-PQ recall is a default sized for larger data, measured at a small one; never framed as a flaw [ledger]. I read that as a guardrail on tone, not as a causal finding.

The exact sentence I'd publish, with tags removed: "On this 100,000-row 80x-replicated synthetic corpus, LanceDB's default IVF-PQ vector mode measured recall@10 0.440 and ann_recall@10 0.025, while refine_factor=20 measured recall@10 0.528 and ann_recall@10 0.163; the bench notes attribute the remaining gap to PQ quantization rather than probe count and warn that recall on replicated data measures near-duplicate ranking, so these numbers do not transfer to a real 100,000-call corpus." Every number in that sentence is from the rendered x80 table [repo].

## turn-13 · skeptic

**Q — verbatim as asked**

Your thesis says "the numbers depend more on index choice and settings than on the database," but every index-quality number you've committed (0.495, 0.025, 0.163) comes from one corpus where each document has 80 near-identical copies, and you've conceded you can't separate index error from replica tie-breaking. What is the strongest claim about "index choice and settings matter more than the database" that survives on the latency numbers alone (873 ms, 3.8 ms, 16 ms, 20 ms, plus the filtered 71 ms vs 25 ms), and what would a hostile reader say about those latency numbers, such as a single laptop, a single run, p95 of 93 ms on HNSW, and no stated variance across repeats?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

The strongest latency-only claim I can make is narrower than the thesis. The cleanest comparison is inside one backend: pgvector exact scan p50 872.9 ms against pgvector HNSW p50 3.799 ms on the same corpus and same laptop. That is a large change from index/search settings alone, so it supports "index and settings are the dominant visible latency lever in this run" [repo]. The cross-backend numbers do not isolate a database effect because the index types were not matched: pgvector HNSW p50 3.799 ms, LanceDB vector p50 15.994 ms, and LanceDB hybrid p50 19.495 ms differ in index as well as product, and LanceDB refine_factor=20 p50 20.282 ms shows the settings themselves move latency [repo]. The filtered gap has the same confound: HNSW filter p50 71.103 ms vs LanceDB vector filter p50 25.356 ms is HNSW with relaxed ordering vs IVF-PQ, not pgvector vs LanceDB holding index constant [repo]. So the strongest claim that survives is: on these committed numbers, the largest latency differences are tied to index choice and search settings, and I cannot separate a database-only contribution [repo].

A hostile reader would say the hardware is one Intel i5-1038NG7 at 2.00 GHz with 8 cores and 15 GB RAM, Linux, local disk, precomputed embeddings, and no committed server or concurrent-load numbers [repo]. That is a laptop datapoint, not a production database benchmark [stretch]; a hosted run on the same two backends with concurrency would test the ordering. They would also note the params say repeats=3, but the table reports no spread, so there is no run-to-run variance [repo]. The HNSW p50 is 3.799 ms while p95 is 92.885 ms, a wide tail, and the bench notes the tail is driven by filter queries under iterative_scan=relaxed_order; filter p95 is 134.7 ms against filter p50 71.103 ms [repo]. Without per-repeat p95s, a hostile reader can say that tail may be a few hard queries rather than a stable latency profile [stretch]; reporting per-repeat variance would resolve it. The corpus is also synthetic, with near-duplicate replicas, so the absolute latencies may not transfer to real transcripts [repo]. My defensible wording stops at "latency ordering on this laptop, this corpus, these settings"; anything broader is [stretch].

## turn-14 · tactician

**Q — verbatim as asked**

The 3.8 ms HNSW p50 sits next to a 71 ms filtered p50 and a 93 ms p95. If a reader had only one of those three HNSW latency numbers to plan around, which would you tell them to use. What exactly happens inside a filtered query under `iterative_scan=relaxed_order` that makes it about 19x slower than the unfiltered one, and what did you try (a different `ef_search`, `strict_order`, a different `max_scan_tuples`, or a partial or btree index on the filter column) to bring it down, with what result?

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

If I had to plan around one of those three, I would use the 93 ms p95, not the 3.8 ms p50 [repo]. The 3.8 ms number is the overall p50 and is pulled down by the unfiltered topical, exact and paraphrase queries. The filtered query type is much slower: p50 71.103 ms and p95 134.7 ms in the same run [repo]. The overall p95 of 92.885 ms is closer to a safe planning number, but it still sits under the filtered p95, so if I could choose from the full table I would treat 134.7 ms as the real bound for a filtered contact-center workload [repo]. The 3.8 ms figure would be a misleading target.

What happens under `iterative_scan=relaxed_order` is that filtering is applied after the HNSW scan. pgvector's documentation describes the failure mode: with an approximate index and `ef_search=40`, a selective condition that matches only a small share of rows returns too few qualifying rows per scan unless the scan keeps going [web]. In this run the filtered p50 is 71.103 ms versus 3.767, 3.349 and 3.477 ms for topical, exact and paraphrase queries, which is the roughly 19x gap [repo]. The walk does not stop once it has k near neighbors; it has to keep walking until it has k candidates that also pass the `where`, so selective filters force many more visited nodes [web][repo]. It is not a cold cache effect; timing follows a full warm-up pass [repo].

I did not run `strict_order`, `max_scan_tuples`, a partial index or a btree index on the filter column in this committed run, and I do not have a measured result for any of those to cite [ledger]. The bench exposes an `--sweep ef_search=40,100,200` knob, but the rendered table is the `ef_search=40` run only, so I also do not have a 100 or 200 result in hand [repo]. From experience, the first change I would test is a btree or partial index on `outcome` and `date` to push the filter before the approximate scan, and I would sweep `strict_order` against `relaxed_order` to see the recall cost [stretch]. That is a hypothesis, not a measured result; a bench run with those indexes and that sweep would prove or correct it.


## turn-15 · council-routed question (skeptic's slot)

**Q — verbatim as asked**

The council sent one question back to the panel (routed via the skeptic, the interviewer who owns unearned
claims): The draft's "Choosing at this size" section is missing a verdict. At 100,000 rows on this corpus,
which store would you pick for the vector-search path, pgvector or LanceDB, and what condition flips that
pick? Name the store, give the measured numbers that carry the pick, and tag the pick itself.

**A — verbatim as given (model answering on the author's behalf; uncleaned; tagged [ledger]/[repo]/[web]/[stretch])**

I pick pgvector with its HNSW index for the vector-search path at 100,000 rows on this corpus [stretch]. The carrying numbers are from the 2026-10-03 x80-ivf-pq table [repo]. pgvector-hnsw/vector runs p50 3.799 ms and p95 92.885 ms, against pgvector/vector exact scan at p50 872.9 ms and p95 900.4 ms [repo]. LanceDB/vector runs p50 15.994 ms and p95 27.612 ms [repo]. ann_recall@10 is 0.495 for pgvector-hnsw and 0.025 for lancedb/vector, and index build is 27.180 s for pgvector-hnsw against 115.4 s for LanceDB [repo]. I am not carrying label recall at x80, because the notes say scale runs rank near-duplicates, not corpus retrieval [repo].

The flip is the filtered-vector tail. The README says those filter queries are what push HNSW p95 well above p50 [repo]. If the production mix is mostly where-filtered vector search and a 27.612 ms p95 matters more than a 3.799 ms median, I would move to LanceDB, and pgvector would become the flip for unfiltered similarity search where median latency drives the decision [stretch]. A path that also needs hybrid or BM25 would flip the pick outright, since pgvector in this bench supports vector only while LanceDB has fts and hybrid rows [repo].
