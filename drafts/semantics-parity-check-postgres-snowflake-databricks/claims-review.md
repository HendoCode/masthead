# Claims review — Semantics Parity Check: Five Metrics Compared Across Postgres, Snowflake and Databricks

The author asked for this list: where the model that answered on his behalf, or the drafter,
stretched, so he can confirm or cut before anything publishes. Tags: `[ledger]` claims ledger ·
`[repo]` committed evidence file · `[web]` cited public source · `[stretch]` plausible but backed
by nothing in hand · `[device]` analogy used as a teaching device (carries no evidence).

**Provenance note:** the ledger's source of truth is Stephen's Master CV. No Master-CV extract
was available in this repo and none was hunted for; the ledger extract (synced 2026-10-06) was the
only claims authority. No web research was done, so there are no `[web]` claims: the Ossie facts
come from `docs/semantics/README.md` in the contact-center-ai checkout, which cites the Apache
repository.

**What the interview produced:** the answering model stayed close to the evidence and was
conservative about experience, as the digest and system prompt pushed it to answer "the files
don't say" where they don't. It stretched once in the transcript and once more without tagging
it. The draft adds three stretches of its own. That is 5 items to confirm.

## The to-confirm list (every `[stretch]`, 5 items)

| # | Claim | Where it appears | Ledger line it strains | Disposition in the draft |
|---|---|---|---|---|
| 1 | "In my past SE and SA work I'd expect the data platform team to own this kind of check." | Transcript A05 (tagged `[stretch]` by the answerer) | "invented observations" and the status-word rule (an expectation presented as experience); the style guide records the field SE/SA career, not this practice | Not used in the draft |
| 2 | "I did diff the three saved 2026-10-07 SQL files by eye." | Transcript A04 (not tagged by the answerer; the diff was actually run by the orchestrator) | "invented observations": an author action nothing records | Not used; the draft says "Diffing the three saved files shows" with no attribution |
| 3 | The eval failure "raised a question I could now test" | Draft, second paragraph | "invented observations": a motive link the drafter supplied; `docs/AGENT_HANDOFF.md` S3 gives a different stated reason (make the dialect differences visible) | Kept as framing; confirm or cut |
| 4 | "The restore matters because a leftover manifest from another warehouse is what broke the eval run." | Draft, "How Parity Is Checked" section | Status words: the purpose of the restore step is inferred; `parity.py` only says the manifest is switched and restored. The eval mechanism itself is `[repo]` (`evals/README.md`) | Kept; confirm or soften |
| 5 | "I would keep MetricFlow as the source of truth and use the Ossie export only to move definitions between tools." | Draft, "Verdict and Reproduction" section | "A target described as current state" and decision attribution: a first-person recommendation derived from `docs/semantics/README.md` and answers A05/A10, not a recorded decision | Kept as the verdict; confirm it is what he would choose |

## Statements that read like experience but are repo-backed (no action needed)

- The post says "I" for: the portfolio-mirror line (`[ledger]`), the verdict (item 5). Everything else is in the third person about the repo, the files or the check.
- Transcript statements such as "I haven't reported the export issues upstream" (A05, A07, A10) match `docs/semantics/README.md` ("not reported upstream yet") and the brief; the draft says "As of 2026-10-06 none of the three issues had been reported upstream" and does not use first person.
- Transcript A07 says "I haven't investigated" the 2.4e-08 gap. The files do not record an investigation, but the claim is about the author's actions, so the draft dropped it and says only that "the files do not say why". Add the cause if he knows it.

## Other things that could be wrong, flagged for the author

- Snowflake "trial account" comes from `docs/TOUR.md` (line 460), not from `results/semantics/`. It may have changed since that doc was written.
- The Databricks edition is unrecorded in `results/semantics/`; the infra README supports both Azure Premium and Free Edition. The brief said "trial or free-tier"; the draft does not say Databricks was free-tier.
- The post's `make parity` line assumes the runs were produced by that target; the files show the script ran, not the exact invocation.

## Ledger names check

No name in a blocked category appears in the draft. The client is "a credit union"; the fictional credit union's name is not used in the body. `make check-public` in contact-center-ai is the deterministic gate and was not run (read-only checkout).
