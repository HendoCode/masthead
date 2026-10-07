# Sources & Handoff — one-metric-three-warehouses

Everything the draft rests on, with citations, plus the council record and the pre-publish
checklist. Evidence files are read-only copies of the contact-center-ai checkout at commit
`cc2344d` (HEAD at intake), re-read on 2026-10-07 before any number was quoted. No master-CV
extract was available in this repo and none was hunted for; the claims ledger extract (synced
2026-10-06) was the only claims authority.

## Research citations (every figure in the draft traces here)

| Claim in the draft | Source file (contact-center-ai) | Note |
|---|---|---|
| Five metrics, three targets, per-metric tolerances (0, 0.0001, 0.01, 0.01, 0.0001) | `tools/parity.py` (`METRICS`, comment "Counts are exact; monetary amounts to the cent; rates/ratios to 1e-4"); `results/semantics/2026-10-07_parity.json` `params` | |
| Table values and max_diff (1250; 6.588841176470588 / 6.588841176 / 6.5888412, about 2.4e-08; 38287038.69; 2354869.83; 0.5752) | `results/semantics/2026-10-07_parity.json` | Re-read 2026-10-07. The rendered README rounds to 6.589 |
| Run dates 2026-10-06 and 2026-10-07; git `99e12f5` and `f46c7bd`; "All targets agree within tolerance"; Python 3.13.5, Linux | `results/semantics/README.md`; both `*_parity.json` | Hardware field is the client machine; no warehouse size or edition is recorded |
| Values identical across the two dates; the three SQL files byte-identical across the two dates | `results/semantics/2026-10-06_parity*.json/.sql` vs `2026-10-07_*` | `diff` run by the orchestrator 2026-10-07: only the `sql_file` name lines differ in the JSON; no differences in any `.sql` |
| How parity.py runs (stage manifest, `mf query`, CSV row, `--explain`, restore) | `tools/parity.py` (`run_parity`, `run_mf_target`, `stage_semantic_manifest`, `restore_manifest`, `sanitize_sql`) | |
| Three-dialect SQL structure; quoting fragments; `DOUBLE PRECISION` vs `DOUBLE`; `CAST ... NULLIF(..., 0)` on all three | `results/semantics/2026-10-07_parity_{postgres,snowflake,databricks}.sql` | Names redacted to `<name>` by `sanitize_sql` |
| No date functions in the five metrics' SQL | same three SQL files | Absence checked by reading; no `date_*` or time dimension appears |
| First live eval run: sql 0.00, Postgres `syntax error at or near` a backtick, manifest written by a Databricks build, cause was the environment | `evals/README.md` lines 146-148 | Judge scores deliberately not quoted (ledger and README give differently grouped figures) |
| 21 of 43 metrics LOB-filtered; 43 metrics; 11 datasets; Ossie `0.2.0.dev0` draft; converter commit `b6c702e`; validator pass; three issues; dropped constructs; not reported upstream | `docs/semantics/README.md` (checked 2026-10-02) | Quoted from the repo's own write-up, not re-fetched from apache/ossie. No web research was done |
| Snowflake is a trial account hosted on AWS | `docs/TOUR.md` line 460 | Not in `results/semantics/` |
| Databricks edition unrecorded | `infra/azure/modules/databricks/README.md` supports Azure Premium or Free Edition; `results/semantics/` records neither | `[GAP]` |
| 1Password via `tools/warehouse-run.sh`; Snowflake key pair, no password field; dbt portability rules | `olap/dbt/README.md` | |
| Synthetic dataset, Meridian Valley, seed 42; single month-end snapshot 2026-08-31 | `olap/dbt/README.md` ("Data notes", "Prerequisites") | The draft does not name the credit union |
| `make parity` is the entry point | `Makefile` (`parity` target) | `[GAP]` exact invocation per run date not recorded |

## "Interest rate" count: what each document says (open item, recorded as a GAP, not resolved)

| Document | Says |
|---|---|
| `docs/TOUR.md` line 91 | "interest rate" is **five** declared metrics (shows a five-metric `mf query --explain`) |
| `docs/semantics/README.md` line 111 | the LOB filter keeps **four** "interest rate" meanings apart |
| `olap/dbt/models/marts/semantic/metrics.yml` header | **four** side-by-side answers; per-metric comments number the meanings 1, 2, 4a, 4b, 5, 6; the file declares **seven** interest-rate metrics plus the intermediate `note_rate_times_balance` |
| `olap/dbt/README.md` | lookup table lists **seven**; the worked example shows **five** values |
| `docs/TOUR.md` line 527 | "Interest rate" maps to **seven** LOB-filtered metrics |

The documents disagree (four, five, seven). The draft gives no count and says only that one of the
interest-rate metrics was in the parity check.

## Must not claim (from brief B2, updated for what now exists)
- Identical numbers across warehouses **beyond what `results/semantics/` shows**. `results/semantics/` now exists (runs 2026-10-06 and 2026-10-07), so the claim is allowed, scoped to five named metrics, one synthetic dataset, the stated tolerances, the two run dates, and trial or free-tier warehouse accounts. Do not generalize to other metrics, scales or dialect features (dates especially).
- That the Ossie export issues were reported upstream (they were not, as of 2026-10-06).
- That the semantic layer "absorbed" date-function differences: the five metrics' SQL has no date functions.
- That the Ossie export was executed or that its truncation was observed: it is a reading of the exported expression.
- No vendor pitch: describe Ossie and the standards effort; do not argue for a company.
- No employer or client names; the client is "a credit union" (ledger). No production, user-count or "production-ready" language.
- Nothing about the Databricks edition: unrecorded.

## Clearances
No customer, client or employer name appears in the draft. Clearance line: none needed; none recorded.

## Council record
Inline, combined pass per round (no subagents, no extra audits). Editors: slop-allergist,
voice-guardian, presentation-reviewer, claims-steward, headline-utility, technical-reviewer.

- Round 1 (first full draft): slop-allergist 6 (hard fail: "a MetricFlow join path and not a field of `account_snapshot`" is contrast framing, allowed zero times), voice-guardian 8 ("cheap to copy" is an adjective with no cost measured), presentation-reviewer 9, claims-steward 6 (hard fail: "I have not investigated" asserts an author action no file records), headline-utility 9, technical-reviewer 8 (`nps` called a ratio; it is a derived metric). Aggregate 7.67. Information gaps: none routed to interview (interview cap of 10 turns was used); open ones became GAPs in the editorial block.
- Fixes applied between rounds: contrast sentence rewritten as two plain sentences; "I have not investigated" removed; "cheap" removed; `nps` moved to the derived metrics; "I have not run the exported expressions" rewritten as what the parity check did; "I repeated nothing by hand" and "from one setup" deleted as unrecorded; "most are filtered" replaced by "21 of the 43"; Ossie sentence now says who checked and when; `[repo/web]` tag corrected to `[repo]` (no web research).
- Round 2: slop-allergist 9, voice-guardian 9, presentation-reviewer 9, claims-steward 9 on content (every number has a row above; no blocked name; Must-not-claim honored) but with 5 unconfirmed `[stretch]` claims, which the steward's cap would hold at 6 until the author confirms them (see `claims-review.md`), headline-utility 9, technical-reviewer 9. Aggregate 9.0 with the claims steward scored on content; 8.5 if the unconfirmed-stretch cap is applied.

## Pre-publish checklist
1. Open GAPs from the draft's editorial block: interest-rate count (4/5/7), Databricks edition, why the mortgage rate differs in decimals, upstream filing of the Ossie issues, exact `make parity` invocation per run.
2. To-confirm list: `claims-review.md` (every `[stretch]`).
3. Run `make check-public` in contact-center-ai before publishing.
4. Confirm the public repo URL before adding a link.
5. Diagrams: none in this draft.

## After you publish
Come back and run Step 7 (Lessons): the machine diffs the published version against the draft,
proposes generalizable lessons, and on an explicit yes appends them to
voice/stephen/content-lessons.md.
