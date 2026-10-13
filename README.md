# coffeeshop_genAI

HEC Paris MSc DSAIB · GenAI sprint · **Case 04: Make customer feedback useful to a manager.**
This tool turns a batch of Juniper Café customer comments into a short briefing for the weekly
operations meeting. Every point in the briefing cites the comment IDs it comes from.

The brief and course context are summarised in [AGENTS.md](AGENTS.md).

## Setup

```bash
uv sync
cp .env.example .env   # add your ANTHROPIC_API_KEY
uv run pytest
```

## Layout

| Path | Contents |
|---|---|
| `data/feedback_batch.csv` | Official starter batch: 12 messages, 11 visits (F07 duplicates F03) |
| `data/tests/` | Labelled, simulated test batches |
| `src/coffeeshop/` | Solution code |
| `tests/` | pytest tests |
| `outputs/` | Generated briefings (git-ignored) |
| `appendix/` | Submission: design rationale, test table, group contributions |
| `docs/` | Source PDFs (git-ignored) |
