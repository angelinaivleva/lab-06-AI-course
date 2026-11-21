# REPORT — what the measurement showed, who it affects, and whose responsibility it is

All numbers below are copied from my submitted notebook run (Qwen2.5-3B-Instruct, greedy decoding, T4 GPU, N_PAIRS = 40 per group, SEED = 2026, 400 profiles per run).

## 1. Measurement read-out

**Bare agent (Part 1).** 0 of 400 replies were unparseable and 356 of 400 profiles were shortlisted. Every rejection scored 20–45 and every shortlist scored 45–85, so the decision boundary sits at a score of 45 (the 40–49 bin held 33 rejections and 31 shortlists).

| attribute | pairs | flips (rate, 95% CI) | its floor | floor flips | pairs with different scores | mean \|diff\| [95% CI] | floor mean \|diff\| |
|---|---|---|---|---|---|---|---|
| name_language | 40 | 2 (5.0% [1.4%, 16.5%]) | null_name | 0/40 | 11/40 | 3.12 [1.38, 5.25] | 2.00 [0.62, 3.62] |
| gender_coded_name | 40 | 4 (10.0% [4.0%, 23.1%]) | null_name | 0/40 | 6/40 | 1.12 [0.25, 2.13] | 2.00 [0.62, 3.62] |
| university | 40 | 1 (2.5% [0.4%, 12.9%]) | null_university | 1/40 | 10/40 | 2.50 [1.12, 4.12] | 2.12 [0.88, 3.62] |

Direction (each pair turned so the named group comes first):

- **Gender-coded name:** all 4 flips went against the male-coded name (0 vs 4, exact McNemar p = 0.125). Score gap male minus female −0.4 [−1.2, +0.6]. Across the profiles in these pairs, female-coded names were shortlisted 90.0% of the time and male-coded names 80.0%.
- **Name language:** Kazakh-language names 2/0 (p = 0.500), Russian 0/1, English 0/1 (both p = 1.000); score gaps +0.7, +0.2, −1.0 with intervals that all include 0.
- **University tier:** flagship vs regional 1/0 flips (p = 1.000), but a score gap of **+2.5 [+1.1, +4.1]** points with sign-test p = 0.002. This is the only comparison whose interval excludes zero.

**Read against the floors, no attribute stands clear of its own floor on decisions.** For each of the three attributes the 95% interval of the flip rate overlaps the interval of its floor, and university has exactly the same flip count as its floor (1/40). On the mean absolute score difference, name_language (3.12) is above null_name (2.00) and university (2.50) above null_university (2.12), but the intervals overlap in both cases. So this run does **not** support the claim that name language, gender coding or university moved the decision. The only signal is a small, one-directional score shift in favour of flagship universities (2.5 points on a 0–100 scale). It matters most near the boundary score of 45, where a few points can change the outcome. Five directional comparisons were tested, so one p = 0.002 should still be treated with care.

**What n = 40 could and could not detect.** An exact two-sided McNemar test at 0.05 needs at least 6 one-sided flips (15.0% of 40 pairs), or 8 if one goes the other way, or 10 if two do. The gender result (4 one-sided) is below that line. An effect that moves fewer than about 15% of pairs could not be told from chance here. "No measured effect" is therefore not "no effect".

**What the floors cover and what they do not.** A floor changes only one irrelevant detail in the same kind of field: another name of the same language group and gender coding, or another university of the same tier. It keeps the prompt wording, the field order and the model fixed. It does not measure how much the outputs move with another wording of the system prompt, another order of the lines (the optional order probe was not run), another model or another GPU. A stronger estimate would need many more pairs per attribute (to reach a test that can see effects of 5–10%), several prompt wordings, and the order probe.

**With the harness (Part 3).** Name and university were blinded, so the two members of every pair saw identical text and all flips and pair score differences became 0/40 **by construction**. That is not evidence that the harness changed the model. What changed for individual profiles: 110 of 400 outcomes changed (shortlist → escalate 88, shortlist → reject 15, reject → escalate 6, reject → shortlist 1). 166 of 400 scores changed, by a mean of 14.01 points, which is far more than any pair difference in Part 1. Removing two lines changes the form of the prompt as well as its content, so this does not prove that the model relied on name or university. Final outcomes: 254 shortlisted, 52 rejected, 94 sent to a person (23.5%), 0 refused. Model calls were unchanged (400, 1.00 per profile); prompt tokens fell from 84,409 to 77,922.

**Would this generalise?** Not from this run. A different prompt wording, a different model or real applicants (with real universities and names outside the three language groups in the data) could behave differently. The run supports only statements about this model, this prompt and these synthetic profiles.

## 2. Who is affected

**Wrongly rejected applicants.** If the gender direction is real (it is not significant), male-coded applicants near the boundary are the ones who lose a shortlist place: all 4 one-sided flips went against them. If the university score gap is real, applicants from regional universities lose about 2.5 points, which only changes the outcome when their score is near 45. For a scholarship the cost is direct: lost money, possibly a lost place at university, and in a yearly cycle a full year of waiting.

**Wrongly shortlisted applicants, and who they displace.** Shortlist places are limited. Every applicant pushed over the line by an irrelevant attribute (for example a flagship-university applicant gaining 2.5 points at the boundary) displaces someone with the same merit. The displaced person never learns why. The costs are not symmetric: a wrongly shortlisted applicant is usually re-checked at a later stage, while a wrongly rejected one is usually never looked at again.

**Applicants affected by the harness itself.** Blinding is not free. 15 applicants who were shortlisted by the bare agent were rejected after blinding, and 1 went the other way. These people bear the cost of a design change, not of their merit, and outside the 45–60 band nobody re-checks them. 94 applicants (23.5%) now wait for a human reviewer, including 18 whose GPA and exam score disagree strongly. Refused applicants (minors, people without consent) get no screening at all, so they need a non-automated route.

**Parties the brief does not mention.**
- The reviewers who must read 94 of 400 applications in this run (95 of the 406 log records).
- The fund, which carries the legal and reputational risk.
- People who never apply because they expect to be rejected.
- Applicants with names outside the three language groups in the data, or from universities the model has never seen; my measurement says nothing about them.

**If there is an effect my sample could not detect.** Any effect moving fewer than about 6 of 40 pairs one way is invisible to this run. Whoever bears such an effect bears it silently. Escalation would only catch it if the case happens to fall in the band.

**Compounding.** Used every year, even a 2.5-point skew repeats in each cohort. If the score is reused (by a later review, another fund, or a published ranking), the skew is carried into decisions that do not show where it came from.

## 3. Professional responsibility

**What I must check before it runs.** The measurement must be repeated with many more pairs, so that effects smaller than 15% can be seen, and with more than one prompt wording. The escalation band must be recalibrated on the harness's own scores: I planned 40.8% of applicants from the baseline scores and got 23.5%. The refusal rule must be checked against real names and programme titles, because a regex on words like "reject" or on brackets could refuse an honest applicant. On these 400 synthetic profiles it refused 0.

**What I must tell the people who deploy it.**
- The measurement had n = 40 per attribute and could not detect small effects.
- Blinding changed 110 outcomes and moved 166 scores by about 14 points, for reasons that are not necessarily about the hidden fields.
- The model still reasons on irrelevant fields. One logged reason reads "Average performance but consent to processing", which treats consent as merit.
- About 1 in 4 applications will need a human reviewer at the current band.

**What I must not claim.** That the harness made the system fair (the 0/40 flips after blinding are true by construction). That the model is unbiased. That the legal sources I cite as benchmarks bind this fund.

**What discharging this takes.** A test suite with the matched pairs re-run on every change of model or prompt; a documented limits section; a stop condition (for example, the fund does not deploy if any attribute exceeds its floor in a larger run). In this lab I built and ran the measurement, the five controls and the refusal test. The larger re-measurement, the recalibration and the review of the refusal rule on real data are left.

**What belongs to the institution.** Setting the purpose (scholarship screening only), choosing and training the reviewers, deciding how long records are kept and who reads them, and running an appeals process. None of these is in my code.

**Where the two conflict.** The fund may want escalation narrowed or removed because 94 human reviews cost money and time. I have measured what that band does: it sends boundary cases (all rejections scored ≤ 45) to a person. If the fund removes it, I would document in writing what the control was preventing and the measured cost of removing it, and not present the reduced system as equivalent.
