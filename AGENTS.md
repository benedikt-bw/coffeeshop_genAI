# AGENTS.md

Context for coding agents working in this repo. The source PDFs in `docs/` are git-ignored,
so this file is the canonical summary of the brief. Do not re-read the PDFs unless something
here is missing or contradictory.

## The task (Case 04 — "Make customer feedback useful to a manager")

HEC Paris · MSc DSAIB 2026–2027 · *Generative and Agentic AI for Business* (Shubin Yu) ·
afternoon AI solution sprint, 3 hours, groups of 1–3.

**Business situation.** Juniper Café (fictional) has three branches: **Central, Riverside,
Station**. The manager receives feedback via a web form and messages forwarded by branch staff.
Before the weekly operations meeting they read the comments and try to spot what deserves
attention. A colourful complaint can dominate the discussion while recurring, less dramatic
issues are missed. The comments are a **convenience sample, not a customer survey**. Repeated
messages, mixed opinions and branch differences make a simple positive/negative summary
inadequate.

**Goal.** Build a *reusable* AI solution that turns a batch of customer comments into a
**concise, traceable briefing** for the weekly operations meeting: what customers mention,
where the evidence comes from, and which small improvement to investigate first.

### Requirements

1. Identify useful themes and **retain the original comment IDs**. Handle comments with
   multiple themes, conflicting views and very little usable detail.
2. **Handle the identified duplicate explicitly.** Show counts with a **clear denominator**;
   do not count the same visit twice or invent missing feedback.
3. **Connect each finding to evidence.** Separate what the comments *say* from *interpretation*;
   avoid claims about all customers or about the causes of a problem.
4. Suggest **one small action to investigate or test**, including what extra information would
   help. The **manager reviews the brief and decides** — the tool does not decide.

### Decisions the group must make (and document)

- What counts as one observation: a message, a visit, or a theme mention? Can a comment belong
  to several themes? (Pack says yes: theme counts need not sum to number of visits.)
- How does the manager inspect the evidence behind a summary and notice contradictory comments?
- Which part needs GenAI, and which counts / duplicate checks can be done **deterministically**?

Possible measures: factual support, count accuracy, useful theme coverage, preparation time.
The natural EDGE target is **Decisions** (more accurate, relevant, helpful information).

### Starter data

Verbatim copy in `data/feedback_batch.csv`.

- **F07 is a forwarded copy of F03 (same visit V03).** All other visit IDs are distinct →
  12 messages, **11 unique visits**.
- The batch has **no** sales totals, complete customer counts or staffing records. Never
  infer them.
- Theme definitions are ours to design and document.

### Test variations suggested by the brief

Empty batch · an additional duplicate · a comment with two opposing opinions · a new branch ·
a comment with no identifiable issue. Test on *changed* batches — the number of comments is not
the number of tests. Need **≥ 10 tests** covering routine, missing-information and difficult
cases, each run against the **same baseline**. Label any added/simulated data as such.

## Deliverables

1. **Demo video (3–5 min):** problem, user, value target; why this solution form; working
   prototype on a routine input **and** a difficult/failure case; test results, limitations,
   next improvement.
2. **Appendix** (doc, notebook or ZIP) containing:
   - Short design rationale: workflow, 5A choice and trade-offs, human responsibilities,
     EDGE target.
   - Reproduction info: solution files / prompts / config, data, tool & model details, run
     instructions, **group members' names and contributions**.
   - Compact test table: ≥ 10 inputs/conditions, expected behaviour, baseline result,
     prototype result, pass/fail. Summarise **quality, time (latency) and cost**; label
     estimates or unavailable measures.

No slide deck or separate report. **A static example alone is not a reusable solution** — it
must demonstrably handle new inputs.

### Evaluation criteria

- **Business value:** clear user need, meaningful baseline, measurable benefit.
- **Design choices:** justified 5A form, useful GenAI contribution, appropriate human control.
- **Reliability:** working, reproducible, handles missing information, exposes limits.
- **Evidence:** fair tests, honest trade-offs. *Extra agents or code earn no credit by
  themselves* — justify complexity.

### Suggested 3-hour plan

| Time | Stage | Work |
|---|---|---|
| 00:00–00:20 | Scope | One user, workflow, EDGE target, success measure. Set a baseline (manual process, template, or single prompt). |
| 00:20–02:00 | Build | Pick 5A form, build reusable prototype. Make sources, missing info and human review visible. |
| 02:00–02:40 | Test | ≥ 10 small tests vs. the baseline; routine, missing-info, difficult cases; improve. |
| 02:40–03:00 | Submit | Record demo, package appendix. |

## Course frameworks (from the morning lecture)

Flow: **Diagnose** (find the bottleneck, define value) → **Justify** (compare solutions and the
autonomy they need) → **Design** (evidence, people, control points) → **Evaluate** (defend a
pilot, build and test it). "Observe before automating."

**EDGE** — what improves: **E**fficiency (time per task, e.g. 6 → 4 min with review),
**D**ecisions (accurate, relevant, actionable), **G**rowth (revenue/volume), **E**mpowerment
(less stress, new capabilities). Quality and value are measured separately: quality =
correct facts, completeness, appropriate next step; value = time to accepted output, review and
rework, adoption.

**5A solution menu** — pick the form that fits the work:

| Form | What | Strength | Limit | Good fit |
|---|---|---|---|---|
| Access | Paste input + facts into a chat model | No build, flexible | Repeated context, manual checking | One-off tasks |
| Assistants | Shared instructions + knowledge base | Reusable, grounded | Stale sources, retrieval gaps | Repeated knowledge work |
| Applications | Focused UI, review facts beside the output | Clear review, configurable | Build effort, maintenance | Stable repeated task |
| Automation | Fixed known route (e.g. n8n); missing fact → ask, exception → refer | Repeatable, traceable runs | Brittle branches, integration upkeep | Known route |
| Agents | Observe → plan → choose allowed tool → check → stop/ask | Adaptive | Variable cost, autonomous errors | Next step depends on results |

A template may already solve half the problem — always compare against a simple baseline.

**Agentic patterns** (optional; use only with a reason): Reflection (draft → compare with
facts → revise, bounded), Tool use (fetch evidence, then use it), ReAct (result guides next
step), Planning (split a combined request), Multi-agent (distinct roles need a reason, e.g.
retrieve / draft / review-challenge-unsupported-claims).

**Define the output you will accept** (skill-style spec): output, evidence (every claim
supported), boundary (human approves), missing information (ask or refer). A good human
handoff says *what is missing* and *what decision is still needed*.

## Repo conventions

- Python 3.12, managed with **uv** (`uv sync`, `uv run ...`). Virtualenv in `.venv/`.
- Secrets in `.env` (git-ignored); see `.env.example`.
- `data/` — input batches. `feedback_batch.csv` is the official starter pack; keep it
  unchanged. Put test variations in `data/tests/`, clearly labelled as simulated.
- `src/coffeeshop/` — solution code. `tests/` — pytest tests. `outputs/` — generated
  briefings (git-ignored). `appendix/` — submission material (rationale, test table).
- Keep deterministic work (parsing, dedup by visit ID, counting, denominators) in plain code;
  use the LLM only where judgement is needed (theme tagging, wording). Every claim in a
  briefing must cite comment IDs.
