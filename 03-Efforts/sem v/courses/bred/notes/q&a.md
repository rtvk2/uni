# Behavioral Research & Experiment Design — Practice Scenarios

_Six scenarios, each drilling one recipe your instructor keeps re-skinning across assignments. Try each one yourself first (cover the answer key), then check. Cross-references (§) point to your `l1-4.md` notes._

---

## Recipe 1 — Philosophy/Approach/Method/Data-type tagging + pragmatist synthesis

_(Drills the same recipe as Q1 on your in-class assignment — the mental-health survey question. Source: §6, §7, §10.)_

**Scenario:** A company's annual employee engagement survey shows that 40% of employees report low engagement. However, HR doesn't know _why_ engagement is low, and initiatives to fix it haven't worked. The company wants to understand the gap and design a better intervention.

**Your task:** a. Formulate two research questions: one primarily quantitative/positivist, one primarily interpretivist/qualitative. b. For each, identify: research approach (deductive/inductive/abductive), research method(s), type of data collected, and why your choices fit. c. Design a pragmatist mixed-methods study combining both.

<details> <summary>Answer key (click to expand — or just don't scroll yet)</summary>

**a1. Positivist/quant question:** "Is there a correlation between department, tenure, and self-reported engagement scores?"

- **Approach:** Deductive — you're starting from an existing theory (e.g., job-characteristics theory linking tenure/role to engagement) and testing whether the data supports it.
- **Method:** Survey with a validated engagement scale (e.g., Gallup Q12), administered company-wide.
- **Data type:** Quantitative — numeric engagement scores, categorical department/tenure variables, analyzed statistically (correlation/regression).
- **Why appropriate:** You need a large-n, comparable measure across many employees to spot patterns — exactly what positivist/quant methods are built for (§6, §9).

**a2. Interpretivist/qual question:** "How do employees experience and make sense of feeling 'disengaged' in their day-to-day work?"

- **Approach:** Inductive — no fixed theory going in; you're building understanding from employees' own accounts.
- **Method:** Semi-structured interviews or focus groups with a purposive sample of low-engagement employees.
- **Data type:** Qualitative — transcripts, coded thematically.
- **Why appropriate:** "Why/how do people experience X" questions need rich, first-person meaning-making that a survey scale can't capture (§6).

**c. Pragmatist mixed-methods design:** Run the quant survey first (broad diagnostic — which departments/tenure groups are lowest?), then use those results to purposively sample interview participants from the lowest-scoring groups (qual — understand _why_), and feed both into a practical intervention recommendation for HR. This is the same "what → why → what should be done" structure as the GenAI worked example in §6.

</details>

---

## Recipe 2 — Correlation ≠ causation / confound critique

_(Drills Q2 on your assignment — salary and job satisfaction. Source: §4's broccoli/Ayurveda examples, §17's confounding-variables framework.)_

**Scenario:** A study of 800 remote workers finds that employees who use video calls (vs. audio-only) more frequently report higher feelings of team belonging. Is this sufficient to conclude that video calls _cause_ stronger team belonging? Explain why or why not, and identify at least three factors you'd want to rule out first.

<details> <summary>Answer key</summary>

**No — this is correlational, not causal evidence.** Multiple alternative explanations exist for the same pattern:

1. **Reverse causation:** people who already feel more connected to their team might be _more willing_ to turn their camera on, rather than the camera causing the connection.
2. **Confound — role/seniority:** employees in more collaborative, client-facing, or senior roles might both use video calls more _and_ naturally have more team interaction/visibility, inflating belonging scores for unrelated reasons.
3. **Confound — personality/extraversion:** more extroverted employees may prefer video calls _and_ independently report higher social belonging regardless of call format.
4. **Confound — team culture:** some teams may have a norm of high video-call use _and_ independently strong belonging-building practices (e.g., regular social events), so the call format is a marker of a "high-belonging team culture," not its cause.

**What would strengthen the causal claim:** randomly assign otherwise-similar teams to mandatory video-on vs. audio-only meeting policies (turning it into a true or field experiment, §17) and measure belonging before/after, controlling for role, tenure, and team.

</details>

---

## Recipe 3 — Generating qualitative + quantitative questions on one topic

_(Drills Q3 — "should I get a bike?" Source: §9.)_

**Scenario:** Consider the question: _"Should I switch to a plant-based diet?"_ Formulate three qualitative and three quantitative research questions addressing different aspects of this decision.

<details> <summary>Answer key</summary>

**Qualitative (the "why/how do people experience it" framing, §9):**

1. How do people who have switched describe the social challenges of eating plant-based around family and friends?
2. What motivates someone to consider switching to a plant-based diet in the first place?
3. How do people experience changes in their relationship with food and cooking after switching?

**Quantitative (the "how many/how much" framing, §9):**

1. What is the average difference in monthly grocery spending between plant-based and omnivorous diets?
2. Is there a measurable difference in self-reported energy levels between people on a plant-based diet for 6+ months vs. those who aren't?
3. What percentage of people who start a plant-based diet are still following it after one year?

Note the pattern: qualitative questions use "how/why do people experience," quantitative questions use "what is the [measurable amount/rate/difference]" — this is the exact test given in §6's "how to recognize philosophy from a question" skill.

</details>

---

## Recipe 4 — Writing a null hypothesis and a non-directional alternative

_(Drills Q4 — sleep quality and cognitive performance. Source: §15.)_

**Scenario:** A researcher wants to examine whether daily social media use is related to self-reported anxiety levels among teenagers. The researcher measures daily social media usage (in hours) and anxiety scores (via a standardized scale) in a sample of teenagers with varying usage levels.

Formulate a null hypothesis (H₀) and a non-directional alternative hypothesis (H₁).

<details> <summary>Answer key</summary>

- **H₀:** Anxiety levels are not related to daily social media use among teenagers.
- **H₁ (non-directional):** Anxiety levels are related to daily social media use among teenagers (i.e., usage is _associated with_ anxiety, without specifying whether more use means higher or lower anxiety).

**Why non-directional, not directional:** notice the neutral verb "related to" / "associated with" rather than a comparative like "increases" or "is higher than" — that's the tell for non-directional/two-tailed phrasing (§15's reading tip). If the question had said "does higher social media use _increase_ anxiety," you'd write a directional H₁ instead: "Higher daily social media use is associated with higher anxiety levels."

</details>

---

## Recipe 5 — IV/DV/confound identification + operationalization + experiment-type classification

_(New recipe territory now that Lecture 5–6 material is in play. Source: §17, §18.)_

**Scenario:** A student claims: _"I always study better with background music playing."_ A researcher wants to test this by comparing study performance with music on vs. music off.

**Your task:** a. Write a testable hypothesis (Hₐ) from the claim, and identify the IV and DV within it. b. Operationalize the DV concretely (what would you actually measure?). c. Operationalize the IV (what exactly are the two conditions?). d. Name two plausible confounding variables and explain how each could offer an alternative explanation for the results. e. Classify this as a true experiment, quasi-experiment, non-experiment, or field experiment, and justify your answer.

<details> <summary>Answer key</summary>

**a.** Hₐ = "Study performance is affected by whether background music is playing." IV = background music (present/absent); DV = study performance.

**b.** DV operationalized as: number of correct answers on a standardized 20-question comprehension quiz taken immediately after a 30-minute study session.

**c.** IV operationalized as: Condition A = instrumental background music played at a fixed, moderate volume throughout the session; Condition B = silence, same room, same duration.

**d. Confounds:**

1. **Prior familiarity with the study material** — if the same material is reused across both conditions in a within-subjects design without counterbalancing, whoever studies second may simply have had more total exposure to the topic, inflating their score regardless of music (this is an order effect, §18).
2. **Individual differences in music preference/sensitivity to distraction** — a between-subjects design without matching could have, by chance, more highly music-sensitive (easily distracted) people in one group, making music look worse than it really is.

**e.** This is a **true (controlled) experiment**: the IV (music on/off) is fully manipulated by the researcher, the setting is controlled (lab or standardized study room), and if participants are randomly assigned to condition order (within-subjects) or randomly assigned to group (between-subjects), causal claims about music's effect on performance are supportable — unlike a quasi-experiment, where "prefers to study with music" would be an existing trait rather than something assigned.

</details>

---

## Recipe 6 — Choosing within-subjects vs. between-subjects, and controlling order effects

_(Source: §18.)_

**Scenario:** Continuing Recipe 5's music-and-studying experiment: the researcher has 40 available participants and wants to compare the "music" and "silence" conditions.

**Your task:** a. Should this be run as a within-subjects or between-subjects design? Justify your choice, including at least one specific risk of your chosen approach. b. If within-subjects, describe how you'd counterbalance the design. If between-subjects, describe how you'd form the two groups to reduce confounds.

<details> <summary>Answer key</summary>

**a.** A **within-subjects design** is often preferable here because it needs fewer participants overall and minimizes random error from individual differences in baseline studying ability (someone who's just naturally a fast reader would be a confound in a between-subjects comparison). **Risk:** order effects — if everyone studies under "music" first and "silence" second, any improvement in the silence condition might just be practice/familiarity with the quiz format, not the absence of music. There's also a risk that doing both conditions makes the study's purpose obvious to participants, which could trigger demand characteristics (§17) — e.g., someone believing music helps them might unconsciously try harder during the music condition.

**b.** Use **simple counterbalancing**: half the participants do music→silence, the other half do silence→music (AB/BA), so order effects cancel out across the whole sample. For extra rigor, use **complex counterbalancing (ABBA)** with two study sessions per condition per person (music, silence, silence, music), averaging each participant's two "music" scores and two "silence" scores — this also helps average out any random/fatigue-related noise within a single sitting (§17's "random variable" category, §18).

_(If a between-subjects design were chosen instead — e.g., because doing two full study sessions is too time-consuming or risks contaminating the material — the answer would instead describe a matched-pairs approach: pre-testing all 40 participants on a baseline reading-comprehension measure, then splitting them into two groups matched on that baseline score before randomly assigning one group to music and one to silence, per §18's matched-pair design section.)_

</details>

---

### How to use this set

- Try each scenario cold before opening the answer key — that's closer to exam conditions than reading the key first.
- If your next in-class assignment reuses this same six-recipe pattern with a new cover story, you should be able to slot the new scenario straight into whichever recipe it matches (checking the verb/phrasing tells from §6, §9, and §15 to figure out _which_ recipe applies is itself a testable skill).
- Send me the new slides whenever they're up and I'll fold any new recipe types (e.g., a specific statistical test choice, or a new research-strategy pairing) into an updated version of this set.