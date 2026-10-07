# ANSWERER CONTEXT (static; identical for every turn)

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

## 2. Orchestrator's cited digest
# Cited digest (written by the orchestrator; every line names its file in the contact-center-ai checkout at commit cc2344d)

Piece: "One metric, three warehouses" (brief B2, blogs/PROPOSED_POSTS.md). Thesis: declare a metric once, compile it for three SQL dialects; show what the semantic layer absorbed, what the interchange format (Apache Ossie) kept and dropped.

## What the parity run is (tools/parity.py, results/semantics/README.md)
- `make parity` runs `mf query` (MetricFlow) for five metrics on three targets (postgres, snowflake, databricks), diffs the values to a per-metric tolerance, saves each target's generated SQL next to the numbers. Output: results/semantics/<date>_parity.json plus <date>_parity_<target>.sql, rendered into results/semantics/README.md.
- Five metrics and tolerances (parity.py METRICS): call_volume 0 (exact), average_mortgage_note_rate 0.0001, banking_available_balance 0.01, credit_card_outstanding 0.01, first_contact_resolution_rate 0.0001. Comment in parity.py: "Counts are exact; monetary amounts to the cent; rates/ratios to 1e-4."
- Two dated runs exist: 2026-10-06 (git 99e12f5) and 2026-10-07 (git f46c7bd). Both report "All targets agree within tolerance." The metric values are identical across the two runs; the saved SQL files are byte-identical between the two dates for all three targets (checked by diff by the orchestrator).
- 2026-10-07 values (results/semantics/2026-10-07_parity.json): call_volume 1250 on all three; banking_available_balance 38287038.69 on all three; credit_card_outstanding 2354869.83 on all three; first_contact_resolution_rate 0.5752 on all three; average_mortgage_note_rate postgres 6.588841176470588, snowflake 6.588841176, databricks 6.5888412, max_diff 2.4e-08 (tolerance 0.0001). So the one metric whose values are not textually identical is the mortgage rate; it differs only in how many decimals each warehouse returned. The files do not say why.
- The run's "hardware" field is the machine that ran the Python client (Linux, python 3.13.5), not the warehouses. The files record no warehouse size, edition, region or timing.
- Account type: docs/TOUR.md says Snowflake is "a Snowflake trial account hosted on AWS". The Databricks edition used in the parity run is NOT recorded in results/semantics/; infra/azure/modules/databricks/README.md says the bootstrap script supports either an Azure Premium workspace or Databricks Free Edition. Unknown which was used.
- Data: one synthetic dataset (Meridian Valley credit union, seed 42; olap/dbt/README.md). f_account_snapshot holds a single month-end snapshot (2026-08-31) per account.
- What the run did NOT cover: 5 of the 43 metrics (olap/dbt/README.md, docs/TOUR.md "We've found 43 metrics"); one dataset; no larger scale; no timing or cost; no time-based dimensions or date functions in these five metrics' SQL; no Ossie round trip (docs/semantics/README.md "Not done": reverse direction not run); no run of the parity with the Ossie export (the export's expressions were not executed).

## The three saved SQL files (results/semantics/2026-10-07_parity_{postgres,snowflake,databricks}.sql)
- The three files are structurally identical: same CTEs (sma_10003_cte, sma_10001_cte, rss_10007_cte), same CROSS JOIN of five subqueries, same `WHERE product__lob = 'mortgage'` / 'banking' / 'cards' filters, same LEFT OUTER JOIN of account snapshot to d_product on product.
- Differences are only: (1) relation quoting. Postgres: `FROM "contactcenter"."marts"."f_interaction" interactions_src_10000`. Snowflake: `FROM <name>.<name>.f_interaction interactions_src_10000` (names redacted by tools/parity.py sanitize_sql, unquoted). Databricks: ``FROM `<name>`.`marts`.`f_interaction` interactions_src_10000`` (backticks). (2) The cast type in the ratio: Postgres `CAST(SUM(__first_contact_resolved_calls) AS DOUBLE PRECISION) / CAST(NULLIF(SUM(__call_volume), 0) AS DOUBLE PRECISION)`; Snowflake and Databricks `AS DOUBLE`.
- Both ratio sides use CAST ... NULLIF(..., 0), so float division and divide-by-zero to null are in the generated SQL on every target. This is the thing the Ossie export loses (docs/semantics/README.md known issue 3).
- The five metrics' SQL contains no date functions, so the post must not claim the semantic layer absorbed date-function differences from this run. olap/dbt/README.md lists portability rules for the dbt models (no `::` casts, no to_char/isodow/doy/generate_series; use dbt.date_spine, dbt.dateadd, dbt.datediff, dbt.date_trunc, dbt.concat) but those apply to the dbt models, which the parity run does not exercise through time dimensions.

## A real dialect failure from earlier (evals/README.md, lines ~146-155)
- The first full live agent eval run (baseline 2026-10-05, sql score 0.00, judge 0.16/0.19) failed because olap/dbt/target/semantic_manifest.json had last been written by `dbt build --target databricks`, which quotes with backticks; the metric tool ran that Databricks SQL on local Postgres: `syntax error at or near` a backtick in ``FROM `ccai`.`marts`.`f_account_snapshot` ``. The cause was the environment. parity.py handles this by staging a per-target manifest (target/<warehouse>/semantic_manifest.json) before each query and restoring the original afterwards (stage_semantic_manifest, restore_manifest). `query_metric` now refuses with one line naming the adapter the manifest was built for.

## Ossie export (docs/semantics/README.md; checked 2026-10-02)
- Apache Ossie (incubating; formerly Open Semantic Interchange), spec 0.2.0.dev0, marked DRAFT. Converter: apache/ossie converters/dbt at commit b6c702e (`ossie-dbt msi-to-ossie`). Export: 11 datasets, 43 metrics. Two converters exist; dbt-core's built-in writer (spec 0.1.1) emitted 37 relationships vs 17 from the Apache one; the extra 20 are many-to-many fan-out joins that upstream removed in apache/ossie#379.
- Three known issues in the export, found by the repo's own checks, "not reported upstream yet" as of Oct 6: (1) the LOB filter points at `product__lob`, a MetricFlow join path, not a field of account_snapshot, across 21 LOB-filtered metrics plus weighted_mortgage_portfolio_rate and net_member_liquidity; (2) call_volume, responses and rate_locks_count all export as `SUM(1)` with no dataset qualifier; (3) ratios lose MetricFlow's float division, exporting `(num) / (den)`, so on engines with integer division (Postgres) first_contact_resolution_rate and rate_lock_fallout_pct would truncate to 0, and a zero denominator errors instead of returning null.
- Not done: the reverse direction; no exporter script; "A consumer that executes the exported expressions directly will get different numbers from `mf query`."
- Dropped constructs: time_granularity, defaults.agg_time_dimension, metric labels (no equivalent); metric type/type_params inlined into one expression (structure lost); dataset `source` carries only the Postgres relation name.

## The "interest rate" count: what each document says (all committed files)
- docs/TOUR.md line ~91: `"interest rate" is five declared metrics`, shown with a five-metric `mf query --explain` (mortgage note, card purchase APR, deposit APY, HELOC current, investment return).
- docs/semantics/README.md line ~111: the LOB filter "keeps the four 'interest rate' meanings apart".
- olap/dbt/models/marts/semantic/metrics.yml header comment: a supervisor asking about the average interest rate "therefore gets four side-by-side answers". The same file's per-metric comments number the meanings 1, 2, 4a, 4b, 5, 6 and the file declares seven interest-rate metrics (average_mortgage_note_rate, weighted_mortgage_portfolio_rate, average_heloc_current_rate, average_credit_card_purchase_apr, average_credit_card_cash_advance_apr, average_deposit_apy, average_investment_return_pct) plus an intermediate numerator note_rate_times_balance.
- olap/dbt/README.md: the lookup table lists the same seven for "interest rate"; the worked example shows five values. docs/TOUR.md line ~527 says "Interest rate maps to seven LOB-filtered metrics".
- So the committed documents say four, five and seven in different places. Unsettled; the draft must record a [GAP], not choose. Parity covers only one of them (average_mortgage_note_rate).

## Other context
- The post's brief B2 "Must not claim": identical numbers across warehouses until results/semantics/ exists (now exists, for 5 metrics only); that Ossie issues were reported upstream (they were not, as of Oct 6); no vendor pitch.
- olap/dbt/README.md still says "Not run against a real warehouse or 1Password yet; the first run is the test" and "Neither loader has been run against a real warehouse". Those lines predate the parity runs and are now stale.
- Credentials: Snowflake key-pair auth, no password field; settings come from 1Password via `tools/warehouse-run.sh` (`op run`), so no `.env` is populated (olap/dbt/README.md).
- Loaders (olap/dbt/loaders/): PUT to an internal stage / Unity Catalog volume, then COPY INTO with explicit casts; a count(*) must equal the rows sent.

## 3. Brief B2 (blogs/PROPOSED_POSTS.md)
## B2 · One metric, three warehouses

- **Subtitle:** dbt and MetricFlow on Postgres, Snowflake and Databricks, and an export to Apache Ossie.
- **Status:** mostly ready. The models build on all three targets; the Ossie export and its issues are written up. The headline ("the same numbers everywhere") waits on S3 (`make parity`).
- **Thesis:** declare a metric once and compile it for three SQL dialects. Show what the semantic layer absorbed (quoting, casts, date functions), what the interchange format kept, and what it dropped.
- **Reader:** analytics engineers and data-platform teams weighing a semantic layer or an open interchange format.
- **Evidence:** `olap/dbt/README.md` (three targets, credentials through 1Password `op run`, Snowflake key-pair auth), `tools/bootstrap-snowflake.sh` and `bootstrap-databricks.sh`, `docs/semantics/README.md` (Ossie 0.2.0.dev0 export: 11 datasets, 43 metrics, field mapping, known issues). The dialect problem showed up for real in `evals/README.md`: a manifest built for Databricks sent backtick-quoted SQL to Postgres.
- **Outline:** the metrics and the line-of-business discriminator; three targets and how credentials stay out of files; the dialect differences (from S3's saved SQL, once it exists); the Ossie export and its three issues (the LOB filter points at a field the dataset doesn't have; three row counts all export as `SUM(1)`; ratios lose float division); what an interchange file is good for today.
- **Must not claim:** identical numbers across warehouses until `results/semantics/` exists. That issues were reported upstream (they weren't, as of Oct 6). No vendor pitch: describe Ossie and the standards effort; don't argue for a company.
- **Open items:** run S3; decide whether to file the three export issues upstream before publishing (a stronger post if they're filed). Settle the "interest rate" count: `docs/TOUR.md` says five metrics, `docs/semantics/README.md` says four meanings.


## Evidence file: results/semantics/README.md
```
# semantics results

Generated by `python -m tools.results render semantics` from the run JSON files in this directory (contract: docs/AGENT_HANDOFF.md §4.3). Do not edit by hand.

## 2026-10-07 · parity

- git: `f46c7bdeb65df3ce6985328c78dc18b2f666d49d`
- hardware: Linux-7.0.14-16-pve-x86_64-with-glibc2.41, unknown
- versions: python 3.13.5
- params: metrics=["call_volume", "average_mortgage_note_rate", "banking_available_balance", "credit_card_outstanding", "first_contact_resolution_rate"], targets=["postgres", "snowflake", "databricks"], tolerances={"call_volume": 0.0, "average_mortgage_note_rate": 0.0001, "banking_available_balance": 0.01, "credit_card_outstanding": 0.01, "first_contact_resolution_rate": 0.0001}

| config | tolerance | postgres | snowflake | databricks | max_diff | ok | status | sql_file |
|---|---|---|---|---|---|---|---|---|
| call_volume | 0 | 1250 | 1250 | 1250 | 0 | true | n/a | n/a |
| average_mortgage_note_rate | 0.000 | 6.589 | 6.589 | 6.589 | 0.000 | true | n/a | n/a |
| banking_available_balance | 0.010 | 38287038.7 | 38287038.7 | 38287038.7 | 0.000 | true | n/a | n/a |
| credit_card_outstanding | 0.010 | 2354869.8 | 2354869.8 | 2354869.8 | 0.000 | true | n/a | n/a |
| first_contact_resolution_rate | 0.000 | 0.575 | 0.575 | 0.575 | 0.000 | true | n/a | n/a |
| postgres | n/a | n/a | n/a | n/a | n/a | n/a | ok | 2026-10-07_parity_postgres.sql |
| snowflake | n/a | n/a | n/a | n/a | n/a | n/a | ok | 2026-10-07_parity_snowflake.sql |
| databricks | n/a | n/a | n/a | n/a | n/a | n/a | ok | 2026-10-07_parity_databricks.sql |

_Notes:_ All targets agree within tolerance.

## 2026-10-06 · parity

- git: `99e12f5d82f32ad6d5c85fa1d739a8a4eb3109cc`
- hardware: Linux-7.0.14-16-pve-x86_64-with-glibc2.41, unknown
- versions: python 3.13.5
- params: metrics=["call_volume", "average_mortgage_note_rate", "banking_available_balance", "credit_card_outstanding", "first_contact_resolution_rate"], targets=["postgres", "snowflake", "databricks"], tolerances={"call_volume": 0.0, "average_mortgage_note_rate": 0.0001, "banking_available_balance": 0.01, "credit_card_outstanding": 0.01, "first_contact_resolution_rate": 0.0001}

| config | tolerance | postgres | snowflake | databricks | max_diff | ok | status | sql_file |
|---|---|---|---|---|---|---|---|---|
| call_volume | 0 | 1250 | 1250 | 1250 | 0 | true | n/a | n/a |
| average_mortgage_note_rate | 0.000 | 6.589 | 6.589 | 6.589 | 0.000 | true | n/a | n/a |
| banking_available_balance | 0.010 | 38287038.7 | 38287038.7 | 38287038.7 | 0.000 | true | n/a | n/a |
| credit_card_outstanding | 0.010 | 2354869.8 | 2354869.8 | 2354869.8 | 0.000 | true | n/a | n/a |
| first_contact_resolution_rate | 0.000 | 0.575 | 0.575 | 0.575 | 0.000 | true | n/a | n/a |
| postgres | n/a | n/a | n/a | n/a | n/a | n/a | ok | 2026-10-06_parity_postgres.sql |
| snowflake | n/a | n/a | n/a | n/a | n/a | n/a | ok | 2026-10-06_parity_snowflake.sql |
| databricks | n/a | n/a | n/a | n/a | n/a | n/a | ok | 2026-10-06_parity_databricks.sql |

_Notes:_ All targets agree within tolerance.

```

## Evidence file: results/semantics/2026-10-07_parity.json
```
{
  "date": "2026-10-07",
  "git_sha": "f46c7bdeb65df3ce6985328c78dc18b2f666d49d",
  "area": "semantics",
  "run": "parity",
  "hardware": "Linux-7.0.14-16-pve-x86_64-with-glibc2.41, unknown",
  "versions": {
    "python": "3.13.5"
  },
  "params": {
    "metrics": [
      "call_volume",
      "average_mortgage_note_rate",
      "banking_available_balance",
      "credit_card_outstanding",
      "first_contact_resolution_rate"
    ],
    "targets": [
      "postgres",
      "snowflake",
      "databricks"
    ],
    "tolerances": {
      "call_volume": 0.0,
      "average_mortgage_note_rate": 0.0001,
      "banking_available_balance": 0.01,
      "credit_card_outstanding": 0.01,
      "first_contact_resolution_rate": 0.0001
    }
  },
  "metrics": {
    "call_volume": {
      "tolerance": 0,
      "postgres": 1250,
      "snowflake": 1250,
      "databricks": 1250,
      "max_diff": 0,
      "ok": true
    },
    "average_mortgage_note_rate": {
      "tolerance": 0.0001,
      "postgres": 6.588841176470588,
      "snowflake": 6.588841176,
      "databricks": 6.5888412,
      "max_diff": 2.4000000209412065e-08,
      "ok": true
    },
    "banking_available_balance": {
      "tolerance": 0.01,
      "postgres": 38287038.69,
      "snowflake": 38287038.69,
      "databricks": 38287038.69,
      "max_diff": 0.0,
      "ok": true
    },
    "credit_card_outstanding": {
      "tolerance": 0.01,
      "postgres": 2354869.83,
      "snowflake": 2354869.83,
      "databricks": 2354869.83,
      "max_diff": 0.0,
      "ok": true
    },
    "first_contact_resolution_rate": {
      "tolerance": 0.0001,
      "postgres": 0.5752,
      "snowflake": 0.5752,
      "databricks": 0.5752,
      "max_diff": 0.0,
      "ok": true
    },
    "postgres": {
      "status": "ok",
      "sql_file": "2026-10-07_parity_postgres.sql"
    },
    "snowflake": {
      "status": "ok",
      "sql_file": "2026-10-07_parity_snowflake.sql"
    },
    "databricks": {
      "status": "ok",
      "sql_file": "2026-10-07_parity_databricks.sql"
    }
  },
  "notes": "All targets agree within tolerance."
}

```

## Evidence file: results/semantics/2026-10-07_parity_postgres.sql
```
WITH sma_10003_cte AS (
  SELECT
    1 AS __call_volume
    , case when first_contact_resolved then 1 else 0 end AS __first_contact_resolved_calls
  FROM "contactcenter"."marts"."f_interaction" interactions_src_10000
)

, sma_10001_cte AS (
  SELECT
    product_id AS product
    , mortgage_note_rate AS __average_mortgage_note_rate
    , banking_available_balance AS __banking_available_balance
    , card_outstanding_balance AS __credit_card_outstanding
  FROM "contactcenter"."marts"."f_account_snapshot" account_snapshot_src_10000
)

, rss_10007_cte AS (
  SELECT
    lob
    , product_id AS product
  FROM "contactcenter"."marts"."d_product" products_src_10000
)

SELECT
  MAX(subq_5.call_volume) AS call_volume
  , MAX(subq_15.average_mortgage_note_rate) AS average_mortgage_note_rate
  , MAX(subq_24.banking_available_balance) AS banking_available_balance
  , MAX(subq_33.credit_card_outstanding) AS credit_card_outstanding
  , MAX(subq_39.first_contact_resolution_rate) AS first_contact_resolution_rate
FROM (
  SELECT
    SUM(__call_volume) AS call_volume
  FROM sma_10003_cte
) subq_5
CROSS JOIN (
  SELECT
    AVG(average_mortgage_note_rate) AS average_mortgage_note_rate
  FROM (
    SELECT
      rss_10007_cte.lob AS product__lob
      , sma_10001_cte.__average_mortgage_note_rate AS average_mortgage_note_rate
    FROM sma_10001_cte
    LEFT OUTER JOIN
      rss_10007_cte
    ON
      sma_10001_cte.product = rss_10007_cte.product
  ) subq_11
  WHERE product__lob = 'mortgage'
) subq_15
CROSS JOIN (
  SELECT
    SUM(banking_available_balance) AS banking_available_balance
  FROM (
    SELECT
      rss_10007_cte.lob AS product__lob
      , sma_10001_cte.__banking_available_balance AS banking_available_balance
    FROM sma_10001_cte
    LEFT OUTER JOIN
      rss_10007_cte
    ON
      sma_10001_cte.product = rss_10007_cte.product
  ) subq_20
  WHERE product__lob = 'banking'
) subq_24
CROSS JOIN (
  SELECT
    SUM(credit_card_outstanding) AS credit_card_outstanding
  FROM (
    SELECT
      rss_10007_cte.lob AS product__lob
      , sma_10001_cte.__credit_card_outstanding AS credit_card_outstanding
    FROM sma_10001_cte
    LEFT OUTER JOIN
      rss_10007_cte
    ON
      sma_10001_cte.product = rss_10007_cte.product
  ) subq_29
  WHERE product__lob = 'cards'
) subq_33
CROSS JOIN (
  SELECT
    CAST(SUM(__first_contact_resolved_calls) AS DOUBLE PRECISION) / CAST(NULLIF(SUM(__call_volume), 0) AS DOUBLE PRECISION) AS first_contact_resolution_rate
  FROM sma_10003_cte
) subq_39
```

## Evidence file: results/semantics/2026-10-07_parity_snowflake.sql
```
WITH sma_10003_cte AS (
  SELECT
    1 AS __call_volume
    , case when first_contact_resolved then 1 else 0 end AS __first_contact_resolved_calls
  FROM <name>.<name>.f_interaction interactions_src_10000
)

, sma_10001_cte AS (
  SELECT
    product_id AS product
    , mortgage_note_rate AS __average_mortgage_note_rate
    , banking_available_balance AS __banking_available_balance
    , card_outstanding_balance AS __credit_card_outstanding
  FROM <name>.<name>.f_account_snapshot account_snapshot_src_10000
)

, rss_10007_cte AS (
  SELECT
    lob
    , product_id AS product
  FROM <name>.<name>.d_product products_src_10000
)

SELECT
  MAX(subq_5.call_volume) AS call_volume
  , MAX(subq_15.average_mortgage_note_rate) AS average_mortgage_note_rate
  , MAX(subq_24.banking_available_balance) AS banking_available_balance
  , MAX(subq_33.credit_card_outstanding) AS credit_card_outstanding
  , MAX(subq_39.first_contact_resolution_rate) AS first_contact_resolution_rate
FROM (
  SELECT
    SUM(__call_volume) AS call_volume
  FROM sma_10003_cte
) subq_5
CROSS JOIN (
  SELECT
    AVG(average_mortgage_note_rate) AS average_mortgage_note_rate
  FROM (
    SELECT
      rss_10007_cte.lob AS product__lob
      , sma_10001_cte.__average_mortgage_note_rate AS average_mortgage_note_rate
    FROM sma_10001_cte
    LEFT OUTER JOIN
      rss_10007_cte
    ON
      sma_10001_cte.product = rss_10007_cte.product
  ) subq_11
  WHERE product__lob = 'mortgage'
) subq_15
CROSS JOIN (
  SELECT
    SUM(banking_available_balance) AS banking_available_balance
  FROM (
    SELECT
      rss_10007_cte.lob AS product__lob
      , sma_10001_cte.__banking_available_balance AS banking_available_balance
    FROM sma_10001_cte
    LEFT OUTER JOIN
      rss_10007_cte
    ON
      sma_10001_cte.product = rss_10007_cte.product
  ) subq_20
  WHERE product__lob = 'banking'
) subq_24
CROSS JOIN (
  SELECT
    SUM(credit_card_outstanding) AS credit_card_outstanding
  FROM (
    SELECT
      rss_10007_cte.lob AS product__lob
      , sma_10001_cte.__credit_card_outstanding AS credit_card_outstanding
    FROM sma_10001_cte
    LEFT OUTER JOIN
      rss_10007_cte
    ON
      sma_10001_cte.product = rss_10007_cte.product
  ) subq_29
  WHERE product__lob = 'cards'
) subq_33
CROSS JOIN (
  SELECT
    CAST(SUM(__first_contact_resolved_calls) AS DOUBLE) / CAST(NULLIF(SUM(__call_volume), 0) AS DOUBLE) AS first_contact_resolution_rate
  FROM sma_10003_cte
) subq_39
```

## Evidence file: results/semantics/2026-10-07_parity_databricks.sql
```
WITH sma_10003_cte AS (
  SELECT
    1 AS __call_volume
    , case when first_contact_resolved then 1 else 0 end AS __first_contact_resolved_calls
  FROM `<name>`.`marts`.`f_interaction` interactions_src_10000
)

, sma_10001_cte AS (
  SELECT
    product_id AS product
    , mortgage_note_rate AS __average_mortgage_note_rate
    , banking_available_balance AS __banking_available_balance
    , card_outstanding_balance AS __credit_card_outstanding
  FROM `<name>`.`marts`.`f_account_snapshot` account_snapshot_src_10000
)

, rss_10007_cte AS (
  SELECT
    lob
    , product_id AS product
  FROM `<name>`.`marts`.`d_product` products_src_10000
)

SELECT
  MAX(subq_5.call_volume) AS call_volume
  , MAX(subq_15.average_mortgage_note_rate) AS average_mortgage_note_rate
  , MAX(subq_24.banking_available_balance) AS banking_available_balance
  , MAX(subq_33.credit_card_outstanding) AS credit_card_outstanding
  , MAX(subq_39.first_contact_resolution_rate) AS first_contact_resolution_rate
FROM (
  SELECT
    SUM(__call_volume) AS call_volume
  FROM sma_10003_cte
) subq_5
CROSS JOIN (
  SELECT
    AVG(average_mortgage_note_rate) AS average_mortgage_note_rate
  FROM (
    SELECT
      rss_10007_cte.lob AS product__lob
      , sma_10001_cte.__average_mortgage_note_rate AS average_mortgage_note_rate
    FROM sma_10001_cte
    LEFT OUTER JOIN
      rss_10007_cte
    ON
      sma_10001_cte.product = rss_10007_cte.product
  ) subq_11
  WHERE product__lob = 'mortgage'
) subq_15
CROSS JOIN (
  SELECT
    SUM(banking_available_balance) AS banking_available_balance
  FROM (
    SELECT
      rss_10007_cte.lob AS product__lob
      , sma_10001_cte.__banking_available_balance AS banking_available_balance
    FROM sma_10001_cte
    LEFT OUTER JOIN
      rss_10007_cte
    ON
      sma_10001_cte.product = rss_10007_cte.product
  ) subq_20
  WHERE product__lob = 'banking'
) subq_24
CROSS JOIN (
  SELECT
    SUM(credit_card_outstanding) AS credit_card_outstanding
  FROM (
    SELECT
      rss_10007_cte.lob AS product__lob
      , sma_10001_cte.__credit_card_outstanding AS credit_card_outstanding
    FROM sma_10001_cte
    LEFT OUTER JOIN
      rss_10007_cte
    ON
      sma_10001_cte.product = rss_10007_cte.product
  ) subq_29
  WHERE product__lob = 'cards'
) subq_33
CROSS JOIN (
  SELECT
    CAST(SUM(__first_contact_resolved_calls) AS DOUBLE) / CAST(NULLIF(SUM(__call_volume), 0) AS DOUBLE) AS first_contact_resolution_rate
  FROM sma_10003_cte
) subq_39
```

## Evidence file: tools/parity.py
```
"""
`make parity`: run five semantic-layer metrics on Postgres, Snowflake and Databricks,
diff the numbers to a tolerance, and write the results to `results/semantics/`.

Each warehouse needs its dbt adapter group and, for Snowflake/Databricks, its
1Password-resolved credentials via `tools/warehouse-run.sh`.  The MetricFlow
semantic manifest in `olap/dbt/target/semantic_manifest.json` is temporarily
switched to the target's adapter for the query, then restored.

Usage:
    uv run python -m tools.parity
    PARITY_TARGETS=postgres,snowflake uv run python -m tools.parity
"""

from __future__ import annotations

import argparse
import csv
import io
import json
import os
import platform
import re
import shlex
import shutil
import subprocess
import sys
import tempfile
from datetime import UTC, datetime
from pathlib import Path

# ---------------------------------------------------------------------------
# Configuration
# ---------------------------------------------------------------------------

REPO_ROOT = Path(__file__).resolve().parent.parent
DBT_DIR = REPO_ROOT / "olap" / "dbt"
WAREHOUSE_RUN = REPO_ROOT / "tools" / "warehouse-run.sh"
RESULTS_DIR = REPO_ROOT / "results" / "semantics"

#: Metrics chosen for parity and their tolerances.
#: Counts are exact; monetary amounts to the cent; rates/ratios to 1e-4.
METRICS: dict[str, dict[str, float | int]] = {
    "call_volume": {"tolerance": 0},
    "average_mortgage_note_rate": {"tolerance": 0.0001},
    "banking_available_balance": {"tolerance": 0.01},
    "credit_card_outstanding": {"tolerance": 0.01},
    "first_contact_resolution_rate": {"tolerance": 0.0001},
}

TARGETS: dict[str, dict] = {
    "postgres": {
        "profile_target": "dev",
        "adapter": "postgres",
        "groups": ["dbt"],
        "wrapper": None,
    },
    "snowflake": {
        "profile_target": "snowflake",
        "adapter": "snowflake",
        "groups": ["dbt", "snowflake"],
        "wrapper": str(WAREHOUSE_RUN),
    },
    "databricks": {
        "profile_target": "databricks",
        "adapter": "databricks",
        "groups": ["dbt", "databricks"],
        "wrapper": str(WAREHOUSE_RUN),
    },
}

ANSI_RE = re.compile(r"\x1b\[[0-9;]*[A-Za-z]")
_SECRET_PLACEHOLDER_RE = re.compile(r"<concealed by 1Password>")

# Environment variable names whose values may be account identifiers, schemas, or credentials.
_SENSITIVE_ENV_PATTERNS = (
    "ACCOUNT", "HOST", "CATALOG", "DATABASE", "SCHEMA", "USER", "ROLE", "WAREHOUSE",
    "TOKEN", "KEY", "PASSWORD", "PASSPHRASE", "SECRET", "CREDENTIAL", "PRIVATE_KEY",
    "HTTP_PATH",
)


def _sensitive_env_values() -> list[str]:
    """Collect non-empty environment values that look like secrets or account ids."""
    values: set[str] = set()
    for name, value in os.environ.items():
        if not value or len(value) <= 3:
            continue
        if any(pattern in name for pattern in _SENSITIVE_ENV_PATTERNS):
            values.add(value)
            # For URLs, also redact the bare hostname/host:port portion.
            if value.startswith(("http://", "https://")):
                host = value.split("://", 1)[1].split("/")[0]
                if host:
                    values.add(host)
    # Replace longest values first so shorter substrings do not leave partial matches.
    return sorted(values, key=len, reverse=True)


def sanitize_sql(sql: str) -> str:
    """Redact account identifiers, hosts, tokens, and 1Password placeholders."""
    for value in _sensitive_env_values():
        sql = sql.replace(value, "<redacted>")
    sql = _SECRET_PLACEHOLDER_RE.sub("<name>", sql)
    return sql
# Pure helpers
# ---------------------------------------------------------------------------


def clean_stdout(text: str) -> str:
    return ANSI_RE.sub("", text)


def parse_value(raw: str):
    """Best-effort parse a CSV cell to int/float/str."""
    raw = raw.strip()
    if raw == "":
        return None
    try:
        return int(raw)
    except ValueError:
        pass
    try:
        return float(raw)
    except ValueError:
        pass
    return raw


def parse_csv_row(path: Path) -> dict[str, object]:
    """Return {column: value} for the first data row of a MetricFlow CSV."""
    text = path.read_text()
    reader = csv.DictReader(io.StringIO(text))
    rows = list(reader)
    if not rows:
        raise ValueError("MetricFlow returned an empty CSV")
    return {k.lower(): parse_value(v) for k, v in rows[0].items()}


def extract_sql(explain_output: str) -> str:
    """Pull the generated SQL out of `mf query --explain` stdout."""
    lines = explain_output.splitlines()
    for i, line in enumerate(lines):
        if "SQL (" in line:
            j = i + 1
            while j < len(lines) and not lines[j].strip():
                j += 1
            return "\n".join(lines[j:]).strip()
    # Fallback: the SQL itself starts with WITH / SELECT.
    for i, line in enumerate(lines):
        if line.strip().upper().startswith(("WITH", "SELECT")):
            return "\n".join(lines[i:]).strip()
    return ""


def diff_metric(values: dict[str, float], tolerance: float, targets: list[str]) -> dict:
    """Compare numeric values across requested targets."""
    numeric = {k: v for k, v in values.items() if isinstance(v, (int, float))}
    missing = [t for t in targets if t not in numeric]
    if missing:
        return {"max_diff": None, "ok": False, "note": f"missing targets: {missing}"}
    if len(numeric) < 2:
        return {"max_diff": None, "ok": False, "note": "fewer than 2 targets returned numbers"}
    vals = list(numeric.values())
    max_diff = max(vals) - min(vals)
    ok = max_diff <= tolerance
    return {"max_diff": max_diff, "ok": ok}


# ---------------------------------------------------------------------------
# dbt / MetricFlow environment
# ---------------------------------------------------------------------------


def git_sha() -> str:
    try:
        return subprocess.run(
            ["git", "rev-parse", "HEAD"],
            cwd=REPO_ROOT,
            capture_output=True,
            text=True,
            check=True,
        ).stdout.strip()
    except Exception:
        return "unknown"


def make_temp_profile(target_name: str) -> Path:
    """Create a temporary profiles dir whose default target is `target_name`."""
    tmp = Path(tempfile.mkdtemp(prefix=f"parity-{target_name}-"))
    original = DBT_DIR / "profiles.yml"
    text = original.read_text()
    text = re.sub(r"^  target: dev$", f"  target: {target_name}", text, flags=re.MULTILINE)
    (tmp / "profiles.yml").write_text(text)
    return tmp


def stage_semantic_manifest(target_name: str, original_path: Path) -> None:
    """Copy the target-specific semantic manifest into place."""
    target_manifest = DBT_DIR / "target" / target_name / "semantic_manifest.json"
    if not target_manifest.exists():
        raise FileNotFoundError(
            f"{target_manifest} not found; run `make dbt-build WAREHOUSE={target_name}` first"
        )
    shutil.copy2(target_manifest, original_path)


def restore_manifest(original_path: Path, original_text: str | None) -> None:
    """Restore the original manifest, or remove it if there was none at the start."""
    if original_text is not None:
        original_path.write_text(original_text)
    elif original_path.exists():
        original_path.unlink()


# ---------------------------------------------------------------------------
# Running MetricFlow per target
# ---------------------------------------------------------------------------


def mf_command(metrics: list[str], csv_path: Path, explain: bool = False) -> list[str]:
    cmd = [
        "mf", "query",
        "--metrics", ",".join(metrics),
        "--csv", str(csv_path),
    ]
    if explain:
        cmd.append("--explain")
    return cmd


def run_subprocess(cmd: list[str], env: dict[str, str] | None = None, cwd: Path = REPO_ROOT) -> subprocess.CompletedProcess:
    return subprocess.run(
        cmd,
        cwd=cwd,
        capture_output=True,
        text=True,
        env=env,
        timeout=600,
    )


def run_mf_target(
    target: str,
    metrics: list[str],
    csv_path: Path,
    profiles_dir: Path,
) -> dict:
    """Run `mf query --csv` and `mf query --explain` for a single target."""
    config = TARGETS[target]
    groups = config["groups"]
    uv_prefix = ["uv", "run"] + [g for grp in groups for g in ("--group", grp)]

    env = os.environ.copy()
    env["DBT_PROFILES_DIR"] = str(profiles_dir)
    env["DBT_PROJECT_DIR"] = str(DBT_DIR)

    # Data query
    data_cmd = uv_prefix + mf_command(metrics, csv_path, explain=False)
    if config["wrapper"]:
        inner = " ".join(shlex.quote(str(c)) for c in data_cmd)
        data_cmd = [config["wrapper"], target, "--", "bash", "-c", inner]

    data_proc = run_subprocess(data_cmd, env=env)
    data_proc.stdout = clean_stdout(data_proc.stdout)
    data_proc.stderr = clean_stdout(data_proc.stderr)

    if data_proc.returncode != 0:
        return {
            "status": "error",
            "error": (data_proc.stdout + "\n" + data_proc.stderr).strip(),
        }

    try:
        values = parse_csv_row(csv_path)
    except Exception as exc:  # pragma: no cover - defensive
        return {
            "status": "error",
            "error": f"Failed to parse MetricFlow CSV: {exc}",
        }

    # Explain query for generated SQL
    explain_cmd = uv_prefix + mf_command(metrics, csv_path, explain=True)
    if config["wrapper"]:
        inner = " ".join(shlex.quote(str(c)) for c in explain_cmd)
        explain_cmd = [config["wrapper"], target, "--", "bash", "-c", inner]

    explain_proc = run_subprocess(explain_cmd, env=env)
    explain_proc.stdout = clean_stdout(explain_proc.stdout)
    sql = extract_sql(explain_proc.stdout)

    return {"status": "ok", "values": values, "sql": sql}


def check_postgres_reachable() -> bool:
    """Cheap probe: can we connect to the local Compose Postgres?"""
    url = os.environ.get("DATABASE_URL", "postgresql://postgres:postgres@localhost:5432/contactcenter")
    try:
        import psycopg2
        with psycopg2.connect(url):
            return True
    except Exception:
        return False


# ---------------------------------------------------------------------------
# Main parity run
# ---------------------------------------------------------------------------


def run_parity(targets: list[str], dry_run: bool = False) -> dict:
    metrics = list(METRICS.keys())
    original_manifest = DBT_DIR / "target" / "semantic_manifest.json"
    original_text = original_manifest.read_text() if original_manifest.exists() else None

    run_dir = RESULTS_DIR
    run_dir.mkdir(parents=True, exist_ok=True)

    results: dict[str, dict] = {}
    sql_files: dict[str, Path | None] = {}

    try:
        for target in targets:
            config = TARGETS[target]
            if target == "postgres" and not check_postgres_reachable():
                results[target] = {
                    "status": "unreachable",
                    "error": "local Postgres is not reachable (run `make up` and seed)",
                }
                sql_files[target] = None
                continue

            if dry_run:
                results[target] = {"status": "dry-run", "values": {}, "sql": "-- dry-run"}
                sql_files[target] = None
                continue

            profiles_dir = DBT_DIR if target == "postgres" else make_temp_profile(config["profile_target"])
            try:
                if target == "postgres":
                    restore_manifest(original_manifest, original_text)
                else:
                    stage_semantic_manifest(config["profile_target"], original_manifest)

                with tempfile.TemporaryDirectory(prefix=f"parity-csv-{target}-") as td:
                    csv_path = Path(td) / "mf_result.csv"
                    results[target] = run_mf_target(target, metrics, csv_path, profiles_dir)
                    if results[target]["status"] == "ok":
                        sql = sanitize_sql(results[target].get("sql", ""))
                        results[target]["sql"] = sql
                        sql_file = run_dir / f"{_run_stamp()}_parity_{target}.sql"
                        sql_file.write_text(sql)
                        sql_files[target] = sql_file.name
                    else:
                        sql_files[target] = None
            finally:
                if target != "postgres":
                    shutil.rmtree(profiles_dir, ignore_errors=True)
    finally:
        restore_manifest(original_manifest, original_text)

    # Diff across requested targets; a missing target is a failure.
    diff: dict[str, dict] = {}
    for metric in metrics:
        values = {
            target: results[target]["values"].get(metric)
            for target in targets
            if results[target].get("status") == "ok" and metric in results[target]["values"]
        }
        tolerance = float(METRICS[metric]["tolerance"])  # type: ignore[arg-type]
        diff[metric] = {"values": values, **diff_metric(values, tolerance, targets)}

    all_targets_ok = all(results[t].get("status") == "ok" for t in targets)
    all_metrics_ok = all(diff[m]["ok"] for m in metrics)
    run_ok = all_targets_ok and all_metrics_ok

    return {
        "results": results,
        "diff": diff,
        "sql_files": sql_files,
        "run_ok": run_ok,
    }


def _run_stamp() -> str:
    return datetime.now(UTC).strftime("%Y-%m-%d")


def build_record(parity: dict, targets: list[str]) -> dict:
    date = datetime.now(UTC).strftime("%Y-%m-%d")
    sha = git_sha()

    metrics_table: dict[str, dict] = {}
    for metric in METRICS:
        row = {"tolerance": METRICS[metric]["tolerance"]}
        for target in targets:
            row[target] = parity["diff"][metric]["values"].get(target)
        row["max_diff"] = parity["diff"][metric]["max_diff"]
        row["ok"] = parity["diff"][metric]["ok"]
        metrics_table[metric] = row

    status = {target: {"status": parity["results"][target]["status"]} for target in targets}
    for target in targets:
        sql_file = parity["sql_files"].get(target)
        if sql_file:
            status[target]["sql_file"] = sql_file

    all_metrics_ok = all(row["ok"] for row in metrics_table.values())
    all_targets_ok = all(status[t]["status"] == "ok" for t in targets)
    notes = []
    if not all_metrics_ok:
        mismatches = [m for m, row in metrics_table.items() if not row["ok"]]
        notes.append(f"Mismatches beyond tolerance or missing data: {', '.join(mismatches)}")
    if not all_targets_ok:
        bad = [t for t in targets if status[t]["status"] != "ok"]
        notes.append(f"Target errors: {', '.join(bad)}")
    if not notes:
        notes.append("All targets agree within tolerance.")

    return {
        "date": date,
        "git_sha": sha,
        "area": "semantics",
        "run": "parity",
        "hardware": f"{platform.platform()}, {platform.processor() or 'unknown'}".rstrip(", "),
        "versions": {"python": platform.python_version()},
        "params": {
            "metrics": list(METRICS.keys()),
            "targets": targets,
            "tolerances": {m: float(v["tolerance"]) for m, v in METRICS.items()},  # type: ignore[arg-type]
        },
        "metrics": {**metrics_table, **status},
        "notes": " ".join(notes),
    }


def main(argv: list[str] | None = None) -> int:
    parser = argparse.ArgumentParser(description="Semantic-layer parity across warehouses")
    parser.add_argument(
        "--targets",
        default=os.environ.get("PARITY_TARGETS", "postgres,snowflake,databricks"),
        help="Comma-separated warehouse targets (default: postgres,snowflake,databricks)",
    )
    parser.add_argument(
        "--dry-run",
        action="store_true",
        help="Print the plan and exit without querying warehouses",
    )
    args = parser.parse_args(argv)

    targets = [t.strip() for t in args.targets.split(",") if t.strip()]
    unknown = [t for t in targets if t not in TARGETS]
    if unknown:
        print(f"Unknown target(s): {unknown}; choose from {list(TARGETS)}", file=sys.stderr)
        return 2

    if args.dry_run:
        print(f"Would run parity for targets: {targets}")
        print(f"Metrics: {list(METRICS.keys())}")
        for t in targets:
            print(f"  {t}: adapter={TARGETS[t]['adapter']}, groups={TARGETS[t]['groups']}")
        return 0

    parity = run_parity(targets)
    record = build_record(parity, targets)

    stamp = _run_stamp()
    results_file = RESULTS_DIR / f"{stamp}_parity.json"
    results_file.write_text(json.dumps(record, indent=2, default=str) + "\n")

    # Render the README
    from tools.results import render
    readme = render("semantics")

    print(f"wrote {results_file}")
    print(f"wrote {readme}")

    # Print a concise table
    print()
    print("| metric | " + " | ".join(targets) + " | tolerance | max_diff | ok |")
    print("|" + "---|" * (len(targets) + 4))
    for metric in METRICS:
        row = record["metrics"][metric]
        vals = " | ".join(str(row.get(t, "n/a")) for t in targets)
        print(f"| {metric} | {vals} | {row['tolerance']} | {row['max_diff']} | {row['ok']} |")

    return 0 if parity["run_ok"] else 1


if __name__ == "__main__":
    sys.exit(main())

```

## Evidence file: docs/semantics/README.md
```
# Semantic layer export: Apache Ossie

`ossie/ccai_semantic.ossie.yaml` is our dbt/MetricFlow semantic layer
(`olap/dbt/models/marts/semantic/semantic_models.yml` and `metrics.yml`) converted to
[Apache Ossie](https://github.com/apache/ossie). It is converter output, committed as-is. Nobody
edited it by hand. Ossie (incubating) is the Apache project that used to be called Open Semantic
Interchange (OSI). It defines a vendor-neutral YAML/JSON format for semantic models (datasets,
fields, relationships, metrics) so they can move between BI tools, semantic layers, and agents.

Checked on **2026-10-02** against these primary sources:

| What | Where |
|---|---|
| Repository | https://github.com/apache/ossie at commit [`b6c702e`](https://github.com/apache/ossie/tree/b6c702ed1c07e91382a69e870c875cbd19570828) (2026-10-01) |
| Spec version | **`0.2.0.dev0`**, marked *DRAFT, schema may change before 0.2.0 is released* ([`core-spec/spec.md`](https://github.com/apache/ossie/blob/b6c702ed1c07e91382a69e870c875cbd19570828/core-spec/spec.md), [`core-spec/ossie-schema.json`](https://github.com/apache/ossie/blob/b6c702ed1c07e91382a69e870c875cbd19570828/core-spec/ossie-schema.json)) |
| Last tagged spec | `0.1.1`. The spec's version history dates it 2025-12-11. The repo has tag [`osi-0.1.1-rc1`](https://github.com/apache/ossie/tree/osi-0.1.1-rc1) and no GitHub release. |
| Converter used | [`converters/dbt`](https://github.com/apache/ossie/tree/b6c702ed1c07e91382a69e870c875cbd19570828/converters/dbt) (`apache-ossie-dbt 0.2.0.dev0`, CLI `ossie-dbt`). Not on PyPI yet, so it runs from the checkout. |
| Announcement | [Apache Ossie (Incubating): The New Name for Open Semantic Interchange](https://www.snowflake.com/en/blog/apache-ossie-open-semantic-interchange-incubator/) |

## Which converter, and why

Two dbt-to-Ossie converters exist today:

1. **`ossie-dbt msi-to-ossie`** from the Apache repo. It reads dbt's `target/semantic_manifest.json`
   and writes spec `0.2.0.dev0` (one model at the document root). **This is the one committed here.**
2. **dbt-core's built-in writer.** On every `dbt parse`, dbt-core 1.12.5 (our pin) calls MetricFlow
   0.213.0's `MSIToOSIConverter` and writes `olap/dbt/target/osi_document.json` (gitignored) in spec
   `0.1.1` (a `semantic_model` array). It needs no extra install. On our models, though, it emits
   37 relationships where the Apache converter emits 17. The extra 20 join two facts (or a fact and
   a dimension's foreign key) on a shared foreign key, e.g. `account_snapshot__transactions__product`.
   These are many-to-many fan-out joins, not the many-to-one foreign keys that an Ossie relationship
   describes (`from` = "many side", `to` = "one side"). Upstream removed them in
   [apache/ossie#379](https://github.com/apache/ossie/pull/379) ("Skip FOREIGN<->FOREIGN entity
   pairs when building relationships").

Apart from that, the two outputs agree: the same 11 datasets with identical fields, and the same
43 metrics with identical expressions. Both pass the upstream validator for their own spec version
(`validation/validate.py` at `b6c702e` and at `osi-0.1.1-rc1`).

## Reproduce

Converting is offline. Network is only needed once, to fetch the converter and its dependencies.

```bash
REPO=$(pwd)   # the contact-center-ai checkout

# 1. Semantic manifest from our dbt project (offline; needs no database)
uv sync --group dbt
(cd olap/dbt && uv run --group dbt dbt parse --profiles-dir .)
# -> olap/dbt/target/semantic_manifest.json (and dbt's own target/osi_document.json, spec 0.1.1)

# 2. The Apache converter, pinned to the commit we used
git clone https://github.com/apache/ossie.git /tmp/ossie
git -C /tmp/ossie checkout b6c702ed1c07e91382a69e870c875cbd19570828
cd /tmp/ossie/converters/dbt && uv sync
uv run ossie-dbt msi-to-ossie \
  -i "$REPO/olap/dbt/target/semantic_manifest.json" \
  -o "$REPO/docs/semantics/ossie/ccai_semantic.ossie.yaml" \
  --model-name ccai_semantic
# no conversion warnings on our models

# 3. Validate against the spec (JSON Schema, unique names, references, SQL syntax)
cd /tmp/ossie && uv run --project converters/dbt --with jsonschema \
  python validation/validate.py "$REPO/docs/semantics/ossie/ccai_semantic.ossie.yaml"
# Validation PASSED
```

Our run: dbt-core 1.12.5 / dbt-metricflow 0.15.0 for the parse. The converter environment resolved
metricflow 0.211.0, sqlglot 30.12.0 and uv 0.12.22. Re-run steps 1–2 after any change to
`semantic_models.yml` or `metrics.yml`. `tests/test_ossie_export.py` fails when the committed export
no longer covers every semantic model, entity, dimension, measure and metric.

## What carries over, and what doesn't

The table below maps each construct our YAML uses to the spec `0.2.0.dev0` field the converter
writes it into. Field names are quoted exactly from `core-spec/spec.md`. "No equivalent" means the
spec has no field for it and the converter drops it.

| Our YAML (MetricFlow) | Ossie `0.2.0.dev0` | Status |
|---|---|---|
| `semantic_models[].name` | dataset `name` | mapped |
| `semantic_models[].description` | dataset `description` | mapped |
| `semantic_models[].model: ref('…')` | dataset `source` | partial: the relation dbt rendered for the `dev` (Postgres) target, `"contactcenter"."marts"."d_product"`. A Snowflake or Databricks consumer needs its own relation name. |
| `entities[]` `type: primary`, `expr` | dataset `primary_key` (the column) plus a field named after the entity | mapped |
| `entities[]` `type: foreign` | `relationships[]`: `from`, `to`, `from_columns`, `to_columns`, plus a field | mapped (17 relationships, all many-to-one) |
| `dimensions[]` `type: categorical` | field with `dimension: {is_time: false}` | mapped |
| `dimensions[]` `type: time` | field with `dimension: {is_time: true}` | mapped |
| `dimensions[].type_params.time_granularity: day` | none | no equivalent |
| `dimensions[].expr` | field `expression.dialects[]` (`dialect: ANSI_SQL`) | mapped |
| `defaults.agg_time_dimension` | none | no equivalent: `metric_time` is a MetricFlow concept |
| `measures[]` (name, `expr`, `description`) | a plain field (no `dimension`) with the measure's `expr` and `description` | mapped |
| `measures[].agg` | folded into each metric's `expression` (`SUM`, `AVG`, `COUNT(DISTINCT …)`) | partial: see "Known issues" below |
| `metrics[].name` | metric `name` | mapped |
| `metrics[].description` | metric `description` | mapped |
| `metrics[].label` | none (the spec's Metric object has no `label`) | no equivalent: dropped |
| `metrics[].type` (`simple`, `ratio`, `derived`) and `type_params` | inlined into one metric `expression` | partial: the result is right but the structure is gone (e.g. `net_member_liquidity` no longer references its two input metrics) |
| `metrics[].filter` (`{{ Dimension('product__lob') }} = '…'`) | `CASE WHEN … THEN … END` inside the metric `expression` | partial: see "Known issues" below |
| (not used by us) | `datatype`, `ai_context`, `unique_keys`, `custom_extensions` | the converter doesn't emit them |

## Known issues in the export

These come from the conversion, not from our models. They are why the export is an interchange
artifact, not a replacement for MetricFlow. A consumer that executes the exported expressions
directly will get different numbers from `mf query`:

1. **The LOB filter points at a field that doesn't exist.** All 21 LOB-filtered metrics (and
   `weighted_mortgage_portfolio_rate` and `net_member_liquidity`, built from them) filter on
   `product__lob`. That is a MetricFlow join path (entity `product`, then dimension `lob` on
   `products`), not a field of `account_snapshot`. Ossie has the `account_snapshot → products`
   relationship, but the expression doesn't say `products.lob`. This is the filter that keeps the
   four "interest rate" meanings apart.
2. **The three row counts look identical.** `call_volume`, `responses` and `rate_locks_count` all
   export as `SUM(1)`, with no dataset qualifier. `first_contact_resolved_calls` and
   `note_rate_times_balance` are also unqualified. A consumer cannot tell which dataset these count.
3. **Ratios lose MetricFlow's float division.** MetricFlow renders a ratio as
   `CAST(num AS DOUBLE) / CAST(NULLIF(den, 0) AS DOUBLE)` (`metricflow/sql/render/expr_renderer.py`,
   `visit_ratio_computation_expr`). The export writes `(num) / (den)`. On engines with integer
   division (Postgres among them), `first_contact_resolution_rate` and `rate_lock_fallout_pct`
   would truncate to 0, and a zero denominator errors instead of returning null.

## Not done

- The reverse direction (`ossie-dbt ossie-to-msi`) was not run.
- No exporter script or Make target. The converter isn't on PyPI, and the steps above are three commands.
- The known issues above are not reported upstream yet.

```

## Evidence file: olap/dbt/README.md
```
# OLAP + Semantic Layer (dbt Core + MetricFlow)

Phase 2 of the semantic-layer demo: the star/snowflake models (§6 of the demo
plan) and the MetricFlow declarations that disambiguate the deliberately
ambiguous metrics (§7). This layer is **additive** — it reads the OLTP
foundation (`olap/oltp/schema.sql`, seeded by `olap/seed.py`) and writes its
own tables into the `marts` schema. It does not touch `ccai_mcp/`, `rag/`, or
the OLTP schema/seed.

## What's here

| Path | Purpose |
|---|---|
| `dbt_project.yml` | dbt project; `marts/` models materialized as tables, staging compiled ephemeral |
| `profiles.yml` | three targets, every value from env: `dev` (the docker-compose `db` Postgres, the default), `snowflake` (key-pair auth) and `databricks` |
| `models/staging/stg_account_lob.sql` | Wide per-LOB account row (CTE only) — the polymorphism that fuels the demo |
| `models/marts/dim/` | `d_date`, `d_time`, `d_member`, `d_household`, `d_staff`, `d_team`, `d_category`, `d_product`, `d_account` (snowflake→`d_product`), degenerate `d_channel`/`d_outcome`/`d_account_status` |
| `models/marts/fact/` | `f_interaction`, `f_interaction_account` (bridge), `f_csat`, `f_account_snapshot`, `f_transaction`, `f_rate_lock`, `f_rate` |
| `models/marts/semantic/semantic_models.yml` | MetricFlow semantic models (entities, the `lob` discriminator, measures) |
| `models/marts/semantic/metrics.yml` | The public metric catalog — 43 named metrics, none keeps a bare ambiguous word |
| `tests/` | Singular tests: transaction sign convention, balance sign convention, LOB-filter isolation |
| `models/marts/_*.yml` | Generic tests: referential integrity (`relationships`), keys, `accepted_values` |

## The disambiguation rule (the point of the whole thing)

There is **no** metric named `interest_rate`, `balance`, `limit`, or `lcv`.
Every ambiguous spoken word maps to N distinct, lob-filtered metrics:

| Word you say | Metrics it resolves to (each a different number) |
|---|---|
| "interest rate" | `average_mortgage_note_rate` · `weighted_mortgage_portfolio_rate` · `average_heloc_current_rate` · `average_credit_card_purchase_apr` · `average_credit_card_cash_advance_apr` · `average_deposit_apy` · `average_investment_return_pct` |
| "balance" | `banking_available_balance` (asset) · `credit_card_outstanding` (**liability**) · `mortgage_principal_balance` · `heloc_drawn_balance` · `investment_market_value` · `escrow_balance` … and `net_member_liquidity` (the only *declared* blend) |
| "LCV / LTV" | `loan_to_value` (a fact) vs `member_lifetime_value` (a declared convention) |
| "limit" | `credit_card_credit_limit` · `heloc_credit_limit` (capacity) vs `insurance_coverage_limit` (coverage) |
| "lock" | `rate_locks_count` (pipeline) — never conflated with a fraud freeze (an interaction outcome) |

The discriminator is `product.lob`, surfaced as `Dimension('product__lob')` in
every metric filter.

## Dependencies

dbt-core + dbt-postgres + MetricFlow (`dbt-metricflow`) are pinned as a uv
dependency group in `pyproject.toml`:

```bash
uv sync --group dbt        # installs the pinned dbt + MetricFlow CLI into .venv
```

Pinned versions (verified together): `dbt-core==1.12.5`,
`dbt-postgres==1.11.0` (adapter versioning is decoupled from core),
`dbt-metricflow==0.15.0` (provides the `mf` CLI).

### Other warehouses (Snowflake, Databricks)

The models are written to run unchanged on three warehouses. The warehouse adapters are
separate dependency groups, layered on top of `dbt`, never in the base install:

```bash
uv sync --group dbt --group snowflake      # dbt-snowflake==1.12.1
uv sync --group dbt --group databricks     # dbt-databricks==1.12.6
```

The two groups are declared as conflicting in `pyproject.toml` (the Databricks adapter caps
`pydantic`/`packaging` and the Snowflake adapter caps `certifi`, and the conflict keeps those
caps out of the base lock), so install one at a time. Versions were checked against PyPI on
2026-10-02: `dbt-snowflake` 1.12.1 requires `dbt-core>=1.10`; `dbt-databricks` 1.12.6 requires
`dbt-core>=1.11.2,<1.12.6` (1.12.5 caps core below 1.12.4, so it cannot pair with the pinned
`dbt-core==1.12.5`).

Each target reads only environment variables (placeholders in `.env.example`, listed in the
root `CLAUDE.md`). Snowflake authenticates with a key pair: `SNOWFLAKE_PRIVATE_KEY_PATH` points at
a PKCS#8 PEM file and `SNOWFLAKE_PRIVATE_KEY_PASSPHRASE` is only for an encrypted key; there is
no password field. The raw OLTP tables must already exist on the target: load them with the
loaders below. On Postgres the sources are read from `public`; on Snowflake and Databricks from
`SNOWFLAKE_RAW_SCHEMA` / `DATABRICKS_RAW_SCHEMA` (default `raw`) in the target's database or catalog.

```bash
dbt parse --target snowflake --target-path target/snowflake   # offline: renders the profile, connects to nothing
dbt build --target snowflake --target-path target/snowflake   # needs the loaded data and a running warehouse
```

Keep `--target-path target/<warehouse>` on every warehouse command (`make dbt-build` adds it).
`mf` and the metric tools read `target/semantic_manifest.json` and query the local Postgres, so a
warehouse build that writes `target/` leaves Snowflake or Databricks SQL there and every metric
query fails. The tools and `make evals-live` refuse with one line when that happens; `dbt parse`
(dev) rebuilds it.

### Running against Snowflake or Databricks (settings from 1Password)

One command each. The `SNOWFLAKE_*` / `DATABRICKS_*` settings come from the 1Password items `CMW/snowflake-ccai` and `CMW/databricks-ccai`, written by `tools/bootstrap-snowflake.sh` / `tools/bootstrap-databricks.sh`. `tools/warehouse-run.sh` resolves them through `op run` against the committed reference templates in `tools/op/`, so no `.env` is populated. The Snowflake private key exists only as a mode-600 temp file for the run.

```bash
make load-snowflake                       # S2 load: raw tables on Snowflake
make load-databricks                      # S2 load: raw tables on Databricks
make dbt-build WAREHOUSE=snowflake        # or WAREHOUSE=databricks
make load-snowflake DRY=1                 # what would run + the variable NAMES resolved; runs nothing
make load-snowflake DIRECT=1              # skip 1Password: read the variables from the shell, as before
```

Before running anything, the wrapper checks, with one message each:
- `op` is installed and signed in (`eval $(op signin)`);
- the item exists;
- `uv sync --group dbt --group <warehouse>` has been done;
- the warehouse answers `SELECT 1` (`tools/warehouse-run.sh --no-check ...` skips this check).

Not run against a real warehouse or 1Password yet; the first run is the test.

### Loaders (`olap/dbt/loaders/`)

Setting up Databricks for them (warehouse, catalog, schemas, volume, `DATABRICKS_*` in 1Password): `tools/bootstrap-databricks.sh`, see [`infra/azure/modules/databricks/README.md`](../../infra/azure/modules/databricks/README.md).

`olap/seed.py` loads Postgres. The loaders put the same rows (same table list, columns and
preparers, imported from `seed.py`) into raw tables on a warehouse. Column types come from
`olap/oltp/schema.sql`, mapped per warehouse (`NUMERIC(p,s)` to `NUMBER(p,s)` / `DECIMAL(p,s)`,
`TIMESTAMPTZ` to `TIMESTAMP_TZ` / `TIMESTAMP`, `JSONB` to `VARIANT` / `STRING`); an unmapped type
raises. The tables carry no primary-key or check constraints, since the warehouses do not enforce them.

| Warehouse | How the file gets there | Load |
|---|---|---|
| Snowflake | `PUT` to an internal stage (`<db>.<raw schema>.ccai_loader_stage`) | `COPY INTO` with explicit `$1:col::TYPE` casts |
| Databricks | `PUT` into a Unity Catalog volume (`/Volumes/<catalog>/<raw schema>/ccai_loader_stage`) | `COPY INTO` with explicit `cast(col AS TYPE)` over JSON read as strings |

Each table runs `CREATE TABLE IF NOT EXISTS`, `PUT`, `TRUNCATE`, `COPY INTO` (forced), then a
`count(*)` that must equal the rows sent. Truncate-and-load makes a re-run land on the same counts
with no duplicates. If a `COPY` fails, that table is left empty and the next run repairs it. The
database (Snowflake) or catalog (Databricks), the warehouse and the role must already exist; the
raw schema and the stage or volume are created. Timestamps are sent with an explicit UTC offset,
matching the Compose Postgres (a UTC server). Run each in its own env:

```bash
python data/synthetic/generate_data.py               # the JSON, if not already generated
make load-dry-run                                    # print every statement, connect to nothing
make load-snowflake                                  # uv run --group dbt --group snowflake python -m olap.dbt.loaders snowflake
make load-databricks                                 # uv run --group dbt --group databricks python -m olap.dbt.loaders databricks
```

The loaders read the same `SNOWFLAKE_*` / `DATABRICKS_*` variables as the dbt targets, plus
`SNOWFLAKE_RAW_SCHEMA` and `DATABRICKS_RAW_SCHEMA`. Neither loader has been run against a real
warehouse; the offline tests use a fake that interprets the generated statements.

Portability rules the models follow, so a new model should too: no `::` casts (use
`cast(... as ...)` and `{{ dbt.type_int() }}`), no `to_char`, `isodow`, `doy` or
`generate_series` (use `dbt.date_spine`, `dbt.generate_series`, `dbt.dateadd`,
`dbt.datediff`, `dbt.date_trunc`, `dbt.concat`, and `case` for day and month names), and ISO
week arithmetic that does not depend on a session's week-start setting.

## Prerequisites

The OLTP layer must be loaded first (see `olap/README.md`):

```bash
docker compose up -d db                    # or a reachable Postgres on :5432
olap/oltp/apply.sh                         # apply the OLTP schema
python data/synthetic/generate_data.py     # regenerate seed JSON (deterministic, seed 42)
python olap/seed.py                        # load into Postgres (idempotent)
```

## Run commands (from `olap/dbt/`)

`dbt` and `mf` both pick up `dbt_project.yml` / `profiles.yml` from the current
working directory, so run everything from this directory (or set
`DBT_PROFILES_DIR` and `DBT_PROJECT_DIR`).

```bash
cd olap/dbt

# sanity
uv run --group dbt dbt debug

# build the star tables AND run all data tests (referential integrity,
# sign-convention, lob-filter isolation)
uv run --group dbt dbt build

# re-run tests only
uv run --group dbt dbt test

# validate the MetricFlow declarations (semantic config + data warehouse)
uv run --group dbt mf validate-configs

# list / query metrics
uv run --group dbt mf list metrics
uv run --group dbt mf query --metrics banking_available_balance,credit_card_outstanding --decimals 2
```

`dbt build` first runs `dbt parse`, which emits `target/semantic_manifest.json`
— `mf` reads that artifact, so `mf validate-configs` must come **after** a
`dbt parse` / `build`.

## Worked disambiguation (real values from the seeded data, seed 42)

The single question **"what is our average interest rate?"** has no answer —
and no metric with that bare name exists. Ask the semantic layer and you get
five declared metrics side by side (via `mf query --metrics ... --explain`,
each generating its own lob-filtered SQL):

| Metric | Value |
|---|---|
| `average_mortgage_note_rate` (lob=mortgage) | 6.5888 |
| `average_credit_card_purchase_apr` (lob=cards) | 18.1192 |
| `average_deposit_apy` (lob=banking) | 1.9373 |
| `average_heloc_current_rate` (lob=home) | 10.1101 |
| `average_investment_return_pct` (lob=investments) | 7.4710 |

The sign trap, **"what is the member's balance?"**:

| Metric | Value |
|---|---|
| `banking_available_balance` (asset) | 38,287,038.69 |
| `credit_card_outstanding` (liability) | 2,354,869.83 |
| `net_member_liquidity` (derived: asset − liability) | 35,932,168.86 |

The acronym collision, **"LCV"**:

| Metric | Value |
|---|---|
| `loan_to_value` (LTV, a fact) | 77.63 |
| `member_lifetime_value` (LCV, a declared convention) | 760,635.25 |

### The LCV convention (parameterized, declared once)

`member_lifetime_value` is a **derived metric** with its formula written down:

```
member_lifetime_value = relationship_revenue × 24 / active_members
```

where:

- `relationship_revenue = total_fee_revenue + total_interest_income` (fee +
  interest income over the trailing ~6-month transaction window),
- `active_members` = distinct members with ledger activity in the window,
- `× 24` packs two declared parameters: **×2** annualizes a 6-month window,
  and **×12** is the assumed average relationship tenure in years
  (`24 = 2 × 12`).

These numbers are a **convention, not a fact** — they are parameters to argue
about, and because the formula lives in one committed YAML file, the argument
happens in a code review instead of a slide deck. §8 of the plan (Q4) leaves
this open; this is the explicitly-parameterized version the plan asked for.

### NPS mapping

`nps = (promoters - detractors) / responses × 100` on the 1–5 scale with
`5 = promoter`, `4/3 = passive`, `2/1 = detractor`.

## Scope boundary

Additive only. This layer never modifies `ccai_mcp/`, `rag/`, or the OLTP
schema/seed — the MCP metric-tool surface (a `query_metric` tool over these
declarations) is the next phase.

## Data notes

- `f_account_snapshot` is at the real `account × snapshot_date` (monthly) grain,
  but the synthetic OLTP seed carries only the *current* as-of state, so this
  build materializes a single month-end snapshot (`2026-08-31`) per account.
  Wiring in a dbt snapshot / periodic loader later changes nothing downstream.
- The seed encodes `interest` transactions only as credits (earned); the
  `interest_expense` measure is declared for when the seed emits the charged
  (debit) leg, and is `0` today.
```
