# Write-up

## 1. AI vs. rules

**AI is used for one step only:** reading each proposal's messy text and extracting
facts (price, weeks, warranty years, licence holder, install count) into JSON, with an
evidence quote for each fact. The proposals are written in free text and differ in
wording, which is where AI is useful.

**Everything else is plain code or rules:** loading the admin's criteria, checking
the mandatory pass/fail criteria, calculating weighted scores, ranking, and logging.
The final decision is made by a human.

**Why the line is there:** pass/fail checks and maths must give the same answer
every time and be explainable, and AI can vary or make mistakes. If the AI only
extracts, a wrong result can be traced to a specific quote and corrected, and the AI
never decides who wins.


## 2. Consistency and explainability

**Would ranking the same proposals twice give the same result? Yes.**
The AI runs once per proposal at temperature 0, and its extracted JSON is saved.
Scoring is a fixed formula in code, so the same JSON and the same criteria version
always give the same scores. Re-ranking reuses the saved JSON and does not call the
AI again. AI output can still vary slightly between runs, so re-extracting is a
deliberate, logged action, and any changed fact is shown to the reviewer.

**How a reviewer sees why a proposal ranked where it did:**
- Each proposal has a per-criterion breakdown: the extracted value, the points
  earned, and the formula used.
- Each extracted fact shows its evidence quote from the proposal, which code has
  checked exists in the source text.
- Ineligible and flagged proposals show the reason in plain words, for example
  "licence not held by the bidder" or "no schedule given".
- The page shows which criteria version and prompt version produced the result.

## 3. Human in the loop

**Where a person reviews, confirms or overrides:**
- **Flagged proposals:** anything with a suspicious instruction, a missing key fact
  or an unclear item is held back. It cannot be ranked until a reviewer clears it,
  asks the bidder for clarification, or rejects it.
- **The draft ranking:** the reviewer sees the full ranked list with reasons and
  must confirm it. They can move a proposal up or down, or change an extracted
  fact after checking it against the evidence quote. Every override needs a
  written reason.
- **Ineligible proposals:** shown with the failed criterion so the reviewer can
  confirm the decision or correct a wrong extraction.
- **The award:** the tool only recommends a ranking. A human makes the final award
  decision.

**What gets logged for audit:**
- Who uploaded and who reviewed, with timestamps
- The category, criteria version (mandatory criteria and weights) and prompt version
- The model name and settings
- The extracted JSON, including evidence quotes, and which quote checks passed or failed
- Flags raised and how each was resolved
- Scores per criterion and the ranking the tool produced
- Every override: the original value, the new value, who changed it and why
- The final confirmed ranking


## 4. Changing criteria

**Criteria are versioned.** An admin edit never changes the old record. It creates
a new version (v1, v2, ...) holding the mandatory criteria, weighted criteria and
weights. Every score and ranking stores the criteria version that produced it, so
old results stay accurate and explainable.

**What happens to proposals already scored:**
- They keep their old scores and show a notice: "scored with v1, current is v2".
- The reviewer can choose "re-score with v2". This reuses the saved extracted JSON,
  so a weight change needs no new AI call and no extra cost.
- If a new criterion needs a fact that was not extracted before, only the AI
  extraction step runs again for the proposals concerned, and the change is logged.
- The old and new rankings are shown side by side with the difference explained
  (for example, "weight on price changed from 25% to 30%").
- Weights must total 100%, and the tool blocks saving a version that doesn't.

**Safeguard:** a ranking that has already been confirmed by a reviewer is never
changed automatically. A new version creates a new draft for review.