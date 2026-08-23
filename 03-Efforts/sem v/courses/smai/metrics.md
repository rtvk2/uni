# Classification Metrics Glossary

**✱ = slides.** Unstarred terms are extra reference material (from StatQuest/sklearn/Wikipedia) not in slides. 
## 1. The Confusion Matrix — Foundations

**✱ Confusion Matrix** A table comparing predicted class labels against actual class labels. For binary classification it's 2×2:

| |Predicted Positive|Predicted Negative|
|---|---|---|
|**Actual Positive**|True Positive (TP)|False Negative (FN)|
|**Actual Negative**|False Positive (FP)|True Negative (TN)|

**✱ True Positive (TP)** Model predicted positive, and the actual label is positive. A correct "hit."

**✱ True Negative (TN)** Model predicted negative, and the actual label is negative. A correct "rejection."

**✱ False Positive (FP) — Type I Error** Model predicted positive, but the actual label is negative. Also called a "false alarm." Example: a healthy patient flagged as having a disease.

**✱ False Negative (FN) — Type II Error** Model predicted negative, but the actual label is positive. Also called a "miss." Example: a diseased patient flagged as healthy — usually the more dangerous error in medical contexts.

**✱ Positive Class / Negative Class** The class of interest (positive, e.g. "has disease," "is spam") vs. the reference class (negative). Choice is convention-dependent but changes how every metric below reads.

**✱ Class Imbalance** When one class vastly outnumbers the other in the dataset (e.g. 99% negative, 1% positive — fraud detection, rare disease screening). Imbalance distorts accuracy and ROC-AUC, and is the reason PR-AUC, MCC, and balanced accuracy exist.

**✱ Accuracy Paradox** The phenomenon where a model achieves very high accuracy simply by always predicting the majority class, while being useless at detecting the minority class. Classic symptom of trusting accuracy under class imbalance.

---

## 2. Rate-Based Metrics Derived from the Confusion Matrix

**✱ Accuracy** `(TP + TN) / (TP + TN + FP + FN)` Fraction of all predictions that were correct. Misleading under class imbalance.

**✱ Sensitivity / Recall / Hit Rate / True Positive Rate (TPR)** `TP / (TP + FN)` Of all actual positives, what fraction did the model correctly catch? High recall = few false negatives. Prioritized in medical screening (don't miss the sick patient).

**Specificity / Selectivity / True Negative Rate (TNR)** `TN / (TN + FP)` Of all actual negatives, what fraction did the model correctly identify? High specificity = few false positives. The "mirror image" of recall, computed on the negative class.

**✱ Precision / Positive Predictive Value (PPV)** `TP / (TP + FP)` Of everything the model flagged as positive, what fraction was actually positive? High precision = few false positives among the predictions. Prioritized in spam filtering (don't wrongly block real email).

**Negative Predictive Value (NPV)** `TN / (TN + FN)` Of everything predicted negative, what fraction was actually negative. The precision-equivalent for the negative class.

**✱ Fall-out / False Positive Rate (FPR)** `FP / (FP + TN) = 1 − Specificity` Of all actual negatives, what fraction was wrongly flagged as positive. This is the x-axis of the ROC curve.

**Miss Rate / False Negative Rate (FNR)** `FN / (FN + TP) = 1 − Sensitivity` Of all actual positives, what fraction was missed.

**False Discovery Rate (FDR)** `FP / (FP + TP) = 1 − Precision` Of everything flagged positive, what fraction was wrong.

**False Omission Rate (FOR)** `FN / (FN + TN) = 1 − NPV` Of everything predicted negative, what fraction was actually positive (missed).

**Prevalence** `(TP + FN) / Total` The actual proportion of positives in the dataset — a measure of class imbalance itself.

---

## 3. Combined / Summary Metrics

**✱ F1-Score** `2 × (Precision × Recall) / (Precision + Recall)` The harmonic mean of precision and recall. Punishes extreme imbalance between the two (unlike the arithmetic mean) — a model can't game F1 by maximizing only one metric while the other collapses.

**F-beta Score (Fβ)** `(1 + β²) × (Precision × Recall) / (β² × Precision + Recall)` Generalizes F1 with a weight β that controls the relative importance of recall vs. precision.

- β = 1 → F1 (equal weight)
- β < 1 (e.g. F0.5) → weights precision more heavily
- β > 1 (e.g. F2) → weights recall more heavily (used when missing positives is costlier, e.g. disease screening)

**Balanced Accuracy** `(Sensitivity + Specificity) / 2` Averages recall across both classes. A fairer alternative to raw accuracy under class imbalance.

**Matthews Correlation Coefficient (MCC)** `(TP×TN − FP×FN) / √[(TP+FP)(TP+FN)(TN+FP)(TN+FN)]` A single correlation-like score between −1 (total disagreement) and +1 (perfect prediction), with 0 meaning random guessing. Uses all four confusion-matrix cells symmetrically, making it one of the most reliable single metrics under severe class imbalance.

**Cohen's Kappa** Measures agreement between predicted and actual labels, corrected for the agreement expected by chance alone. Ranges roughly −1 to 1. Useful when comparing classifiers (or human raters) against a chance baseline, especially with imbalanced classes.

**Informedness (Youden's J statistic)** `Sensitivity + Specificity − 1` Measures how much better than random chance the classifier's positive/negative calls are.

**Markedness** `Precision + NPV − 1` The predictive-value analogue of Informedness — how much better than chance the model's predictions are, from the prediction side rather than the actual-label side.

**✱ Accuracy Paradox — worked numeric form** If accuracy is computed as `(TP + TN) / Total`, a model that predicts almost everyone negative in a rare-disease pool (e.g. 3 flagged positive out of 100, several of them wrong) can still post ~96% accuracy while catching very few true cases — because TN dominates the count. This is why Precision and Recall are reported alongside Accuracy for imbalanced problems.

**✱ Detection Cost** `Cost = C_FP × FP + C_FN × FN` A weighted-cost formulation used when false positives and false negatives carry different real-world costs (e.g. earthquake prediction: a false positive costs preventive measures, a false negative costs disaster recovery). `C_FP` and `C_FN` are the cost-per-error weights, chosen by the application, not derived from the data.

---

## 4. Threshold, Score, and Curve Concepts

**Classification Threshold (Decision Threshold)** The cutoff probability (commonly 0.5) above which a model's continuous output score is converted into a "positive" prediction. Every metric above depends on where this threshold is set — moving it trades recall for precision (or TPR for FPR) in opposite directions.

**Predicted Probability / Score** The raw continuous output of a probabilistic classifier (e.g. 0.83) before thresholding into a hard class label.

**ROC Curve (Receiver Operating Characteristic Curve)** A plot of True Positive Rate (y-axis) against False Positive Rate (x-axis) as the classification threshold is swept from 1 to 0. Each point on the curve corresponds to one threshold value.

**AUC / AUROC (Area Under the ROC Curve)** A single scalar (0 to 1) summarizing the ROC curve. Interpreted as the probability that a randomly chosen positive example is ranked (scored) higher than a randomly chosen negative example. AUC = 0.5 is random guessing; AUC = 1.0 is a perfect ranker.

**Diagonal Line / Chance Line** The line from (0,0) to (1,1) on an ROC plot, representing a random classifier (TPR = FPR at every threshold). ROC curves are judged by how far they bow above this line.

**Precision-Recall (PR) Curve** A plot of Precision (y-axis) against Recall (x-axis) as the threshold is swept. More informative than ROC when the positive class is rare, because it doesn't involve TN (which dominates and inflates apparent performance under imbalance).

**PR-AUC / Average Precision (AP)** The area under the Precision-Recall curve. Preferred over ROC-AUC for heavily imbalanced datasets since it is sensitive to the model's performance specifically on the minority (positive) class.

**Why ROC can look "misleadingly optimistic" under imbalance** Because FPR's denominator (FP + TN) is dominated by the huge number of true negatives, even a large _absolute_ number of false positives barely moves FPR — so ROC-AUC can stay high while precision (which reacts directly to FP relative to TP) crashes. PR curves expose this; ROC curves can hide it.

**Baseline of a PR Curve** Equal to the class prevalence (fraction of positives). A random classifier's PR curve is a horizontal line at this value, unlike ROC's fixed diagonal — meaning "good" PR-AUC is dataset-dependent.

**Youden's Index** The threshold on an ROC curve that maximizes `Sensitivity + Specificity − 1`; a common rule for picking an "optimal" cutoff.

**Isotonic / Platt Scaling** Post-hoc calibration methods that adjust a model's raw scores so predicted probabilities better match true observed frequencies (related to calibration/Brier score below).

---

## 5. Probabilistic / Calibration Metrics

**Log-Loss (Cross-Entropy Loss / Logistic Loss)** `−(1/N) Σ [y·log(p) + (1−y)·log(1−p)]` Penalizes predicted probabilities based on how far they are from the true label, with heavy penalty for confident-but-wrong predictions. Lower is better; unlike accuracy, it rewards well-calibrated probabilities, not just correct hard labels.

**Brier Score** `(1/N) Σ (p − y)²` The mean squared difference between predicted probability and actual outcome (0 or 1). Measures calibration: a well-calibrated model's Brier score is low. Ranges 0 (perfect) to 1 (worst).

**Calibration** The degree to which predicted probabilities match real-world frequencies — e.g., among all cases predicted with 70% probability, roughly 70% should actually be positive. A model can have great AUC (good ranking) but poor calibration (bad probability values).

**Calibration Curve / Reliability Diagram** A plot of predicted probability (binned) vs. observed frequency of the positive class, used to visually assess calibration.

---

## 6. Multi-Class Averaging (extending binary metrics)

**✱ Micro-Averaging / Micro F1-Score** Pools TP, FP, FN across _all_ classes globally first, then computes one Precision, Recall, and F1 from those totals: `Micro-Precision = ΣTP / (ΣTP + ΣFP)`, `Micro-Recall = ΣTP / (ΣTP + ΣFN)`, `Micro-F1 = ΣTP / (ΣTP + ½(ΣFP + ΣFN))` Effectively weights each _instance_ equally, so large classes dominate. In single-label multi-class classification, Micro-Precision = Micro-Recall = Micro-F1 = overall Accuracy.

**✱ Macro-Averaging / Macro F1-Score** Computes the metric (e.g. F1) independently for _each class_ first, then takes the plain, unweighted mean across classes: `Macro-F1 = (F1_class1 + F1_class2 + ... + F1_classN) / N` Treats every class equally regardless of size — a rare class with poor performance drags this down just as much as a common one, making it the go-to choice when small/minority classes matter as much as large ones.

**✱ Weighted Averaging / Weighted F1-Score** Like macro-averaging, but each class's per-class F1 is weighted by its **support** (number of true instances of that class) before summing: `Weighted-F1 = Σ (F1_classᵢ × Supportᵢ) / Σ Supportᵢ` Balances between micro (instance-level, size-dominated) and macro (class-level, size-blind) behavior — reflects class imbalance without letting one class fully drown out the others.

**Rule of thumb for choosing among them** (per the lecture): if classes are imbalanced and overall performance matters most → macro; if imbalanced and class-frequency-weighted performance matters → weighted; if classes are roughly balanced → micro (it converges to accuracy anyway).

**✱ Support** The number of actual occurrences of a class in the dataset — the denominator behind weighted averaging and a standard column in classification reports.

**One-vs-Rest (OvR) / One-vs-All (OvA)** A strategy for extending binary metrics (like ROC-AUC) to multi-class problems: compute the metric for each class treated as "positive" against all others treated as "negative," then average.

**Multi-Label Confusion Matrix** In multi-label settings (an instance can belong to multiple classes at once), a separate 2×2 confusion matrix is computed per label rather than one joint matrix.

---

## 7. Quick Reference — Which Metric When?

|Situation|Preferred metric(s)|
|---|---|
|Balanced classes, general performance|Accuracy, ROC-AUC|
|Severe class imbalance|PR-AUC, MCC, F1/Fβ, Balanced Accuracy|
|Cost of false negatives is high (e.g. disease)|Recall / Sensitivity, F2-score|
|Cost of false positives is high (e.g. spam)|Precision, F0.5-score|
|Need one robust summary stat under imbalance|MCC or Cohen's Kappa|
|Care about predicted-probability quality|Log-loss, Brier score, calibration curve|
|Comparing rankers independent of threshold|ROC-AUC (balanced) or PR-AUC (imbalanced)|
|Multi-class, all classes matter equally|Macro-averaged F1/precision/recall|
|Multi-class, overall volume matters|Micro-averaged F1/precision/recall|


## Common Trip-Ups & Edge Cases in Classification Metrics

**1. Confusion matrix orientation can flip.** Not every source puts actual labels on the rows and predicted labels on the columns — some flip it, putting **predicted** on the rows and **actual/ground-truth** on the columns. Always confirm which axis is which before reading off TP/FP/FN/TN; don't assume a "standard" layout just because it looks familiar.

**2. Precision/Recall word problems test translation, not recall of formulas.** The real skill is converting a plain-English description into the correct metric. Example: "This fruit-sorting system rejects 80% of good, pickable fruit — but whatever it does pick is genuinely good" → that's low recall, high precision. Practice going scenario → metric, not just metric → number. A few common mappings:

- Screening for a terminal disease (can't miss anyone) → maximize **Recall**
- Sorting apples vs. oranges (both error types equally bad) → maximize **Accuracy**
- An automated system that could harm people if wrong (e.g. flagging a target) → **zero False Positives**
- Granting access to a secure area → **low False Positives**

**3. "Micro-F1 = Accuracy" only holds under a specific condition.** This equality is true _only_ for single-label multi-class classification, where every instance gets exactly one predicted label. It silently breaks under multi-label settings or if a "reject/abstain" option exists. Know _why_ it holds (both reduce to total-correct / total-instances when every instance has exactly one label), not just that it does.

**4. F1's harmonic mean punishes imbalance harder than you'd expect.** Because Precision and Recall share the same numerator (TP) but different denominators, their harmonic mean is dragged toward whichever is smaller. Example: Precision = 1.0, Recall = 0.01 → F1 ≈ 0.02, not ≈ 0.5 (which an arithmetic mean would give). Be ready to reconstruct this reasoning, not just quote "F1 punishes extreme values."

**5. Cost-weighted formulas depend on the scenario, not a fixed rule.** `Cost = C_FP × FP + C_FN × FN` — which error dominates depends entirely on the application. In earthquake prediction, a false negative costs disaster-recovery money; a false positive costs wasted preventive measures. A question can hand you asymmetric costs and ask which classifier/threshold minimizes total cost — this requires plugging in actual FP/FN counts, not just reciting the formula.

**6. Macro-F1 and Weighted-F1 are easy to swap under time pressure.** They use the _same_ per-class F1 scores but combine them differently:

- Macro-F1: plain average across classes (every class counts equally)
- Weighted-F1: average weighted by each class's support (frequent classes count more)

These can produce meaningfully different numbers from identical inputs — e.g. three classes with per-class F1 of 0.67, 0.40, 0.67 but very different support could give a macro-F1 around 0.58 and a weighted-F1 around 0.64 from the same data. Practice computing both by hand from a small confusion matrix until it's automatic — don't just memorize the definitions.

**7. Micro-averaging pools counts before dividing — don't average the per-class ratios.** A common error is computing Micro-Precision by averaging each class's precision. The correct method sums TP, FP, FN across all classes _first_, then computes one Precision/Recall/F1 from those totals. Averaging the per-class precisions directly gives you Macro-Precision by accident, not Micro-Precision.

**8. High accuracy can hide poor recall — the trap is in what accuracy conceals, not the arithmetic.** 96% accuracy sounds strong, but if it comes from a rare-disease dataset where only 2 of 5 actual positive cases were caught, recall is just 40%. If asked to critique a reported accuracy number, the expected answer is usually "check class balance and look at precision/recall," not just recomputing accuracy.

**9. ROC curves can look great while precision quietly collapses.** The False Positive Rate's denominator (FP + TN) is dominated by the true negatives whenever the negative class is large. That means even a large _absolute_ number of false positives barely moves FPR — so ROC-AUC can stay high (e.g. 0.95+) while Precision, which reacts directly to FP relative to TP, is actually poor. This is the single most common ROC trap: "high AUC" does not mean "trustworthy positive predictions" under class imbalance. PR curves expose this because they never involve TN at all.

**10. AUC has a specific probabilistic meaning — don't reduce it to "area under a curve."** AUC (Area Under the ROC Curve) equals the probability that a randomly chosen positive example is scored higher than a randomly chosen negative example by the model. AUC = 0.5 is exactly random guessing (equivalent to the diagonal line); AUC = 1.0 is a perfect ranker; AUC < 0.5 means the model is doing worse than random — often a sign that its predictions should simply be inverted, not that it's "bad" in an unrecoverable way.

**11. The ROC diagonal is a fixed baseline; the PR baseline is not.** On a ROC plot, random guessing always traces the same (0,0)–(1,1) diagonal, regardless of the dataset. On a PR plot, a random classifier's curve is a horizontal line at the class's **prevalence** (fraction of positives) — which changes from dataset to dataset. This means a PR-AUC of, say, 0.3 could be excellent (if prevalence is 0.05) or terrible (if prevalence is 0.5). Always check what the "no-skill" baseline actually is before judging a PR-AUC number in isolation — ROC-AUC doesn't need this check, PR-AUC does.

**12. Threshold-independence doesn't mean threshold-irrelevance.** ROC-AUC and PR-AUC both summarize performance across _all_ possible thresholds at once, which is useful for comparing models — but a model with excellent AUC can still perform badly at the one threshold you actually deploy. If a question asks about behavior "at a threshold of 0.5" or similar, that's a request to read one specific point off the curve (one TPR/FPR or Precision/Recall pair), not to just quote the AUC.

**13. Know which curve to reach for, and why, not just the definitions.** ROC-AUC is the right tool when both classes matter roughly equally and you want a threshold-independent ranking measure. PR-AUC is the right tool when the positive class is rare and you specifically care about how well the model finds it without drowning in false alarms (fraud, disease screening, rare-event detection). If a question gives you a heavily imbalanced setup and asks "which curve/metric would you check first," the expected answer is PR-AUC, precisely because ROC can hide the imbalance problem (see #9).

---

**Quick self-test:** take any small 3×3 confusion matrix (e.g. three classes like urgent/normal/spam) with predicted labels on the rows and actual labels on the columns, and hand-compute Micro-Precision and Macro-Precision without notes. If you hesitate on which cells count as TP for the middle class given the flipped axes, that's the exact edge case to drill.

**Quick self-test:** given a dataset where positives make up 2% of all examples and a model reports ROC-AUC = 0.97, explain — without looking anything up — why that number alone doesn't tell you whether the model's positive predictions are actually reliable, and what you'd check instead. If the answer isn't immediate, revisit point 9.