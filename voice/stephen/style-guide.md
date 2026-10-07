# Style Guide: stephen

> **PRIVATE PRODUCTION PACK.** Real author. Private brain only; never the public Masthead repo.

Who the author is and what the content is for. Interviewers and editors read this for context;
`voice-guide.md` is the register authority; `claims-ledger.md` is the claims authority.

## Who
**Stephen Henderson.** Based in San Antonio, Texas. Has spent most of his career in field SE and
SA roles, understanding whether a product actually solves a customer's problem and helping the
customer decide what to do, at IBM, Microsoft, AWS, and a handful of startups. Background in
Android and backend engineering, databases, OO analysis and design, and modeling (statecharts
in particular). Current work is AI consulting. Runs Hendo Code, his own software and AI
consultancy. A heavy hands-on AI practitioner since early 2023.

What he cares about, which shapes the content: the craft first; equipping other teams to do
the work themselves; maps of systems (C4, diagrams) for people reading code they didn't write;
and the line "AI writes it, you understand it": validation, debugging and editing are the skills
that matter.

He writes to be useful to the reader. The approved About-page wording is the model:

> I've spent most of my career in field SE and SA roles, understanding whether a product
> actually solves a customer's problem, and helping them decide what to do. That's happened at
> IBM, Microsoft, and AWS, and at a handful of startups.
>
> The current work is in AI consulting. The Anchoring AI series documents one project in some
> detail: a RAG pipeline and MCP server built as a portfolio mirror of work I did for a credit
> union. Other posts here come from similar project work.
>
> I'm in San Antonio.

(Em dashes from the original were replaced with commas, per the voice guide.)

## Content types
- **Anchoring AI posts.** The public series built on `HendoCode/contact-center-ai`: retrieval
  benchmarks, the semantic layer across warehouses, the LangGraph agent and its evals, the
  agent harness. 1,200-2,500 words. Each post adds one layer the reader can build along.
- **Measurement write-ups.** One question, the method, the table, the limits, the verdict.
  900-1,600 words.
- **Corrections and post-mortems.** Something he or his agents got wrong, how it was caught,
  what changed. 600-1,200 words. Drawn from his corrections log.
- **Short how-tos.** A connector or a script someone else will want: the API surface, the
  setup, the code, the sharp edges. 800-1,500 words.
- **Social adaptations.** A LinkedIn or X version of a published post: one plain summary
  sentence, one specific from the post, and the link. No hooks, no emoji, no "I'm excited to share."

He does not write: industry trend pieces, hot takes, career advice, listicles, product
comparisons he hasn't run himself, or anything about a current client.

## Audience
Engineers, architects, field SEs and SAs, and the hiring managers who read their posts.
Technical and semi-technical readers who can tell when someone is bluffing. They skim headings,
check one number against the repo, and stop trusting the post if it's wrong. They respect
precision and are allergic to the same slop the author is.

## Sourcing and honesty rules
- Every number comes from a committed, rendered results file (for contact-center-ai,
  `results/<area>/README.md`) or from a figure Stephen stated, recorded in his Master CV. Name
  the file in `sources.md`. Never retype a number from memory.
- Every command shown is one that was run, in the form shown.
- The portfolio repo runs on synthetic data (the fictional Meridian Valley credit union, seed
  42). Say so where it matters. Describe it as a mirror of the consulting work; it holds none of
  the client's systems or data.
- Anything Stephen observed but didn't do is attributed ("the team chose...").
- Corrections to a published post go at the top, dated. Never silently edit a published post's
  claims.
- See `claims-ledger.md` for names that never appear and claims that have fixed wording.

## Formats and destinations
| Format | Length | Shape | Destination |
|---|---|---|---|
| Anchoring AI post | 1,200-2,500 words | task → mechanism → numbers → limits → verdict | contact-center-ai GitHub Pages |
| Measurement write-up | 900-1,600 words | question → method → table → limits → verdict | hendocode.github.io |
| Correction / post-mortem | 600-1,200 words | what happened → how it was caught → what changed | hendocode.github.io |
| Short how-to | 800-1,500 words | the need → API surface → setup → code → sharp edges | hendocode.github.io |
| Social adaptation | 40-90 words | one plain summary sentence, one specific, the link | LinkedIn, X |

Diagrams are Mermaid in Anchoring AI posts (the series renders it in the browser) and inline SVG
elsewhere. Captions state the point of the diagram.

## Assets (mention when relevant, never forced)
- The `HendoCode/contact-center-ai` repo and the Anchoring AI series.
- Hendo Code, as the name he publishes under.
- No mandatory call to action. A post may end with the repo link and the command to run it.

## Headings and titles
Every title and heading is literal, descriptive and functional: a reader knows what the section
contains before reading it. Front-load the subject and the action or result, in plain professional
language. A heading names the subject and says what the section does with it (how it works, what
was measured, what was excluded and why). A heading that needs the body to be understood fails.

Never use: literary or atmospheric fragments, narrative placeholders, vague noun phrases, a
prepositional fragment that omits the subject or action, "The anatomy of X," or any metaphor.
(A metaphor or analogy belongs in the body, never in a heading; see `voice-guide.md`.)

| Blocked (cryptic) | Allowed (literal, functional) |
|---|---|
| "Where the wait lives" | How Paused Agent States are Persisted in Postgres |
| "The bench under test" | Testing Environment and Dataset Parameters |
| "Two recall numbers" | Evaluating Capped Recall vs. ANN Recall Metrics |
| "A discarded early run" | Excluding the Initial Non-Comparable Benchmark Run |

Outline rule: when deciding sections and flow, write each heading first as a sentence saying what
the reader learns there, then trim it to a heading. Headings state the information or result; they
do not tease it. Existing rules still apply: no heading starts with "What," no decorative glyphs.

## Presentation hygiene
Checked by `editors/presentation-reviewer.md`:
- One `<h1>`, `<h2>` for sections, `<h3>` for subdivisions, no skipped levels.
- Plain descriptive headings, no decorative glyphs, no headings that start with "What."
- Commands in `<pre><code>`; identifiers in `<code>`.
- Tables for results, always with the date, hardware, and versions the results file records.
