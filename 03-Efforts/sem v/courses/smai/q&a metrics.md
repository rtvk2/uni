# Classification Metrics — Practice Set

Work through these cold, on paper, before checking answers. Answer key is at the bottom — don't scroll ahead.

---

## Part A — Confusion Matrix Basics (orientation trap)

**A1.** A matrix is given with **predicted** labels on the rows and **actual** labels on the columns:

|                         | Actual: Spam | Actual: Not Spam |
| ----------------------- | :----------: | :--------------: |
| **Predicted: Spam**     |      40      |        10        |
| **Predicted: Not Spam** |      5       |       145        |

Treating "Spam" as the positive class, identify TP, FP, FN, TN.

**A2.** Same question, but now the matrix has **actual** on rows and **predicted** on columns:

|                      | Predicted: Spam | Predicted: Not Spam |
| -------------------- | :-------------: | :-----------------: |
| **Actual: Spam**     |       40        |         10          |
| **Actual: Not Spam** |        5        |         145         |

Identify TP, FP, FN, TN. (Compare to A1 — same numbers, different meaning.)

---

## Part B — Core Metrics Computation

Using this confusion matrix (positive class = "Disease"): TP = 30, FP = 20, FN = 10, TN = 140

**B1.** Compute Accuracy. **B2.** Compute Precision. **B3.** Compute Recall (Sensitivity). **B4.** Compute Specificity. **B5.** Compute F1-score. **B6.** Compute False Positive Rate (FPR).

---

## Part C — Scenario → Metric Translation

For each scenario, name the metric to prioritize and briefly justify:

**C1.** An airport security scanner must never let a weapon through, even if it means more manual bag checks.

**C2.** A recommendation engine suggests products; showing an irrelevant product barely bothers users, but users get frustrated if too many suggestions are irrelevant relative to how many they click.

**C3.** A binary classifier sorts survey responses into "positive sentiment" / "negative sentiment" for a report where both error types are equally costly and classes are roughly balanced.

**C4.** A wildfire-detection system flags satellite images for human review. Missing a real fire is catastrophic; a false alarm just costs a review-team's time.

---

## Part D — Accuracy Paradox

**D1.** A rare-disease dataset has 1,000 patients, of whom 20 actually have the disease. A model predicts "no disease" for everyone.

- What is its accuracy?
- What is its recall?
- Why is the accuracy number misleading here?

---

## Part E — F1 and Harmonic Mean Behavior

**E1.** Classifier X: Precision = 0.9, Recall = 0.9. Classifier Y: Precision = 1.0, Recall = 0.1. Compute F1 for both. Compare to what a simple arithmetic mean of Precision and Recall would give for Y. What does the gap tell you?

**E2.** True or false: F1-score treats Precision and Recall as equally important, so a classifier can achieve a high F1 by being excellent at one and mediocre at the other. Justify your answer.

---

## Part F — Micro / Macro / Weighted Averaging

A 3-class classifier (classes: Cat, Dog, Bird) gives this per-class breakdown:

| Class | TP  | FP  | FN  | Support |
| ----- | --- | --- | --- | ------- |
| Cat   | 8   | 2   | 2   | 10      |
| Dog   | 6   | 4   | 4   | 10      |
| Bird  | 1   | 0   | 4   | 5       |

**F1.** Compute per-class Precision for each class. **F2.** Compute Macro-Precision. **F3.** Compute Micro-Precision (pool TP/FP first). **F4.** Compute Weighted-Precision (weight by support). **F5.** In a single-label multi-class setting, Micro-Precision should equal what other overall metric? Verify this holds (or explain why it might not, given the numbers above). **F6.** If Bird is a rare, hard-to-detect class you especially care about, which averaging scheme best reflects that concern — macro or weighted? Why?

---

## Part G — Cost-Weighted Decisions

**G1.** An earthquake early-warning system has C_FP = 2 (cost units per false alarm) and C_FN = 50 (cost units per missed quake). Model A produces FP=10, FN=1. Model B produces FP=0, FN=3. Compute total cost for each and pick the better model.

---

## Part H — ROC / AUC

**H1.** A model reports AUC = 0.5. What does this tell you about its ranking ability?

**H2.** A model reports AUC = 0.3. Is this model necessarily useless? What could you do with it?

**H3.** True or false: A high ROC-AUC guarantees that the model's precision will also be high. Justify.

**H4.** On a dataset where negatives outnumber positives 999-to-1, a model has FP = 50 and TN = 9,950. Compute FPR. Now suppose those same 50 false positives are compared against only TP = 5. Compute Precision. What does the contrast between these two numbers illustrate?

---

## Part I — PR-AUC and Baselines

**I1.** Dataset 1 has prevalence (fraction positive) = 0.5. Dataset 2 has prevalence = 0.02. A random ("no-skill") classifier is run on each. What PR-AUC would you expect each random classifier to achieve, roughly?

**I2.** A model achieves PR-AUC = 0.4 on Dataset 2 above (prevalence 0.02). Is this good or bad? How do you know, and what would you need to check before answering "0.4 is bad"?

**I3.** Two models are compared on a fraud-detection task (positives are rare). Model A has ROC-AUC = 0.98, PR-AUC = 0.35. Model B has ROC-AUC = 0.95, PR-AUC = 0.55. Which model would you actually deploy, and why does this pair of numbers make sense together?

---

## Part J — Threshold Behavior

**J1.** A model's ROC-AUC is 0.92 (very good), but at the deployed threshold of 0.5, its Precision is only 0.2 and Recall is 0.95. What does this indicate, and what's one lever you could pull to improve Precision at the cost of Recall?

---

## Answer Key

**A1.** TP = 40 (predicted Spam, actual Spam), FP = 5 (predicted Spam, actual Not Spam), FN = 10 (predicted Not Spam, actual Spam), TN = 145.

**A2.** TP = 40, FP = 10, FN = 5, TN = 145. Notice FP and FN swapped relative to A1 even though the raw numbers in the table look similar — this is exactly the orientation trap. Always check which axis is "actual" before labeling cells.

**B1.** Accuracy = (30+140)/(30+20+10+140) = 170/200 = **0.85** **B2.** Precision = 30/(30+20) = **0.60** **B3.** Recall = 30/(30+10) = **0.75** **B4.** Specificity = 140/(140+20) = **0.875** **B5.** F1 = 2×(0.60×0.75)/(0.60+0.75) = 0.9/1.35 = **≈0.667** **B6.** FPR = 20/(20+140) = **0.125** (= 1 − Specificity, check: 1−0.875 = 0.125 ✓)

**C1.** Recall — cannot afford false negatives (a missed weapon), even at the cost of more false positives (extra checks). **C2.** Precision — the failure mode is too many irrelevant suggestions relative to good ones; false positives are the concern. **C3.** Accuracy — balanced classes, both error types equally costly, no special asymmetry to correct for. **C4.** Recall (near-zero tolerance for false negatives) — a missed fire is catastrophic while a false alarm is just a review cost, so pushing toward high recall (accepting more false positives) is correct.

**D1.** Accuracy = 980/1000 = **98%**. Recall = 0/20 = **0%**. The accuracy is misleading because it's driven entirely by the large majority (healthy) class — the model catches literally none of the actual disease cases, which recall reveals immediately and accuracy hides completely.

**E1.** F1(X) = 2×(0.9×0.9)/(0.9+0.9) = **0.9**. F1(Y) = 2×(1.0×0.1)/(1.0+0.1) = 0.2/1.1 ≈ **0.182**. Arithmetic mean of Y's precision/recall = (1.0+0.1)/2 = 0.55 — much higher than the harmonic-mean F1 of ≈0.18. The gap shows harmonic mean is dragged toward the smaller value (Recall here), correctly flagging Y as a poor overall classifier despite perfect precision, whereas an arithmetic mean would have hidden the collapse.

**E2. False.** F1 specifically punishes large gaps between Precision and Recall (see E1) — it does not reward one being excellent while the other is mediocre; both need to be reasonably high to get a high F1.

**F1.** Cat: 8/10 = 0.80. Dog: 6/10 = 0.60. Bird: 1/1 = 1.00 (TP=1, FP=0 → 1/(1+0)). **F2.** Macro-Precision = (0.80+0.60+1.00)/3 = **≈0.80** **F3.** Micro-Precision = ΣTP/(ΣTP+ΣFP) = (8+6+1)/((8+6+1)+(2+4+0)) = 15/21 ≈ **0.714** **F4.** Weighted-Precision = (0.80×10 + 0.60×10 + 1.00×5) / (10+10+5) = (8+6+5)/25 = 19/25 = **0.76** **F5.** In a single-label multi-class setting, Micro-Precision = Micro-Recall = Micro-F1 = overall **Accuracy**. Note: this table only gives TP/FP/FN, not TN, so you can't fully re-derive accuracy from it alone here — the equality still holds in principle for single-label data, but verifying it numerically needs the full confusion matrix (including correctly-rejected counts for each class), not just this per-class TP/FP/FN table. **F6.** **Macro** — it weights every class equally regardless of size, so Bird's poor performance (only 1 TP out of 5 actual instances, i.e. low recall) pulls the macro average down noticeably. Weighted-Precision would let Bird's small support (5, vs 10+10 for the others) mute its impact — exactly the opposite of what you want if Bird specifically matters.

**G1.** Model A: Cost = 2×10 + 50×1 = 20+50 = **70**. Model B: Cost = 2×0 + 50×3 = 0+150 = **150**. Model A is better (lower total cost) despite having more false positives — because false negatives are far more expensive here.

**H1.** AUC = 0.5 means the model's ranking is **no better than random guessing** — a randomly chosen positive is no more likely to be scored higher than a randomly chosen negative than chance would predict.

**H2.** Not necessarily useless — AUC = 0.3 means the model ranks negatives above positives more often than chance, i.e. it's **consistently wrong in a predictable direction**. Inverting its scores (or flipping predicted labels) would likely give AUC ≈ 0.7, recovering real ranking power.

**H3. False.** ROC-AUC summarizes ranking quality across all thresholds and doesn't involve TN in a way that reflects imbalance; a model can have high AUC while precision is still poor at a given operating point, especially under class imbalance (FPR's large TN denominator hides moderate absolute increases in FP).

**H4.** FPR = 50/(50+9950) = 50/10000 = **0.005** (looks tiny). Precision = 5/(5+50) = 5/55 ≈ **0.091** (quite poor — over 90% of "positive" flags are wrong). This contrast is exactly the ROC-blind-spot trap: FPR looks great because TN is huge, but Precision — driven by the ratio of FP to TP — reveals the predictions are mostly unreliable.

**I1.** Dataset 1 (prevalence 0.5): random classifier's PR curve sits at roughly **PR-AUC ≈ 0.5**. Dataset 2 (prevalence 0.02): random classifier's PR curve sits at roughly **PR-AUC ≈ 0.02**. The random baseline itself shifts with prevalence — unlike ROC's fixed 0.5 baseline.

**I2.** This is actually **good** — since the random baseline on Dataset 2 is only ≈0.02, a PR-AUC of 0.4 is roughly 20× better than chance. You'd need to know the dataset's prevalence (baseline) before judging any raw PR-AUC number — comparing 0.4 in isolation to some fixed standard (like "0.4 is mediocre") would be a mistake.

**I3.** **Model B** — despite the lower ROC-AUC, its PR-AUC is meaningfully higher (0.55 vs 0.35), which matters more for a rare-positive-class task like fraud detection. This is a direct instance of the ROC-optimistic-under-imbalance trap: Model A's high ROC-AUC is partly an artifact of the huge negative class, while PR-AUC (which ignores TN) shows Model B is actually better at finding fraud reliably.

**J1.** This indicates the model **ranks examples well overall (high AUC) but the chosen threshold of 0.5 is poorly calibrated for this precision/recall tradeoff** — it's flagging far too many negatives as positive at that cutoff. To improve Precision (at the cost of Recall), **raise the classification threshold** (require a higher predicted probability before calling something positive), which reduces FP but increases FN — moving up and to the left along the same ROC/PR curve rather than changing the model itself.

---

**How to use this:** redo any part you got wrong from scratch (not just reread the answer) 24 hours later. If Part A, F, or H/I trip you up a second time, that's your actual weak spot — drill those specifically, not the whole set again.