# Interview Transcript — Semantics Parity Check: Five Metrics Compared Across Postgres, Snowflake and Databricks

> **Answered by a model on the author's behalf, not yet verified by the author.** Every answer
> below was produced by `claude-sonnet-5-5` playing Stephen from the claims ledger, a cited
> digest and the committed evidence files (see `interview-inputs/`). The questions were asked by
> `claude-haiku-4-5-20251001` playing the persona files. Answers are verbatim and uncleaned.
> Tags: `[ledger]` `[repo]` `[web]` `[stretch]`. Technical numbers come from the evidence files,
> never from an answer. A master-CV extract was not available in this repo and was not hunted
> for; the claims ledger extract (synced 2026-10-06) was the only claims authority.

Roster run: domain-specialist (turns 1-5), skeptic (turns 6-10). 10 answered turns, the cap.

## Isolation proof (codeword test)

Three fresh `claude -p --no-session-persistence` processes, each started from a scratch directory
outside any repo, run 2026-10-07.

1. Call A (`claude-haiku-4-5-20251001`): "The secret codeword for this session is PELICAN-7741. Reply only with: stored." Reply: `stored.`
2. Call B (`claude-sonnet-5-5`, new process): "What secret codeword was given to you earlier in this session? If none was given to you in this conversation, reply exactly: NO CODEWORD." Reply: `NO CODEWORD.`
3. Call C (`claude-haiku-4-5-20251001`, new process): same question as B. Reply: `NO CODEWORD`

Neither the other model nor a second call of the same model saw the codeword, so each turn's calls
share no context. The interviewer was given only its persona file (as the system prompt), the
topic and the transcript so far. The answerer was given only the current question plus the static
context (ledger, cited digest, brief B2, evidence files). Exact inputs per turn:
`interview-inputs/turn-NN-{interviewer,answerer}.md`; the static answerer context is
`interview-inputs/answerer-context.md`.

---

## domain-specialist

**Q1 — In practice, when you're comparing this metric across all three warehouses, are you testing against identical source data in each, or are the underlying tables actually different—different grain, different freshness, maybe different slowly-changing-dimension handling—and if so, what counts as "parity" in your actual validation workflow?**

It's identical source data. All three warehouses get the same synthetic dataset (Meridian Valley credit union, seed 42), so grain, freshness and slowly-changing-dimension handling aren't varied. [repo: olap/dbt/README.md] The loaders reuse the table list, columns and preparers from `seed.py`. Each table ends with a `count(*)` that must equal the rows sent. [repo] The column types are mapped per warehouse, for example NUMERIC to NUMBER or DECIMAL. [repo]

Freshness doesn't enter into it, because the data is static. `f_account_snapshot` holds one month-end snapshot (2026-08-31) per account, so there's no history and no SCD logic in play. [repo] The files don't record any table-by-table comparison beyond that row-count check.

What I call parity is narrow. `make parity` runs five metrics through MetricFlow on each target and diffs the values to a per-metric tolerance. Those are 0 for call_volume, 0.0001 for the mortgage rate and the first-contact-resolution rate, and 0.01 for the two balances. [repo: tools/parity.py] Both runs (2026-10-06 and 2026-10-07) report "All targets agree within tolerance." [repo: results/semantics/README.md] The mortgage rate differs only in decimals returned, with a max_diff of 2.4e-08. [repo: 2026-10-07_parity.json] The files don't say why.

That covers five of 43 metrics, one dataset, and a Snowflake trial account. The files don't record which Databricks edition ran. [repo]

**Q2 — You've tested five metrics on static synthetic data with identical grain and no slowly-changing dimensions. When you run the same metrics on real production data with actual slowly-changing dimensions and daily updates, do the three dialects still agree?**

I don't know, and the files don't say. The parity run covers five named metrics on one synthetic dataset (Meridian Valley credit union, seed 42). It ran on two dates, 2026-10-06 and 2026-10-07, and both report "All targets agree within tolerance." [repo] The tolerances are 0 for call_volume, 0.01 for the two money metrics and 0.0001 for the two rates. [repo]

Your description is accurate. `f_account_snapshot` holds a single month-end snapshot (2026-08-31) per account, so there is no slowly-changing history in the data. [repo] The SQL for these five metrics has no date functions or time dimensions. [repo] The run therefore says nothing about how the dialects handle SCD joins, point-in-time logic or daily refreshes. I haven't run this on production data. The repo is a portfolio mirror, and I don't claim the result carries over. [ledger]

What I can say is narrow. The saved SQL for the three targets differs only in relation quoting and in `DOUBLE PRECISION` versus `DOUBLE` for the ratio cast. [repo] The mortgage rate values differ in decimals only (max_diff 2.4e-08), and the files don't say why. [repo] The Snowflake account was a trial, and the files don't record the Databricks edition. [repo]

Testing SCDs and daily updates would need a new dataset and a new run.

**Q3 — What do the other 38 metrics do that these five don't—and why weren't they in the parity check?**

The other 38 cover more kinds of metric than the five I ran. From `docs/semantics/README.md` and `olap/dbt/README.md`, they include derived metrics. `net_member_liquidity` is assets minus liabilities, `member_lifetime_value` is a declared formula, and `weighted_mortgage_portfolio_rate` is built from other metrics. They also include more ratios, such as `rate_lock_fallout_pct` and `nps`. Some use `COUNT(DISTINCT …)`, like active members, and many more apply the line-of-business filter. [repo]

The five I ran were a count, an average, two filtered sums and one ratio. `tools/parity.py` fixes that list in `METRICS`, with tolerances of exact for counts, the cent for money, and 1e-4 for rates. The ratio exercises the float-division and `NULLIF` cast. [repo]

I can't tell you why those five were chosen. The comment in `parity.py` explains the tolerances but not the selection. The files also don't say whether the other 38 were tried. What I can say is that they weren't run, so the result covers five of 43 metrics, on one synthetic dataset, on two run dates. The saved SQL has no date functions. Time dimensions and date handling weren't tested, and neither were the derived metrics. I'd treat parity for the other 38 as open. [repo]

**Q4 — When you run `make parity`, is MetricFlow itself generating the SQL for all three warehouses from a single metric definition, or are you comparing MetricFlow's output against hand-written reference SQL that you've already validated for each dialect—and if it's the latter, what would actually break if MetricFlow's compiler produced subtly different SQL on one of the dialects that still agreed numerically on your five test cases?**

MetricFlow generates the SQL. `tools/parity.py` runs `mf query` for the five metrics on postgres, snowflake and databricks. For each target it stages that target's semantic manifest first. It diffs the returned values and saves the generated SQL from `mf query --explain`. The files contain no hand-written reference SQL, so the second branch of your question doesn't apply. [repo]

Your underlying concern still holds, though. The check compares values only. Nothing asserts on the SQL text. If one dialect's SQL differed subtly but matched on these cases, `make parity` would pass. [repo]

I did diff the three saved 2026-10-07 SQL files by eye. They have the same CTEs, the same cross join and the same filters. They differ only in relation quoting and in `DOUBLE PRECISION` versus `DOUBLE` in the ratio cast. [repo]

The cast shows why this matters. The Ossie export loses it, and `docs/semantics/README.md` says that on Postgres this would truncate `first_contact_resolution_rate` to 0. A silent cast difference of that kind is what a values-only check could miss if the data hid it. [repo]

The coverage is narrow. It's five of 43 metrics, one synthetic dataset, a single month-end snapshot, and two run dates with identical SQL. The files record no timing, and the five metrics' SQL has no date functions. So the run says nothing about date-function differences. [repo]

**Q5 — You've identified that Ossie drops the DOUBLE PRECISION cast Postgres needs—a loss that would silently truncate this metric to zero in production. In your actual deployment workflow, who owns discovering that kind of dialect-specific constraint before a metric goes live, and how does your semantic layer surface which cast each dialect requires?**

I'd correct one thing first. Nothing here is in production. It's a synthetic credit union dataset, a portfolio mirror, and the Ossie claim is a reading of the exported expression. The export was never executed, so the truncation to 0 is predicted in docs/semantics/README.md, not observed.

On ownership, the files don't describe a team process. In this repo the check is a script. `make parity` (tools/parity.py) runs `mf query` for five metrics on postgres, snowflake and databricks and diffs the values to a tolerance. For first_contact_resolution_rate that tolerance is 0.0001, and all three returned 0.5752 on 2026-10-07.

The semantic layer doesn't keep a table of required casts. MetricFlow renders the cast per dialect, and parity.py saves each target's generated SQL. In results/semantics/2026-10-07_parity_postgres.sql the ratio uses `AS DOUBLE PRECISION`. In the Snowflake and Databricks files it uses `AS DOUBLE`. Diffing those files is how the difference shows up.

The check has limits. It covers five of 43 metrics, one dataset and two run dates. It never ran the Ossie export's expressions. I haven't reported the export issues upstream.

In my past SE and SA work I'd expect the data platform team to own this kind of check. [stretch]

## skeptic

**Q6 — When you publish this under the title "One Metric, Three Warehouses," readers will see that title and assume you've validated a metric across three production scenarios—but you've tested five of 43 metrics on static synthetic data with a values-only check and tolerances loose enough to miss the DOUBLE PRECISION gap you found by hand. At what point in the post do you explicitly tell them "this is a 12% coverage POC on synthetic data," and how do you keep them from overgeneralizing your result to their own production schemas?**

Two corrections first. The DOUBLE PRECISION difference is in the generated SQL. Postgres casts to `DOUBLE PRECISION` and Snowflake and Databricks cast to `DOUBLE` (the three saved `.sql` files). It isn't a value gap the tolerances missed. The run does save the SQL next to the numbers, so it isn't values only. Also, Snowflake is a trial account (docs/TOUR.md), and the files don't say which Databricks edition ran.

The disclosure goes in the subtitle and the first paragraph, before any result. It would say: five of 43 metrics, one synthetic dataset (Meridian Valley, seed 42), a single month-end snapshot, a Snowflake trial account, and two runs, 2026-10-06 and 2026-10-07. The title stays, with the subtitle carrying the scope.

The results table is headed with the tolerances: call_volume 0, the two money metrics 0.01, the two rates 0.0001. Then I list what the run didn't cover, taken from the files:
- no timing or cost
- no larger scale
- no date functions in these five metrics' SQL
- no run of the Ossie export's expressions

The one value that differs textually is the mortgage rate, with a max_diff of 2.4e-08. I'd report it as seen and say the files don't say why. The last section tells readers to run `make parity` on their own schemas.

**Q7 — Your parity check missed the DOUBLE PRECISION cast issue you found by hand-reviewing SQL. The mortgage rate differs by 2.4e-08 and you never investigated why. You tested five of 43 metrics on static synthetic data with loose tolerances. What makes you confident publishing this under the title "One Metric, Three Warehouses" won't mislead readers into thinking the semantic layer is production-ready when you yourself haven't fully investigated these gaps?**

You're right to push on the title, and I wouldn't defend it without limits. Here is what the files support.

The cast difference is in the saved SQL. Postgres renders `AS DOUBLE PRECISION` and Snowflake and Databricks render `AS DOUBLE`. Both sides of the ratio use `CAST ... NULLIF(..., 0)` on all three targets [repo: results/semantics/2026-10-07_parity_*.sql]. Parity didn't miss a failure there. It's a dialect difference the layer absorbed.

On the 2.4e-08 gap, the files only show the decimals each warehouse returned: 6.588841176470588, 6.588841176 and 6.5888412 [repo: 2026-10-07_parity.json]. They don't say why, and I haven't investigated. The post will say that.

The run covers five of 43 metrics, one synthetic dataset, the stated tolerances, two run dates, and a Snowflake trial account [repo]. The Databricks edition isn't recorded in `results/semantics/`. The tolerances are exact for counts, a cent for money and 1e-4 for rates [repo: tools/parity.py].

The post will list what wasn't tested: the other 38 metrics, scale, timing, and any Ossie round trip. It will also say the three Ossie export issues aren't reported upstream [repo: docs/semantics/README.md].

I'm not confident the title is safe unscoped. I'd fix that with a subtitle that names the five metrics and the synthetic data, and the post will not use the phrase "production-ready".

**Q8 — You haven't tested this on production data, you haven't tested 38 of your 43 metrics, and you hand-found a cast issue the automated check missed entirely. In the body of the post itself—not buried in a disclaimer—what sentence keeps a reader from concluding this is a portfolio demo, not a validation that semantic layers are safe?**

The sentence goes in the results section, directly under the table, and it reads like this:

"This compared five of the 43 metrics across Postgres, Snowflake and Databricks, on one synthetic dataset, in runs dated 2026-10-06 and 2026-10-07, within the stated tolerances (exact for the count, a cent for the money, 1e-4 for the rates), and it does not tell you whether a semantic layer is safe on your data." [repo: results/semantics/README.md, tools/parity.py]

The next sentence names the gaps. The other 38 metrics were not run, and neither was production data, larger scale, timing or cost. Snowflake was a trial account, and the files don't record which Databricks edition I used. [repo: docs/TOUR.md, results/semantics/README.md]

I'd correct one point in the question. The cast issue was in the Ossie export, and the repo's own checks found it. The parity run never executed the export's expressions, so it could not have caught that issue. The export writes `(num) / (den)` without the `CAST ... NULLIF` that MetricFlow generates. The post says plainly that the export gives different numbers from `mf query` if you execute it directly. [repo: docs/semantics/README.md]

I'd rather a reader leave knowing exactly what was checked than leave with a stronger conclusion than the evidence supports.

**Q9 — If a reader sees your title "One Metric, Three Warehouses" and a table showing all three warehouses agreeing within tolerance, they'll form an opinion in those five seconds before they read your disclaimers—so where in the post do you actively overturn that first impression, not just disclose the scope limitations after the fact?**

The post is a draft, so this is where I plan to do it. The files don't record a finished layout.

The overturn goes directly under the table, in the first lines after it. A reader who sees a green table assumes the warehouses are interchangeable. The next lines say what they can verify: five of 43 metrics, one synthetic dataset, and runs on 2026-10-06 and 2026-10-07. The tolerances are stated too: exact for call_volume, 0.01 for the money metrics and 1e-4 for the two rates.

Then I'd show the SQL. The saved files are structurally identical. They differ only in relation quoting (backticks on Databricks) and `DOUBLE PRECISION` against `DOUBLE`. None of the five metrics uses a date function, so I can't claim the layer absorbed date differences.

The second move is the Ossie export. The table covers `mf query` output. The export's expressions were never executed. docs/semantics/README.md says a consumer running them directly "will get different numbers", and Postgres integer division would truncate the ratio to 0. [repo]

The earlier eval failure, Databricks backtick SQL run on Postgres, also goes in as evidence that dialect problems are real. [repo]

I'd also show the mortgage row in full. The README renders it as 6.589 everywhere, but the JSON has three different decimal lengths. The files don't say why. [repo]

**Q10 — You hand-found the DOUBLE PRECISION cast issue by eyeballing SQL—something your automated parity check completely missed—so what makes you confident you found all the dialect-specific issues, or do you expect readers to discover problems you didn't?**

I'm not confident I found all of them, and I expect readers on other data or metrics to find things I didn't.

One correction first. The `DOUBLE PRECISION` versus `DOUBLE` cast isn't a bug the parity check missed. The generated SQL differs there, and the values still agree. `tools/parity.py` diffs numbers against tolerances and only saves the SQL. It doesn't compare the SQL, so the cast difference is something I read in the three saved files.

What the evidence supports is narrow. `make parity` ran five metrics, out of 43, on three targets. It used one synthetic dataset and two run dates, 2026-10-06 and 2026-10-07, with identical values and byte-identical SQL. Snowflake was a trial account. The files don't record which Databricks edition ran. None of the five metrics' SQL uses date functions, so the run says nothing about date dialects. Nothing covers scale, timing or cost.

The Ossie export's expressions were never executed. Its problems were found by the repo's own checks, and they aren't reported upstream.

So the post will claim agreement on five named metrics within the stated tolerances, and list the rest as untested. [repo]
