# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

Community: r/kpop_uncensored. Labels: `positive`, `negative`, `neutral`.
Data: 208 labeled examples — negative 79 (38%), neutral 69 (33%), positive 60 (29%).

Every target below has to hold on **all three seeds** (42, 7, 2024) to count as met.

---

## 1.

Macro F1 of at least **0.55** on the held-out test set.

**Why this target:**

My three labels are fairly balanced, so a coin-flip guesser lands around 0.33 and always picking `negative` gives a macro F1 of about 0.18. But 89 of my 208 labels (43%) were flagged "verify" while labeling, so the labels are noisy, and the score has to clear 0.55 on all three seeds, not on average. 0.55 is well above guessing without assuming cleaner labels than I have.

---

## 2.

The trained model's macro F1 beats the baseline's macro F1 by at least **0.05** on the same test posts.

**Why this target:**

The baseline only sees my one-sentence definitions. Sentiment is something a general language model already understands, so the baseline may do well on this task. If 208 examples of *my* labeling rules can't add 5 points over the definitions alone, then the fine-tuning didn't learn anything specific to how this community talks (sarcasm, "no shade" hedges, criticism framed as questions).

---

## 3.

Every label has an F1 of at least **0.45** on the held-out set.

**Why this target:**

`positive` is my smallest label at 60 examples, so only about 9 land in the test split, and one wrong post moves its F1 by roughly 10 points between seeds. That's why this per-label floor is looser than criterion 1. It still catches a model that gets a decent average by mostly ignoring `positive`.

---

## 4.

At most **20%** of the test posts that are truly `positive` or `negative` are predicted as the other one.

**Why this target:**

My hardest boundary is a post that defends an idol by attacking the people criticizing them. My decision rule sends those to `negative`, because the post's main purpose is the complaint. Swapping positive and negative is the worst mistake this classifier can make, worse than either one being confused with `neutral`. If it happens more than 1 time in 5, the model hasn't learned the rule, or I didn't apply it consistently.

---

## 5.

On the 30-post staff agreement set, I agree with the staff labels on at least **21 / 30 (70%)**.

**Why this target:**

All 208 of my labels were pre-labelled with AI before I reviewed them, so my own consistency as a labeler hasn't been tested yet. 43% of my posts were borderline enough to flag, and the staff set is chosen to include ambiguous posts. So I expect real disagreements, but I'd want to agree on at least 7 in 10 before trusting my labels as a training signal. Below that, a low model score would be a labeling problem, not a model problem.

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