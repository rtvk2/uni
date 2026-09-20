# SMAI Assignment 1 — Viva Notes (every cell, plain language)

Seed is 1703431610, from sha256 of sairitvik.uppuganti mod 2^32. It is the only random seed used everywhere, so every split and every sampler is reproducible.
Format: hybrid explainer plus Q and A per cell, as agreed. Markdown, not TeX, because TeX would be far longer with no viva benefit. No big code blocks below — everything is explained in words, math, and tiny inline snippets like lcg_uniform_generator.

Cell map, so you can verify nothing is skipped: 00 title, 01 checklist, 02 seed, 03-04 imports, 05-06 Q1 headers, 07 essays.csv head, 08 score counts, 09 kept-feature essay, 10 EssayFeatureExtractor, 11 dropped-feature note, 12 commented DroppedFeatureExtractor, 13-14 section 1.2 headers, 15 features2dictionary plus correlation plus CSV save, 16 low-correlation one-liner, 17 X.head, 18 y equals score, 19 chained train_test_split, 20-22 normalisation header plus ZScoreStd plus fit-transform, 23-24 visualisation header plus 5-plot block, 25-26 Q1.3 headers, 27 DistanceMetrics, 28 Evaluator, 29 KNN, 30 smoke test k equals 21, 31-33 tuning header plus HyperparameterTuner plus grid launch, 34 tuning curves, 35-43 Q1.4 header plus four metric tables, 44-45 best config plus test eval, 46-48 Q2 headers, 49 lcg_uniform_generator, 50 LCG demo, 51-52 Gaussian header plus inverse_cdf_gaussian plus gaussian_generator, 53-54 Binomial header plus three functions, 55-57 image header plus Image.open plus row_marginal plus col_conditional plus image_generator, 58 lone matplotlib import, 59 paw scatter, 60 empty, 61-63 validation header plus empirical_cdf plus max_cdf_distance plus three checks, 64-66 Q2.2 headers plus three MLEs, 67-68 fitting header plus compute_training_params, 69-70 MAP header plus log_posterior_scores plus predict_class, 71-73 metrics header plus confusion_matrix plus precision_recall_f1 plus breed pivot plus synthetic dogs plus split plus fit plus eval, 74 empty, 75-77 spam headers plus emails.csv head, 78 fit_spam_counts, 79 spam split plus fit, 80-82 smoothed-classifier header plus stable_sigmoid plus naive_bayes_spam_prob, 83 row_to_text plus test loop plus scores, 84 pretty confusion matrix, 85-88 Q2.4 headers plus beta_binomial_posterior, 89-90 point-estimate header plus posterior_point_estimates, 91-92 interval header plus credible_interval, 93 bark synthesis plus three priors plus printout, 94 empty end marker.

---

# PART 1 — Essay scoring with KNN

## 1.0 The dataset, honestly (cells 07, 08)

The file essays.csv has three columns: essay_id, full_text, score. Score is an integer 1 to 6 given by a human.

Counts in your notebook: score 1 has 1252 essays, score 2 has 4723, score 3 has 6280, score 4 has 3926, score 5 has 970, score 6 has 156. Total 17307. Score 0 has 0 rows, the loop just prints it.

Plain meaning: the data is skewed. It looks like a hill with a fat middle. Scores 2, 3, 4 together are about 88 percent of everything. Score 6 is only 156 essays, less than 1 percent. Score 5 is also thin.

Why this matters for viva: any model will see mostly middle-score essays during training, so it naturally learns to guess near the middle. That makes overall error look decent while the rare 6s get systematically under-predicted. That is why you report four metrics, not just one: MAE alone would hide the tail problem, while R-squared and Pearson expose whether the model actually tracks quality up and down.

Column note: the assignment PDF says the column is called essay, your CSV calls it full_text. Same thing. Your code uses df[full_text] and df[score].

## 1.1 Feature engineering (cells 09, 10, 11, 12, 15, 16)

Rule of the game: no NLP libraries, no pretrained models, no re module, no string module. Only plain str methods like split, strip, lower, count, replace. So every feature must be hand-built statistics.

Shared cleaning, in words: for words, split the essay on whitespace, strip punctuation characters off both ends of each token (including curly quotes and dashes), lowercase it, throw away empties. For sentences, first turn every ? and ! into a period, then split on periods, then the same strip plus lowercase plus drop-empties. This sentence splitter is crude — abbreviations like Mr. get split — but the error hits all essays roughly equally, so relative comparisons still work.

There are 13 kept features. For each: what it is, exact formula, how your method computes it, why a good essay moves it, and its correlation with score.

Length and fluency group, method length_fluency:

- n_words, corr about +0.65, the strongest positive. Formula: number of cleaned words. Code: len(words), guarded max(1, ...) so empty essays do not divide by zero. Intuition: longer essays develop the argument more. A 50-word essay cannot score like a 400-word one.
- n_sents, corr about +0.59. Formula: number of cleaned sentences. Intuition: more sentences means more organised development of ideas.
- avg_word_len, corr about +0.21. Formula: sum of characters over all words divided by n_words. Intuition: longer words tend to be more precise or academic. The related avg_words_per_sent was dropped at corr about -0.065, pure noise.

Vocabulary richness group, method vocab_richness. First build a frequency dictionary mapping each distinct word to its count, then:

- root_ttr (root type-token ratio), corr about +0.40. Formula: number of distinct words divided by sqrt(n_words). Why the square root: raw TTR, distinct divided by n, mechanically falls as essays get longer, because you reuse common words. Dividing by sqrt(n) instead roughly flattens that length effect, so it measures diversity fairly across short and long essays. High means varied vocabulary.
- hapax_ratio, corr about -0.44. Formula: number of words occurring exactly once (hapax legomena) divided by n_words. The sign surprises people: better essays have LOWER hapax ratio. Reason: a focused, well-argued essay repeats its topic words; a scattered weak essay sprays rare words once each and never develops them. All three vocab features pairwise correlate below 0.4, so they carry independent signals.
- entropy (Shannon entropy), corr about +0.51. Formula: over the word distribution with p equal count divided by n, entropy equals negative sum of p times log2(p). Intuition: entropy is high when word use is even and rich, low when one word dominates. Example: an essay repeating the 50 times has low entropy; one using varied vocabulary evenly has high entropy. Units are bits because log base 2.

Punctuation group, method punctuation_numeric, computed on the raw essay string, not the cleaned words:

- comma_ratio, corr about +0.24. Formula: essay.count(comma) divided by n_words times 100, i.e. commas per 100 words. Intuition: commas mark clauses and lists, hence complex sentences.
- punctuation_diversity, corr about +0.26. Formula: number of distinct marks seen from the set period comma semicolon colon exclamation question, divided by 5. Intuition: using varied punctuation means varied sentence types. Dropped siblings like semicolon ratio or question-mark ratio each correlated below 0.11, so they were cut.

Lexical sophistication group, method lexical_sophistication:

- char_bigram_diversity, corr about -0.65, largest magnitude. Formula: collect every adjacent character pair inside every word (for cat you get ca and at), count distinct pairs, divide by n_words. Intuition: more distinct roots and affixes means richer morphology. Why negative: as essays grow, the inventory of possible bigrams saturates while the denominator n keeps growing, so long good essays get smaller values. The sign is a normalisation artefact, but the ranking power is real, so keep it.
- long_word_ratio, corr about +0.19. Formula: fraction of words with length 7 or more. Intuition: formal and academic diction. Dropped cousins yules_k (about -0.03) and word_len_skewness (about -0.05) were noise.

Readability group, method readability_indices:

- coleman_liau, corr about +0.22. Formula: 0.0588 times L minus 0.296 times S minus 15.8, where L equals mean letters per word times 100 and S equals sentences per word times 100. It estimates the US grade level needed to read the text. Higher means more complex writing. Why this index and not Flesch-Kincaid: Coleman-Liau needs only letters, words, sentences. Flesch needs syllables per word, which under the str-only ban must be guessed by a vowel-group heuristic (your _count_syllables: lowercase, strip trailing e, count groups of aeiouy, minimum 1). That guess is noisy, and Flesch correlated -0.015, pure noise, so it was dropped. ARI at -0.019 was redundant with Coleman-Liau.

Discourse group:

- first_person_ratio, from method first_person_ratio. Formula: count of words in the set i, me, my, mine, myself, we, our, us divided by n_words. Finding: clean monotone fall from about 0.0195 at score 1 to about 0.0079 at score 6. Intuition: weak essays narrate personally (I think...), strong argumentative essays frame analytically in third person. Near-orthogonal to all other features, so free extra signal.
- opener_diversity, from method sentence_opener_diversity. Formula: take the first word of each cleaned sentence, count distinct openers, divide by number of sentences. Corr about -0.23. Intuition: repeated openers like The, This, I signal monotonous structure. Higher is not always better here in raw correlation because long essays naturally reuse openers, but jointly with other features it helps.

Selection rule (cell 16 one-liner plus cell 11): drop a feature if absolute correlation below about 0.1 (noise), or if it duplicates another feature (pairwise correlation above about 0.85), or if adding it raises KNN validation MAE (extra dimension adds distance noise). Every drop was validated by retraining KNN with and without the group and keeping the 13 that gave lower validation MAE.

Dropped list with reasons, mapping to the commented cell 12: compression_ratio (1 minus compressed bytes over raw bytes via zlib, redundant with n_words because longer essays compress similarly), ngram_rep_rate (trigram repeat fraction, corr 0.12), sentence_length_cv (std over mean of sentence lengths, -0.096), flesch_kincaid_grade (-0.015), sentence_length_std (-0.055), prop_long_sentences (fraction of sentences over 15 words, 0.098), n_paragraphs (count of double-newline plus 1, always 1 because the CSV has no paragraph breaks, correlation NaN), avg_sents_per_para (equals n_sents when paragraphs equal 1, pure duplicate), adv_punct_ratio (semicolons plus colons per sentence, 0.069), spacing_error_ratio (missing-space proxy, monotone but so tiny it only added KNN noise), yules_k and word_len_skewness (near zero). The helper _count_syllables survives as dead code used only by the dropped Flesch path.

Cell 15 mechanics: features2dictionary applies extract_features row by row and returns a DataFrame with one row per essay and 13 columns. feature_score_correlation recomputes each column's pandas Pearson correlation with score, sorts by absolute value, prints the table, and saves feature_correlations1.csv. That CSV is your evidence for keep versus drop.

Q: why is everything numeric. A: KNN needs distances, so strings or sets cannot go in directly. Every feature above is a float or int.

## 1.2.1 Train, validation, test split (cells 14, 17, 18, 19)

Cell 17 shows X.head, the 13-column feature frame. Cell 18 sets y to the score column.

Cell 19 does the split in two chained calls with the same seed: first 80 percent train versus 20 percent temp, then split temp half-half. Net ratio 80 train, 10 validation, 10 test. On 17307 essays that is about 13845 train, 1731 validation, 1731 test. Same seed twice is fine because the inputs differ; it only guarantees reproducibility, not identical partitions.

Why three splits, in plain words: train fits the model, validation picks K and distance (you are allowed to look many times), test is touched exactly once at the end for an honest number. If you picked K on test, you would optimistically overfit to test. No stratify was used; for regression labels that is acceptable, but remember score 6 contributes only about 16 test essays, so test metrics are noisy on the tails.

## 1.2.2 Normalisation (cells 20, 21, 22)

Why needed: KNN decides by distance. n_words ranges over hundreds while comma_ratio ranges over single digits. Without scaling, n_words alone decides every neighbourhood and the other 12 features might as well not exist. Z-score puts every feature on unit-variance footing so each gets a vote.

Formula per feature: z equals (x minus mu) divided by sigma, with mu the training mean and sigma the training standard deviation, computed column-wise (axis equals 0).

Class mechanics: ZScoreStd has fit (compute and store mean and std from the given frame), transform (apply stored values to any frame), fit_transform (fit then transform, train only). Cell 22 calls fit_transform on X_train and transform on X_val and X_test. transform before fit raises ValueError.

Why fit on train only: at deployment time you do not know the future distribution. Estimating mu and sigma from validation or test leaks their distribution into training and gives optimistic error. The leak is small on 17k rows but it is still leakage, and examiners ask exactly this. Edge case: a constant feature has sigma 0 and would give NaN or inf; none of the 13 is constant, but production code would clamp sigma to a tiny epsilon, exactly like the 1e-9 guard in gaussian_mle.

## 1.2.3 Visualisation (cells 23, 24)

Requirement: at least 4 meaningful plots, each with title, x and y labels, legend where applicable, username stamped. You have 5. What each shows and why it was chosen:

- Correlation bar chart: one horizontal bar per feature, Pearson correlation with score, red for negative, blue for positive, zero line drawn, numeric labels. Purpose: rank predictive power at a glance and justify keep versus drop. Computed with corrwith on the unnormalised frame.
- Boxplots by score for n_words, entropy, hapax_ratio, char_bigram_diversity: per score 1 to 6, box is the interquartile range (25th to 75th percentile), line inside is the median, whiskers extend about 1.5 IQR, dots are outliers. Colours encode score. Purpose: show monotone separation of medians (n_words rises, hapax falls) plus heavy overlap, which proves no single threshold suffices and a multivariate model like KNN is needed.
- KDE curves for n_words and entropy, one curve per score: gaussian_kde with Scott bandwidth smooths each score group's histogram into a density over a 300-point grid. Purpose: show the full conditional distribution p(feature given score); score-6 mass sits right for n_words, score-1 sits left. gaussian_kde from scipy.stats is allowed here because it is plotting only, never inside an implemented sampler or metric.
- Scatter n_words versus entropy coloured by score: each essay one dot, alpha 0.35 so dense regions darken. Purpose: show the two strongest positive signals are complementary, not collinear; high-score cloud sits up-right.
- Mean feature profile: min-max normalise 6 top features to 0-1 across the whole dataset, group by score, plot mean trajectory with one line per score. Purpose: reveal the joint signature — score 6 is high everywhere except hapax_ratio and first_person_ratio where it is low. Min-max here is only for display, not for modelling, so no leakage concern.

## 1.3.1 KNN implementation (cells 25-30)

Idea in one sentence: to score a new essay, find the k training essays closest in the 13-dim standardised space and average their human scores.

DistanceMetrics, all static methods plus a dist dispatcher classmethod that lowercases the name and raises ValueError on unknown:

- Euclidean: sqrt of sum of squared differences. Spherical neighbourhoods. Squares large gaps, so one huge feature gap dominates.
- Manhattan: sum of absolute differences. Diamond neighbourhoods. More robust to a single spiky feature.
- Cosine distance: 1 minus cosine similarity, i.e. 1 minus dot(a,b) over norms. Angle only, blind to vector length. Since essay quality lives partly in length (n_words magnitude), cosine throws away signal, which is why it trails by about 0.01 to 0.02 on every metric.

KNN class: init stores k, distance name, resolves the function. fit memorises training arrays as numpy (lazy learner, no real training) plus a global_mean that is stored but never used — say so honestly if asked. predict loops test rows one by one, computes distances to all train rows, argsorts, takes the first k indices, averages their labels, appends. Mean, not majority vote, because scores are treated as numbers (regression-style KNN). No rounding: fractional predictions are fine for MAE, RMSE, R-squared, Pearson.

Evaluator, all from scratch with numpy: MAE is mean of absolute errors (robust, same units as score). RMSE is sqrt of mean squared error (one 4-point miss counts like sixteen 1-point misses, so RMSE minus MAE measures outlier severity; RMSE is always at least MAE). R-squared is 1 minus SS_res over SS_tot, the fraction of variance explained; 0.55 means about 55 percent; it can be negative when worse than predicting the mean; code returns 0.0 when SS_tot is 0 (all true scores identical, undefined otherwise). Pearson r is covariance over the product of standard deviations; it is invariant to scale and shift (predicting twice score plus 1 still gives r 1 with terrible MAE), hence you need both families. Code returns 0.0 when the denominator is 0 (constant predictions). metrics returns all four in a dict.

Cell 30 smoke test runs k 21 euclidean on validation once before the grid, to confirm the pipeline runs.

## 1.3.2 Tuning and 1.4 evaluation (cells 31-45)

Grid: k values 1, 3, 5, 7, 9, 11, 13, 15, 17, 19, 21, 25, 31 (a superset of the brief example) times 3 distances, 39 configs, evaluated on validation only. HyperparameterTuner stores one dict per config with MAE, RMSE, R2, Pearson, appends to results, writes performance_log.txt, prints progress. get_metric filters by metric, optionally by k or distance.

Curves (cell 34): four subplots of metric versus K, one line per distance. Higher-is-better for R2 and Pearson, lower for MAE and RMSE.

Trend to narrate: steep improvement from k 1 to about k 9 (averaging kills variance), then flattening; best at the grid max k 31. Small k overfits — k 1 MAE about 0.64 to 0.68 depending on distance. Large k smooths noise in the overlapping feature space. Honest caveat: since the best is at the grid edge, a larger k might help slightly, but eventually predictions collapse to the global mean (underfit), so 31 is best-in-grid, not proven-globally-optimal.

Best validation, all four metrics agreeing: manhattan k 31, MAE about 0.5191, RMSE about 0.6794, R2 about 0.5834, Pearson about 0.7645. Euclidean k 31 is within noise; cosine trails. Per the brief there is no single best metric, so the choice is the config that jointly dominates all four.

Cells 35-43 print per-metric tables via get_metric so the examiner sees every config. Cells 44-45 retrain once on train with manhattan 31 and evaluate once on test: MAE about 0.5594, RMSE about 0.7253, R2 about 0.5529, Pearson about 0.7464. Small validation-to-test gap means no leak and no over-tuning to validation.

---

# PART 2 — Probability and Bayesian learning

## 2.1.1 Uniform generator, the LCG (cells 47-50)

You are banned from np.random and random, so cell 49 builds randomness from integer arithmetic.

Recurrence (MINSTD Park-Miller): state_{next} equals (48271 times state) mod m, with m equals 2^31 minus 1 (a Mersenne prime). Period is m minus 1, about 2.1 billion, so 20k draws never cycle.

Steps in code: state starts as seed mod m, but if that is 0 set to 1, because 0 is absorbing (a times 0 mod m stays 0 forever, you would emit zeros forever). Then repeat num_samples times: update state, emit state divided by m, which lies strictly in (0,1), never exactly 0 or 1. Finally affine-map to the requested interval: low plus u times (high minus low), returning a numpy array.

Why exclusivity matters: downstream gaussian code applies erfinv, where erfinv of exactly plus or minus 1 is infinite, and log(0) is undefined. LCG never hitting exactly 0 or 1 quietly protects both.

Cell 50 demo draws 100 values in [1,100) with your seed and prints them. Allowed tools: plain Python plus numpy only.

## 2.1.2 Gaussian sampler (cell 51, 52)

Background: a Gaussian with mean mu and std sigma has CDF F(x) equals one-half times (1 plus erf((x minus mu) over (sigma times sqrt(2)))). Inverse-transform sampling says: if U is uniform on (0,1), then F-inverse(U) is Gaussian-distributed. So invert the CDF algebraically: from y equals one-half(1 plus erf(...)) you get 2y minus 1 equals erf(...), then (x minus mu) over (sigma sqrt2) equals erfinv(2y minus 1), so x equals mu plus sigma times sqrt(2) times erfinv(2y minus 1). That is exactly the one line in inverse_cdf_gaussian, using scipy.special.erfinv, which is an allowed special function (a scalar math function, not a sampler).

gaussian_generator then composes: draw num_samples uniforms in (0,1) from your LCG, push each through inverse_cdf_gaussian. No scipy.stats, no np.random anywhere inside.

## 2.1.3 Binomial sampler (cells 53, 54)

PMF: P(X equals k) equals C(n,k) p^k (1-p)^(n-k), implemented as binomial(n, p, k) with scipy.special.comb, exact and vectorised so k can be an array like arange(n+1). Note the repo argument order is (n, p, k).

Discrete inverse CDF (quantile): Q(y) equals smallest k in 0..n with CDF(k) at least y. binomial_quantile builds the PMF array over 0..n, cumsums it into the CDF staircase, and returns searchsorted(CDF, y), numpy's binary search for the insertion point, which is exactly that minimum. binomial_generator draws LCG uniforms in (0,1) and maps each through the quantile. Same single idea as Gaussian, discrete version.

## 2.1.4 Image sampler (cells 55-59)

Idea: a grayscale image is a 2D empirical density. Divide every pixel by the image total and you get a joint PMF P(row, col) summing to 1. Chain rule: P(row, col) equals P(row) times P(col given row). So sample hierarchically: pick a row from its marginal, then a column from that row's conditional. Each step reuses the discrete cumsum plus searchsorted machine from Q2.1.3.

Functions: row_marginal returns row sums over total, shape (H,). col_conditional(image, row) returns that row's pixels over that row's sum, shape (W,). image_generator precomputes the row CDF, draws two independent uniform streams u_rows from LCG(seed) and u_cols from LCG(seed plus 999), maps rows via searchsorted(row CDF, u_rows), then per draw builds that row's column CDF and maps the column via searchsorted. Returns (row_indices, col_indices).

Why two streams with offset 999: reusing the same uniforms for rows and columns would couple them (same random number drives both choices). Offset decorrelates while staying reproducible.

Run: cell 56 loads paw_print.png and cat_face.png via PIL into arrays (cat_face is loaded but the evaluated run uses the paw). Cell 58 is just the matplotlib import. Cell 59 runs 5000 draws on the paw (100 by 100), scatter-plots col on x and row on y with tiny dots alpha 0.5, inverts the y axis because image row 0 is the top, titles with username. The cloud reconstructs the paw because dense dark pixels hold more mass and get hit more often.

Tiny example to say aloud: a 2 by 2 image [[3,1],[0,2]] totals 6, row marginal [4/6, 2/6], row CDF [0.667, 1]. Draw u 0.5 gives row 0; that row conditional [0.75, 0.25], second u 0.8 gives column 1. That is the whole algorithm.

Edge: an all-black row would make col_conditional 0 over 0 (NaN). The paw has no such row; production code would guard.

## 2.1.5 Validation (cells 61, 62, 63)

Definitions: empirical_cdf(sample, x) sorts the sample once, then for each eval point counts entries at or below it via searchsorted with side right (right means ties count, matching at-or-below) divided by n. max_cdf_distance computes that empirical vector and the theoretical CDF vector on the same eval grid and returns max absolute gap. That max gap is the Kolmogorov-Smirnov statistic (here used as a number, not a formal test).

Checks with 20000 draws each: Uniform[1,100] against (x minus low) over (high minus low) clipped to [0,1]; Gaussian(0,1) against one-half(1 plus erf((x minus mu) over sigma sqrt2)) with a 50-point linspace over mu plus/minus 4 sigma (tails beyond hold under 1e-4 mass, negligible); Binomial(30, 0.3) against the cumsum-PMF staircase evaluated at 0..n. Results about D 0.0041 uniform, 0.0043 gaussian, 0.0033 binomial. Expected sup error scales like 1 over sqrt(n), about 0.007 at n 20000, so 0.004 means the generators match theory.

## 2.2 Naive Bayes with mixed features (cells 64-73)

Setup truth first, because examiners probe it: breed_traits_long.csv is one row per breed (aggregated 1-5 trait scores, 195 breeds), not one row per dog. Height and weight columns are ignored per the corrigendum. To get a per-dog problem you synthesise 50 dogs per breed, 9750 total: per trait, draw gaussian_generator with mean equal the breed mean and sigma 0.5, seed arithmetic seed plus i times 1000 plus feature index for independent reproducible streams, clip to [1,5]. The stray np.random.seed line in the cell is vestigial data-prep; every evaluated draw uses your LCG generator, so say you would delete that line. 14 numeric traits listed in the cell, all modelled gaussian via feature_distributions mapping every trait to gaussian. Split 80/10/10 gives train 7800, val 975, test 975. Validation accuracy about 0.799, test about 0.8031, macro precision/recall/F1 about 0.82, 0.79, 0.80. Random baseline at 195 classes is 1/195, about 0.005, so 0.80 is strong. Confusion matrix is 195 by 195, near-diagonal; the notebook prints the top-left 5 by 5.

### 2.2.1 MLEs (cells 65, 66)

Maximum likelihood means: pick the parameters that maximise the probability (likelihood) of the data you actually saw. Take log-likelihood, differentiate, set to zero.

- Gaussian: log-likelihood in mu and sigma gives mu_hat equals sample mean, sigma_hat equals sqrt of mean squared deviation with divide by N, not N minus 1. N minus 1 (Bessel) is the unbiased variance; N is the MLE. Code uses np.std default (divide by N, correct) and clamps sigma to at least 1e-9 so later log(sigma) never sees log(0). Returns plain floats per spec.
- Binomial with known n: p_hat equals sample mean over n. Intuition: fraction of successes.
- Uniform: likelihood is (b minus a)^(-N) if all points lie inside [a,b], else 0. Tightest surviving interval wins, so a_hat equals min(x), b_hat equals max(x). Endpoints, not moments.

### 2.2.2 Fitting (cells 67, 68)

compute_training_params(df, features, feature_distributions) is generic: classes from sorted unique breeds (sorted for determinism), log-priors as log(count over total) computed once in log space, then per class slice the frame and per feature dispatch on the mapping string to the matching MLE above, storing (name, params) tuples in params[cls][feat]. The binomial branch hardcodes n equals 5 (trait scale 1-5 with rounding); it is dead code in your run since everything is gaussian, but the pattern is correct for discrete 1-5 traits. Returns (log_priors, params). Fit on train only.

Naive part, plainly: given the breed, features are assumed conditionally independent, so the joint likelihood factorises into a product. This is false (traits correlate) but works because classification needs only the right argmax, not calibrated probabilities; correlated evidence double-counts yet usually preserves the winner.

### 2.2.3 MAP prediction (cells 69, 70)

MAP means maximum a posteriori: pick the class with highest P(class given x), proportional to P(class) times product of P(x_k given class). In log space (products become sums, no underflow over 14 features times 195 classes): score_i(x) equals log prior plus sum of log likelihoods, predict the argmax.

Per-family log densities: gaussian negative one-half log(2 pi) minus log sigma minus one-half ((x minus mu) over sigma)^2. Binomial via lgamma for the combination, logC equals lgamma(n+1) minus lgamma(k+1) minus lgamma(n-k+1) (exact, no factorial overflow), plus k log p plus (n-k) log(1-p), with k equals round(val) and out-of-range k giving negative infinity. Uniform gives negative log(b minus a) inside [a,b], negative infinity outside (zero-density veto — the classic NB fragility; your clipped gaussian data stays in range so it never fires). predict_class returns max(scores, key scores.get). MLE from 2.2.1 is MAP with a flat prior; here priors are the log class frequencies.

### 2.2.4 Metrics and eval loop (cells 71, 73)

confusion_matrix(y_true, y_pred, labels): map label to index, zero an n-by-n int array, increment [true, pred] per pair, skip unknown labels. Rows are truth, columns are predictions, diagonal is correct.

precision_recall_f1(cm): per class, TP equals diagonal, FP equals column sum minus TP, FN equals row sum minus TP, precision TP over TP plus FP, recall TP over TP plus FN (0.0 when denominator 0), then macro-average (mean over classes, rare breeds count equally; micro would weight dogs equally). Final F1 equals 2 times macroP times macroR over their sum, i.e. F1 of the macro averages, not the mean of per-class F1s — state this if asked. No sklearn.

evaluate_nb in cell 73 loops each row to a dict sample, calls predict_class, then builds cm, metrics, and plain accuracy (fraction equal). Reports val and test numbers above.

## 2.3 Spam detection (cells 75-84)

### 2.3.1 Counts (cells 76-79)

emails.csv is a bag-of-words matrix: one row per email, one column per word holding counts, plus Email No. id and Prediction 0/1. Cell 77 head shows this. Split 80/20 gives train 4137, test 1035.

fit_spam_counts on train only (test untouched until evaluation): class_freq counts emails per class, giving spam 1215, ham 2922, i.e. ham-heavy about 71 percent (majority baseline 0.71, which later explains high accuracy coexisting with lower precision). word_freq maps each word to spam and ham document counts: number of train emails of that class containing the word at least once (row[word] greater than 0). This is document frequency, not token count, matching the Bernoulli presence/absence event model where P(word given class) is the fraction of that class's emails containing it.

### 2.3.2 Classifier (cells 80-84)

stable_sigmoid(t): two branches, 1 over (1 plus e^(-t)) for t non-negative, e^t over (1 plus e^t) for t negative. Never exponentiates a positive number. Why: naive form at t equals -1000 computes e^1000 which overflows to inf or raises; the branch keeps every exp argument at most 0. Same math, no overflow.

naive_bayes_spam_prob(text, word_freq, class_freq, alpha 1.0): lowercase, split on whitespace, deduplicate via set (presence, consistent with doc counts). Start both scores from log priors log(N_spam over N) and log(N_ham over N). For each word in vocab (skip out-of-vocabulary, the standard open-vocabulary policy: unseen words carry no estimate), add Laplace-smoothed log likelihood log((count_c plus alpha) over (N_c plus 2 alpha)). Denominator is plus 2 alpha because the outcome is binary (present versus absent), not plus vocab-size (that would be the multinomial version). Return sigmoid(score_spam minus score_ham), because sigmoid of the log-odds equals P(spam given text). Derivation in words: log posterior odds equals log prior ratio plus summed log likelihood ratios; inverting the log-odds gives the sigmoid.

Laplace intuition: alpha equals 1 adds one pseudocount per class per word (a Beta(1,1) prior). Without it, a word never seen with spam gives P equals 0, log 0 equals negative infinity, one word vetoes the class. Alpha 0 is brittle MLE; alpha to infinity washes out signal to uniform.

Evaluation (cell 83): row_to_text joins the present words of each test row back into a string (bridging matrix storage to the text interface), predicts probability, thresholds at 0.5 (MAP under equal costs), tallies TP TN FP FN, computes accuracy (TP plus TN over n), precision (TP over predicted spam), recall (TP over actual spam), F1 harmonic mean. Results: accuracy about 0.8841, precision about 0.7078, recall about 0.9860, F1 about 0.8240, with TP 281, TN 634, FP 116, FN 4. Reading: catches nearly every spam (misses 4 of 285) at the cost of 116 ham flagged as spam, expected with overlapping vocab and ham majority; F1 balances the two. Cell 84 reprints the same as a labelled confusion table.

## 2.4 Beta-Binomial bark-day rate (cells 85-93)

This is genuinely Bayesian, unlike the Naive Bayes classifier name: instead of a point estimate, you keep a distribution over the unknown rate p itself.

Model: each dog's bark_days k_i is Binomial(n equals 30, p). Prior on p is Beta(alpha_0, beta_0). Conjugacy (one line to memorise): Beta prior times Binomial likelihood is proportional to p^(sum k plus alpha_0 minus 1) times (1-p)^(sum (n-k) plus beta_0 minus 1), i.e. Beta(alpha_0 plus sum k, beta_0 plus sum (n-k)). So the update is two additions.

Data synthesis (cell 93): per-dog p from the Barking Level 1-5 score via p equals (barking minus 0.5) over 5, giving 0.1 to 0.9 in steps (centred, never degenerate 0 or 1 where Binomial would be deterministic). Each k_i drawn from your binomial_generator with n 30, p_i, one sample, seed plus i (independent reproducible streams). N equals 7800 train dogs.

Functions: (a) beta_binomial_posterior applies the two-line update, returns (alpha_post, beta_post). (b) posterior_point_estimates returns mean alpha over (alpha plus beta), mode (alpha minus 1) over (alpha plus beta minus 2) when both exceed 1, 1.0 when only alpha exceeds 1 (mass piles at 1), 0.0 when only beta exceeds 1, NaN when neither (e.g. uniform Beta(1,1) has no unique interior mode — NaN is correct, not a bug), variance alpha beta over ((alpha plus beta)^2 (alpha plus beta plus 1)). (c) credible_interval returns the central interval via scipy.special.betaincinv (Beta quantile, allowed special function): lower at tail (1 minus level) over 2, upper at 1 minus tail.

Numbers: MLE sum k over (N times n) equals about 0.5786 from 7800 dogs. All three priors (Uniform Beta(1,1), weakly informative Beta(2,5), strong Beta(10,30)) give posterior mean about 0.5786 within 1e-4, variance about 1e-6, 95 percent credible interval about [0.5766, 0.5806]. Lesson: posterior mean equals (sum k plus alpha_0) over (N n plus alpha_0 plus beta_0), a weighted average of MLE and prior mean with weights data-trials versus pseudo-trials. Here 234000 real trials swamp at most 40 pseudo-trials (prior weight under 0.02 percent), even the strong prior vanishes. With 5 dogs (150 trials) Beta(10,30) would pull about 21 percent of the way toward 0.25. Credible means 95 percent posterior belief p lies inside; confidence would mean long-run procedure coverage — different philosophy, similar numbers here only because n is huge.

---

# Appendix — libraries and traps, plainly

Every non-trivial library call: hashlib sha256 plus hexdigest for the seed; pandas read_csv, head, corr, corrwith, pivot (long-to-wide breed table), value_counts, groupby, iterrows, iloc; numpy mean, std, sum, cumsum, searchsorted (the quantile and CDF workhorse), sqrt, log, dot, norm, argsort, sort, clip, linspace, arange, zeros; sklearn train_test_split shuffled slicing, the only allowed sklearn use; math log, exp, lgamma, log2 (bits for entropy); scipy.special erfinv and erf (Gaussian pair), comb (Binomial PMF), betaincinv (Beta quantile); scipy.stats.gaussian_kde plotting only in cell 24, never inside an implemented function; PIL Image.open to arrays; matplotlib subplots, scatter, boxplot, plot, barh, axvline, text, invert_yaxis, tight_layout, show; zlib.compress only inside commented cell 12.

Rapid-fire answers: mean not vote because scores are numeric and mean matches squared-error geometry; R2 penalises bias and scale while Pearson ignores them, hence both; k 1 overfits (single neighbour noise), k 31 smooths variance, beyond lies the global mean; Manhattan beats Euclidean on spiky features, cosine loses because it discards length where the signal lives; fitting the scaler on train plus val is leakage; bigram sign is a normalisation artefact; LCG zero is absorbing; one uniform stream reused for rows and columns would couple them, hence seed plus 999; searchsorted left gives the CDF quantile, right gives at-or-below CDF; all-black rows would 0 over 0; NB works despite violated independence because argmax needs ranking, not calibration; spam denominator is plus 2 alpha (binary), not plus vocab size; alpha 0 gives log-zero vetoes; sigmoid branch avoids e^1000 overflow; prior vanishes at N 7800 by the pseudo-trial arithmetic above; Beta(1,1) mode NaN is correct.
