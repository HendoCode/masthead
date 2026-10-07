# Turn 02 interviewer input (exact)
Model: claude-haiku-4-5-20251001, fresh process, cwd outside any repo, --no-session-persistence.
System prompt: the verbatim contents of interviewers/domain-specialist.md, shown next.

## System prompt
````
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
````

## User prompt
````
TOPIC: A blog post titled "One Metric, Three Warehouses". Thesis: declare a metric once in a semantic layer (dbt + MetricFlow) and compile it for three SQL dialects (Postgres, Snowflake, Databricks); a parity check compares the numbers, and the post shows what the layer absorbed and what an interchange format (Apache Ossie) kept and dropped. Reader: analytics engineers and data-platform teams weighing a semantic layer or an open interchange format. The author is being interviewed to supply raw material.

TRANSCRIPT SO FAR:
Q01: In practice, when you're comparing this metric across all three warehouses, are you testing against identical source data in each, or are the underlying tables actually different—different grain, different freshness, maybe different slowly-changing-dimension handling—and if so, what counts as "parity" in your actual validation workflow?

A01: It's identical source data. All three warehouses get the same synthetic dataset (Meridian Valley credit union, seed 42), so grain, freshness and slowly-changing-dimension handling aren't varied. [repo: olap/dbt/README.md] The loaders reuse the table list, columns and preparers from `seed.py`. Each table ends with a `count(*)` that must equal the rows sent. [repo] The column types are mapped per warehouse, for example NUMERIC to NUMBER or DECIMAL. [repo]

Freshness doesn't enter into it, because the data is static. `f_account_snapshot` holds one month-end snapshot (2026-08-31) per account, so there's no history and no SCD logic in play. [repo] The files don't record any table-by-table comparison beyond that row-count check.

What I call parity is narrow. `make parity` runs five metrics through MetricFlow on each target and diffs the values to a per-metric tolerance. Those are 0 for call_volume, 0.0001 for the mortgage rate and the first-contact-resolution rate, and 0.01 for the two balances. [repo: tools/parity.py] Both runs (2026-10-06 and 2026-10-07) report "All targets agree within tolerance." [repo: results/semantics/README.md] The mortgage rate differs only in decimals returned, with a max_diff of 2.4e-08. [repo: 2026-10-07_parity.json] The files don't say why.

That covers five of 43 metrics, one dataset, and a Snowflake trial account. The files don't record which Databricks edition ran. [repo]


Ask exactly ONE question now, in the style of your persona. Output only the question text, nothing else.
````
