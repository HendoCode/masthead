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
