# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy

**Criterion:** The classifier must achieve an overall accuracy of at least **0.60** on each of the three held-out test splits generated with random seeds 42, 7, and 2024. It passes only if all three runs reach 0.60.

**Evaluation:** Read `accuracy` for each seed in `results_three_seeds_before.json`. Each value is the share of correctly classified posts in that seed's own held-out split (about 30 posts at 200 total).

**Why this target:** I chose 0.60 because it is substantially above the 33% random-guessing baseline for three labels while accounting for the small test sets. With about 30 posts per test split, each incorrect prediction has a noticeable effect on accuracy.

---

## 2. Per-label performance

**Criterion:** The F1 score for each of `analysis`, `hot_take`, and `reaction` must be at least **0.50** on each of the three held-out test splits generated with seeds 42, 7, and 2024. The same floor applies to every label, and it passes only if all nine label-by-seed scores reach 0.50.

**Evaluation:** Read the per-label `f1` for each seed in `results_three_seeds_before.json`, computed on that seed's own held-out split.

**Why this target:** I chose 0.50 as a reasonable minimum performance floor for every label. A single overall accuracy score could hide a label that the model handles badly while it does well on the others, so this floor makes strong performance on one label unable to cover for weak performance on another.

---

## 3. Label balance

**Criterion:** No single label may make up more than **50%** of the completed dataset. For a 200-post `labels.csv`, that is at most 100 posts of any one label.

**Evaluation:** Count the rows per label in the final `labels.csv` (the notebook's section 2 check prints these counts). The criterion passes if the largest label's share is 50% or less. This is checked once, on the whole file, not per seed. It is stricter than the notebook's built-in 70% cap, which only stops training and is not itself a target.

**Why this target:** I set 50% to prevent one label from dominating the dataset and to encourage a more balanced collection across `analysis`, `hot_take` and `reaction`.

---

## 4. Agreement with staff labels

**Criterion:** My labels must match the staff labels on at least **70%** of the 30 staff posts, which is at least 21 of 30 posts.

**Evaluation:** Label the 30 posts in `data/staff_posts.csv` under the definitions in `data/staff_taxonomy.md`, saved as `my_staff_labels.csv` with `text` and `label` columns and the text pasted unchanged. Then get `data/staff_labels.csv` from my TF, run `python agreement.py`, and read `agreement_rate` in `agreement_results.json`. This needs both files, and `data/staff_labels.csv` does not exist until my TF sends it. It is measured once, not per seed.

**What this does and does not test:** this measures my ability to apply the **staff's** definitions as written. It is not independent agreement on **my own** taxonomy, and it says nothing directly about whether my own 200 labels are consistent.

**Why this target:** The 30 staff posts include deliberately ambiguous examples, and the project documentation says an agreement rate in the 70s is normal, so 70% is the lower edge of what is expected rather than an easy number.

---

## 5. Prediction confidence

**Criterion:** On each of the three test splits generated with seeds 42, 7, and 2024, the accuracy of the most-confident third of test predictions must exceed the accuracy of the least-confident third by at least **0.10**. It passes only if all three runs show a gap of 0.10 or more.

**Evaluation:** For each seed in `results_three_seeds_before.json`, compute `confidence.accuracy_most_confident_third` minus `confidence.accuracy_least_confident_third`. Confidence is the model's top softmax score, and each third is `confidence.n_per_third` posts (about 10 at 30 test posts). These values only exist after the model is trained.

**Why this target:** Confidence should be a useful signal of how reliable a prediction is. The groups are small, so one post moves a third by about 0.10 and the result will be noisy, which is why the target is a modest gap.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
