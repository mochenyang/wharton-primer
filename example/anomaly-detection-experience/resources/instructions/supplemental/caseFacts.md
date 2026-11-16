## The case: Call Center Staffing Analytics — Detecting Favoritism

**Situation.** A call center operation is under close scrutiny for uneven performance, shoddy operations, and low employee morale. There is a rumor that the call center manager is engaging in favoritism, that certain employees get unfairly easy working conditions, but nobody has made a convincing case.

**Complication.** Some go further and accuse the manager of nepotism, meaning the workers getting sweet deals are the manager's relatives. The matter is now before a judge who, given how serious it is, has asked for hard evidence.

**Key question.** Can we use machine learning to identify "objectively" whether certain employees are treated in a systematically different way from others, using not one but multiple dimensions together? The method is **anomaly detection**, an unsupervised machine learning technique. The answer could be evidence of favoritism, or a lack of it.

**Data: `call-center.csv`.** One row per employee record, 400 rows, 11 columns, no missing values:

| Column | Meaning |
|---|---|
| Employee ID | Identifier |
| Avg Tix / Day | Average tickets handled per day |
| Customer rating | Average customer rating (roughly 1–5) |
| Tardies | Number of times late |
| Graveyard Shifts Taken | Night shifts worked |
| Weekend Shifts Taken | Weekend shifts worked |
| Sick Days Taken | Sick days used |
| % Sick Days Taken on Friday | Share of sick days that fell on a Friday |
| Employee Dev. Hours | Paid professional-development hours received |
| Shift Swaps Requested | Times the employee asked a colleague to swap shifts |
| Shift Swaps Offered | Times the employee offered to cover a colleague's shift |

**What "favoritism" might look like in the data.** A favored employee might get perks (many development hours, few graveyard or weekend shifts, swap requests granted) despite weak performance (few tickets, low ratings) or poor conduct (tardies, suspicious Friday sick days). The point is that it's the *combination* that's suspicious. Any single number can have an innocent explanation.

**Important limits.** The data contains no information about family relationships. Anomaly detection can show that someone is *statistically unusual*. It cannot show *why*, and it cannot prove nepotism on its own.
