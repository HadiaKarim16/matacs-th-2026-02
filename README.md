# MATACS-TH-2026-02: AI Proposal Triage Design

- **Name:** Hadia Karim
- **Reference:** MATACS-TH-2026-02
- **Role:** Associate Software Developer
- **Time actually spent:** About 4 hours in one day.

## What this repository contains

| File | What it is |
|---|---|
| [diagram/flow.png](diagram/flow.png) | Process flow from upload to ranked output, showing AI steps, rule-based steps and human checkpoints |
| [WRITEUP.md](WRITEUP.md) | Answers to the five questions |
| [PROMPTS.md](PROMPTS.md) | The extraction prompt, as it would be sent |
| [ranking.json](ranking.json) | Expected ranking of proposals A to E |

## Summary of the design

AI is used for one step only: extracting facts from each proposal into JSON, with an
evidence quote for each fact. Mandatory checks, scoring and ranking are plain code
using the admin's versioned criteria, so the same extracted facts always give the same
score. Proposals with suspicious content or missing key facts are flagged for a human.
A reviewer confirms or overrides the ranking, and a person makes the final award decision.

## Assumptions

- Scores are calculated against the best eligible bidder (a relative scale), using
  the admin's weights.
- Proposals with missing key facts or suspicious content are flagged for a human, not
  scored or rejected automatically.
- Install dates that may fall outside the 5-year window are noted for the reviewer to
  confirm with the bidder.

## How I used AI tools

I used Claude to help plan the design, draft the idea of prompt, how to draft write-up and ranking, and also 
explain the steps for using draw.io. I reviewed everything before committing
and drew the diagram myself in draw.io, rather than using a diagram the AI generated.

Where I corrected the AI: its first draft of the extraction prompt had a rule (the
licence rule) that said "as described below" when nothing was described below, so I
replaced it with a clear rule covering true, false and null cases. Its first rule
about hidden instructions also used examples that were too close to the wording in
the sample proposals, so I changed them to generic wording because the brief says not
to copy sample text into the repository. I also checked the scoring formulas and
recalculated the scores for A and E by hand before keeping them.