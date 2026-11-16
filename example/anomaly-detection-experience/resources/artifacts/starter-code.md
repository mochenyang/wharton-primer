# Colab Starter Code: Anomaly Detection

Run each step in its own Colab cell. First upload `call-center.csv` using the folder icon in the left sidebar.

## Step 1: Load and inspect

```python
import pandas as pd
import numpy as np
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import IsolationForest

df = pd.read_csv("call-center.csv")
print(df.shape)
print("Missing values:", df.isna().sum().sum())
df.describe().round(2)
```

## Step 2: Data-quality check (duplicate IDs)

```python
print("Duplicated IDs:", df["Employee ID"].duplicated().sum())
df[df["Employee ID"].duplicated(keep=False)].sort_values("Employee ID")
```

## Step 3: One feature at a time (z-scores)

```python
features = df.columns.drop("Employee ID")
z = (df[features] - df[features].mean()) / df[features].std()

extreme = z.abs() > 3
print("Employees with any |z| > 3:", extreme.any(axis=1).sum())
df.loc[extreme.any(axis=1), ["Employee ID"]].assign(
    extreme_on=extreme[extreme.any(axis=1)].apply(
        lambda row: ", ".join(row.index[row]), axis=1)
)
```

## Step 4: Isolation Forest (all features together)

```python
X = StandardScaler().fit_transform(df[features])

iso = IsolationForest(n_estimators=200, contamination=0.02, random_state=42)
iso.fit(X)

df["anomaly_score"] = -iso.score_samples(X)   # higher = more unusual
df["flagged"] = iso.predict(X) == -1          # True = flagged as anomaly

print("Flagged:", df["flagged"].sum())
df.sort_values("anomaly_score", ascending=False).head(10).round(3)
```

Compare the top rows with a typical employee:

```python
df[features].median()
```

## Step 5: Robustness

```python
# Does the top of the ranking change with a different random seed?
for seed in [0, 1, 7]:
    scores = -IsolationForest(n_estimators=200, random_state=seed).fit(X).score_samples(X)
    top5 = df["Employee ID"].iloc[np.argsort(-scores)[:5]].tolist()
    print("seed", seed, "top 5:", top5)

# How many get flagged under different contamination settings?
for c in [0.01, 0.02, 0.05]:
    flagged = IsolationForest(n_estimators=200, contamination=c, random_state=42).fit(X).predict(X) == -1
    print("contamination", c, "->", flagged.sum(), "flagged")
```
