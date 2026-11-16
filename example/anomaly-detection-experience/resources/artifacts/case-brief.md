# Case Brief: Detecting Favoritism at a Call Center

**Situation.** A call center has uneven performance, shoddy operations, and low morale. Rumor has it the manager plays favorites, giving certain employees unfairly easy working conditions.

**Complication.** Some accuse the manager of *nepotism*: the favored workers are said to be relatives. The case is before a judge who wants **hard evidence**.

**Key question.** Can machine learning *objectively* show whether certain employees are treated systematically differently, using **multiple dimensions together**?

**Method.** Anomaly detection, an unsupervised machine learning technique.

## Data: `call-center.csv` (one row per employee)

| Column | Meaning |
|---|---|
| Employee ID | Identifier |
| Avg Tix / Day | Average tickets handled per day |
| Customer rating | Average customer rating |
| Tardies | Times late |
| Graveyard Shifts Taken | Night shifts worked |
| Weekend Shifts Taken | Weekend shifts worked |
| Sick Days Taken | Sick days used |
| % Sick Days Taken on Friday | Share of sick days on a Friday |
| Employee Dev. Hours | Paid development hours received |
| Shift Swaps Requested | Times they asked others to cover |
| Shift Swaps Offered | Times they offered to cover others |

## Getting set up in Google Colab
1. Go to **colab.research.google.com** and create a new notebook.
2. Click the **folder icon** in the left sidebar, then the **upload** icon.
3. Upload `call-center.csv` (your instructor provides it).
