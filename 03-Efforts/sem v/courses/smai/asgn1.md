**Q1: Automated Essay Scoring System (8–12 hours)**

- **Feature Engineering (3–4 hrs)**: Writing the regex/splitting logic for text stats _without_ NLP libraries, and brainstorming/designing _additional_ meaningful statistical features (e.g., avg word length, punctuation counts, vocabulary richness).
    
- **Preprocessing & Viz (2–3 hrs)**: Splitting, writing the Z-score scaler by hand, and generating the 4 required plots (correlation heatmaps, boxplots, etc.) with proper labels and usernames.
    
- **KNN Implementation & Tuning (3–5 hrs)**: Implementing Euclidean, Manhattan, and Cosine distances from scratch; writing the loop for hyperparameter tuning; building the validation table. _Time-sink alert:_ Debugging distance calculations and handling edge cases in KNN predictions.

## Length & fluency

**1. Word/sentence/paragraph counts, words-per-sentence** The original AES system (Ellis Page's PEG, 1960s) already leaned on this: the system used a standard multiple regression on proxy variables such as document length, grammar, or punctuation to predict human-rated scores, reaching accuracy comparable to human raters. More recent corpus work confirms raw length is still one of the single strongest predictors: across multiple grading programs, most features show correlations around 0.30 with human scores, but essay length alone correlates almost 0.8.

- _Page, E.B._ — Project Essay Grade (PEG), 1960s (via arxiv.org/pdf/2110.06874)
- _Automatic essay scoring system_ — image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/8472861

## Vocabulary richness

**2. Type-Token Ratio (TTR)** TTR (Johnson, 1944) is the simplest and most widely used lexical diversity index — the number of unique words divided by the number of running words. It's directly tied to writing quality: L2 writing studies have found a positive relationship between lexical diversity and writing proficiency, with essays scored higher by human raters showing greater lexical diversity.

- _Johnson, 1944_ (via sciencedirect.com/science/article/abs/pii/S1075293520300660)
- Crossley et al. 2014, Engber 1995 (via sciencedirect.com/science/article/pii/S1075293521000295)

**3. Length-corrected TTR variants (MATTR, Root TTR)** Raw TTR shrinks as essays get longer, which is exactly the length-confound problem I flagged earlier — and it's a documented issue, not just a hunch: TTR is highly affected by text length. Two fixes that are still just arithmetic (no libraries needed):

- **Root TTR**: calculates the ratio of unique words to the square root of total words, providing a stable estimate of lexical diversity across essays of varying length (Guiraud, 1954).
    
- **MATTR**: slides a fixed-size window across the token sequence and averages the within-window TTR, correcting for length bias.
    
- _Guiraud, 1954_ (via arxiv.org/pdf/2606.00250)
    
- _Covington & McFall_ MATTR (via arxiv.org/pdf/2605.21540)
    

**4. Word-frequency / rare-word usage** This one legitimizes the "long words as sophistication proxy" idea: studies found students with higher academic language proficiency tend to use less frequent words — length in characters is a crude but license-free stand-in for a frequency lookup you're not allowed to use.

- Biber & Gray 2013, Laufer & Nation 1995 (via sciencedirect.com/science/article/pii/S1075293521000295)

## Compression & entropy (structural, no wordlist needed)

**5. Compression ratio as a redundancy/complexity proxy** This is well-established outside AES specifically, in text-complexity and redundancy-measurement literature: compression ratio is a long-established proxy for statistical redundancy — a stream that compresses to a small fraction of its size is, by construction, highly predictable from its own contents. A standard definition you can implement directly with stdlib `zlib`: $CR = 1 - \frac{#\text{compressed bytes}}{#\text{original bytes}}$ where a compression ratio of 0.8 means the original text was reduced by 80% in its compressed form.

- _Semantic Compression With Large Language Models_ — arxiv.org/pdf/2304.12512
- _Content redundancy for provenance classification_ — arxiv.org/pdf/2606.29605

**6. Shannon entropy of character/word distributions** Grounded in the same information-theoretic tradition, applied directly to text: word Shannon entropy measures how uniformly vocabulary is distributed across unique word types, and character trigram entropy captures sub-word repetition patterns. Low lexical diversity and reduced distributional entropy are already linked as companion signals in this literature: a narrower vocabulary produces lower type-token ratios and reduced entropy over word and character distributions.

- _Detecting Synthetic Political Narratives_ — arxiv.org/pdf/2605.21540

## Readability formulas (closed-form, no dictionary needed)

**7. Flesch-Kincaid Grade Level / Flesch Reading Ease** Fully self-contained formulas over (words, sentences, syllables) — syllables approximated with a vowel-cluster regex heuristic rather than a lookup table: $$FK_{grade} = 0.39\left(\frac{\text{words}}{\text{sentences}}\right) + 11.8\left(\frac{\text{syllables}}{\text{words}}\right) - 15.59$$ Flesch-Kincaid Reading Ease is one of the most popular readability formulas for English text, calculating readability based on average sentence length and average syllables per word. Their intuition for why this tracks perceived quality: sentences with more clauses are harder to read, and words with more syllables are harder to read than words with fewer syllables.

- _Flesch, 1948; Kincaid et al._ (via arxiv.org/pdf/2101.10537, readable.com)

**8. Other closed-form readability indices** (same syllable/character/sentence-count inputs, different weighting — good for a comparison table): SMOG Index relies on the number of polysyllabic words and sentences; Coleman-Liau Index relies on average words and sentences per 100 words; Automated Readability Index relies on character, word, and sentence counts — all three avoid any word list, unlike Dale-Chall which needs one (and is therefore off-limits for you).

- _Deliberate Exposure to Opposing Views_ — arxiv.org/pdf/2401.14608

## Error/mechanics proxies

**9. Grammar/error-rate proxies as heavily-weighted features** This grounds the "regex-detectable irregularities" idea (repeated words, capitalization, punctuation spacing) as a legitimate substitute for a real grammar checker: Vajjala (2018) reported that measures of grammatical complexity and errors were assigned large weights among various linguistic features, and the original e-rater system built its scoring around exactly this category: e-rater uses 12 features, including grammatical errors and lexical complexity measures.

- _Ke & Ng, 2019; Burstein et al., 2004; Vajjala, 2018_ (via arxiv.org/pdf/2406.08817)

## Discourse/structure proxies

**10. Discourse-element presence as a score driver** Backs the "transition words as topic-sentence proxy" idea, and gives you a concrete number to cite for why structure matters this much: a score-6 essay is missing about half a discourse element on average, while a score-1 essay is missing about 5.1 discourse elements.

- _Automatic essay scoring system_ — image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/7831196

---

A practical note for your report: features #1, #3, #7, #8, #9 map cleanly onto rubric language you can quote/paraphrase ("motivation and interpretation of each feature"), while #5 and #6 (compression, entropy) are the ones worth flagging explicitly as "features an ordinary person wouldn't think of" since they're borrowed from information theory rather than writing-assessment literature — that contrast is worth a sentence in your write-up.