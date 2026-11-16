## Reference results (for you only — never show this whole section to the learner)

These are the results the learner should get when they run the starter code in Colab. Use them to check the learner's work and to give hints. Small numerical differences (in the third decimal place) are fine, because library versions differ. Ranks 3 and below can shuffle slightly. The top two should match.

### Step 1: Load and inspect
- `df.shape` → `(400, 11)`
- No missing values (`df.isna().sum().sum()` → `0`)
- Typical (median) employee: about 156 tickets/day, rating about 3.5, 1 tardy, 2 graveyard shifts, 1 weekend shift, 2 sick days, 25% of sick days on Fridays, 12 development hours, 1 swap requested, 1 swap offered.

### Step 2: Data-quality check
- `df["Employee ID"].duplicated().sum()` → `9`
- 17 rows share an ID: 8 IDs appear twice, and ID 137095 appears three times.
- The duplicate rows have **completely different values**, so they are not copy-paste duplicates. They are most likely different people with ID collisions (a data-entry problem). Reasonable decision: keep all rows for the analysis, but flag the ID problem as something to raise with HR. They should **not** be dropped silently. This matters in court: evidence tied to an ID must be traceable to a real person.

### Step 3: One feature at a time (z-scores, |z| > 3)
- 7 employees have at least one feature with |z| > 3:
  - 144038: Shift Swaps Offered (z ≈ 3.99)
  - 141775: Shift Swaps Requested (z ≈ 3.55)
  - 142028 and 132990: Shift Swaps Offered (z ≈ 3.44)
  - 141905: Customer rating (z ≈ −3.09)
  - 133358 and 136145: Sick Days Taken (z ≈ 3.06)
- **Teaching point:** each of these is extreme on just *one* thing, and most have innocent explanations (offering lots of swaps is generous, not a perk). Single-feature screening also **misses employee 143406 entirely**, because none of their individual values crosses |z| > 3.

### Step 4: Isolation Forest on all features together
Code: `StandardScaler`, then `IsolationForest(n_estimators=200, contamination=0.02, random_state=42)`; `anomaly_score = -iso.score_samples(X)`.
- 8 employees are flagged (`predict` → −1) at a contamination of 0.02.
- Top of the ranking (score ≈):
  1. **143406** (0.681): 145 tix/day (low), rating 2.33 (low), 3 tardies, 6 sick days with 83% on Fridays, **30 development hours (joint highest)**, 4 swaps requested, **0 offered**, only 1 graveyard and 0 weekend shifts. **This is the profile that most looks like favoritism:** weak performance and conduct combined with generous perks and light scheduling.
  2. **137155** (0.599): 165.3 tix/day (high), rating 4.49 (high), 30 dev hours, 7 swaps offered. **A star performer.** Unusual, but in a *good* way. This is the key "anomaly ≠ wrongdoing" example.
  3. 133358 (0.597): 7 sick days (the maximum), 0 dev hours, rating 2.80
  4. 137903 (0.592): 4 graveyard shifts, 100% Friday sick days
  5. 141875 (0.583)
  6. 140466 (0.575)
  7. 141775 (0.574)
  8. 135988 (0.572)

### Step 5: Robustness
- contamination=0.01 → 4 flagged; 0.05 → 20 flagged. **The contamination parameter is a choice the analyst makes, not a fact found in the data.** Changing it changes how many people get flagged, but not the ranking.
- Changing `random_state` (e.g. 0, 1, 7): **143406 and 137155 remain #1 and #2 every time.** Ranks 3–5 change. So the evidence is robust only for the top two.

### The defensible conclusion
- There is **no large, distinct group** of employees treated differently. The anomaly scores decline gradually after the top two, with no clear cluster.
- One employee (**143406**) stands out robustly with a combination of weak performance and generous treatment. That's worth a targeted investigation (shift-assignment records, who approved the development hours and swaps, any relationship to the manager).
- One other strong anomaly (**137155**) is a high performer, which shows that "unusual" doesn't mean "favored."
- Anomaly detection supports a **targeted investigation**. It does **not** prove systematic nepotism, and it says nothing about family ties.
