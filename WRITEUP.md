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