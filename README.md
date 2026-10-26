# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

TakeMeter sorts text posts and comments from r/kpop_uncensored, a public subreddit for unfiltered K-pop discussion, by the poster's stance toward the K-pop subject the post is mainly about. Each post gets one of three labels: `positive` (praise, enjoyment, support), `negative` (complaints, criticism, worry), or `neutral` (questions, explanations, analysis with no clear stance). The subreddit is a good test because a lot of its negativity is indirect: criticism framed as a "genuine question," praise of an idol inside a rant about fans, or "no shade" hedges in front of a takedown.

---

## Label Taxonomy

### `positive`

**Definition:** The poster expresses enjoyment, praise, or support for the K-pop subject the post is mainly about.

**Example 1:**
> Does anyone still listen to B.I?His solo work is amazing
> I was a huge fan of IKON and their music after the whole scandal I lost interest in the group.After that a while ago I listened to B.I's solo work and really enjoyed them..I hope his other song's get the same love as BTBT.

**Example 2:**
> Do you remember Kep1er's Up and Wing Wing?
> After a really long time I listened to these songs and I love them. Their song writer was really really talented. The girls did so well too I don't follow them now what's going on with Kep1er?

### `negative`

**Definition:** The poster complains about, criticizes, or expresses worry or disapproval of the K-pop subject the post is mainly about.

**Example 1:**
> Honestly I haven’t been the biggest fan of the makeup and contacts that are used for idols recently. Makeup trends and contacts have always been there, but I feel like in the past the idols’ features were still accentuated somehow? Nowadays, it feels like everyone is wearing the same/very similar makeup that drowns out their features.

**Example 2:**
> High Up ent is literally wasting UNCHILD's potential
> After the news of the delayed comeback, the company has provided no updates about the group. Like, what's going on at this point? They're still a nugu group; this kind of hiatus will hurt them

### `neutral`

**Definition:** The poster asks a question, explains facts, or analyzes without taking a favorable or unfavorable stance on the subject.

**Example 1:**
> How can a group qualify as the XX gen Leader?
> This is probably opinion based, we can all agree on the 3rd and 2nd gen leaders though but why not 5th gen.
> I've seen many people say Zerobaseone, Cortis, Illit but do we measure it by popularity? Or something else?
> Was just wondering.

**Example 2:**
> Why Do K-Pop Albums Have So Many Versions?
> I apologize if this is a dumb question, but can someone please explain why some companies release like 5+ versions of the same song or album? I was about to listen to ENHYPEN’s new album and saw that there are like 7 different versions out. Why is that?

### The hardest boundary

**Which two labels:** `positive` vs. `negative`, when a post defends an idol by attacking the people criticizing them.

**The decision rule I used every time:**

First find what the post is mainly about: the thing the title and most sentences are aimed at. Then label the poster's attitude toward *that*. If the main purpose is complaining about people (fans, haters, a company, a fandom habit), the post is `negative`, even if it praises or defends an idol along the way. It's `positive` only when the main purpose is praise or enjoyment and any complaint is a side note.

**Second rule, for `neutral` vs. `negative`:** Framing a post as a question doesn't make it `neutral`. If the body of the "question" states criticism as fact ("let's be honest, she's a terrible vocalist"), it's `negative`. It's `neutral` only when the poster is genuinely asking or explaining and offers no verdict of their own.

---

## The Dataset

**Where the posts came from:** r/kpop_uncensored (public subreddit), text posts only, no image posts. I copied posts by hand from the subreddit because new Reddit API access requires manual approval. 141 entries are original posts; 67 are replies from those posts' comment threads, marked "likely a comment" in the note column. Duplicates (the same post copied twice) were removed.

**How I labelled them:** In two passes. First, I labelled 20 rows cold, on my own, using only my definitions and the decision rules above, without looking at any AI labels. These are marked `cold` in the `note` column. The remaining 188 rows were pre-labelled by an AI model (Claude) in batches. Each came back with a label and a note: either `evidence:` followed by the phrases that decide the label, or `verify:` explaining why the post could fit two labels (89 rows). These are marked `pre-labelled` in the `note` column. I then reviewed the pre-labelled rows, focusing on the `verify` rows, and changed 1 label.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `negative` | 79 | 38.0% |
| `neutral` | 69 | 33.2% |
| `positive` | 60 | 28.8% |
| **Total** | 208 | 100% |


**Three hard cases**

**1.**
> *The post:* "Bro k pop fans need to calm down" — the poster defends UNCHILD's Heekie going on hiatus ("Let my girl rest... they are people too") while attacking a commenter and calling the HYBE curse "bs."
>
> *Could have been:* `positive` (supports Heekie) or `negative` (angry at fans).
>
> *I chose, because:* `negative`. The title and most of the post are aimed at other fans, not at Heekie, so by my decision rule the main purpose is the complaint.

**2.**
> *The post:* "I've had this curiosity for a while now... what is so special about her to reach this level of popularity?" — a long post about Lisa, framed as an honest question, that calls her "a terrible vocalist," her performances "quite repetitive," and her album "very poorly received."
>
> *Could have been:* `neutral` (it's a question asking for other opinions) or `negative`.
>
> *I chose, because:* `negative`. The poster answers their own question with criticism in nearly every paragraph. Under my second rule, question framing doesn't make it neutral when the body is a verdict.

**3.**
> *The post:* "MEOVV's latest B Side is so good! Teddy gives these girls those edm empty chorus 100 times recycled songs every comeback and is fully wasting their talent... should have been the title track instead of Go Hard"
>
> *Could have been:* `positive` (praise for the B-side and MEOVV) or `negative` (criticism of Teddy and the title track).
>
> *I chose, because:* `positive`. The post is mainly about the B-side, and the attitude toward it is clearly enthusiastic. The Teddy complaint is there to argue the B-side deserved more, so it supports the praise instead of replacing it.

---

## The Training Run

**Base model:** `distilbert-base-uncased`
 
**Settings:** 3 epochs, learning rate 2e-5, batch size 16, max length 128 tokens, seed 42. Trained on an Apple Silicon GPU (MPS) with torch 2.14.1.
 
**Anything I changed from the defaults, and why:** Nothing. The only edit was setting `LABELS = ["positive", "negative", "neutral"]` to match my CSV. I kept `MAX_LENGTH = 128` on purpose for this first run, even though it's a known problem for my data: my median post is about 131 words, and roughly 63% of posts are longer than 128 tokens, so the model only sees the start of most posts. That matters for my labels, because many posts open neutrally and only show their stance later (criticism framed as a question, "no shade, but…"). If I add the stretch run, I'll change one setting at a time.
 
**Split sizes:** 70/15/15 stratified, done by the notebook: 144 train · 32 val · 32 test.
 
| Label | Train | Val | Test |
|---|---|---|---|
| `positive` | 42 | 9 | 9 |
| `negative` | 55 | 12 | 12 |
| `neutral` | 47 | 11 | 11 |
 
`positive` has only 9 test posts, just above the notebook's 8-post warning line. One post moves its F1 by about 10 points, so expect that score to swing between seeds next unit.
 
**What the run looked like:** Training loss only went from 1.096 to 1.064 and validation loss from 1.090 to 1.080 over 3 epochs. For three balanced labels, random guessing is about 1.10, so the model was still close to guessing when training stopped. That's only 27 update steps (144 posts ÷ 16 per batch × 3 epochs). Results on the held-out test set (seed 42): accuracy 0.469, macro F1 0.372; F1 `positive` 0.364, `negative` 0.600, `neutral` 0.154. The most-confident third of predictions was right 60% of the time, versus 40% for the least-confident third. Saved in `results.json` and `test_split.csv`.

---

## How I Used AI

**Moment 1**

- *What I asked for:* After I'm done drafting a first version of `criteria.md`, I asked it to double-check my criteria against the template and the grading rubric to see if they were reasonable and testable.
- *What came back:* It said criterion 4 ("at most 20% of positive/negative posts predicted as the other") could be read two ways and needed a formula. It also caught that an edit I made had replaced criterion 5's target line, so criterion 5 no longer named a number. And it warned me not to change any targets after seeing my first training results, because lowering a missed target costs the point.
- *What I changed:* I added the formula to criterion 4, (positive→negative + negative→positive) ÷ all true positive and negative test posts, which is at most 4 swaps out of 21 with seed 42. I restored criterion 5's 21/30 target and updated its reason to say 20 rows were labelled cold and 188 were pre-labelled. I left all five targets unchanged, even though my seed-42 run already misses criteria 1 and 3.

**Moment 2**

- *What I asked for:* For each batch, I asked Claude to label every post and return the rows in my CSV template and follow my rule for label definition and pre-labelled samples I did on my own.
- *What came back:* Labeled rows with no notes and hard to verify all the labels are correct. When it merged the batches, it also pointed out that posts defending an idol by attacking critics had been labeled inconsistently, some `positive` and some `negative`.
- *What I changed:* I had it replace the empty note with evidence for each label, quoting the exact phrases that decide it, or `verify:` with a reason for borderline rows, so I could check each one quickly. 

**Pre-labelling disclosure:**
I labeled the first 20 rows myself, cold, before using any AI. These are marked cold in the note column. The remaining 188 rows were pre-labelled by an AI model. I pasted the posts in batches, and it returned a label for each one with a note quoting the phrases that decided it, or a verify: reason when the post could fit two labels. These rows are marked pre-labelled in the note column.

I then the next pre-labelled row and changed 1 labels. Most changes were among the verify rows. 

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.688 | 0.469 | -0.219 |
| Macro F1 | 0.686 | 0.372 | -0.313 |
| F1 — `negative` | 0.667 | 0.600 | -0.067 |
| F1 — `neutral` | 0.696 | 0.154 | -0.542 |
| F1 — `positive` | 0.696 | 0.364 | -0.332 |

**What I predicted before I looked:**
I took a glance at the F1 values from running the notebook in Unit 5. The F1 of negative class is the highest and it barely got trained only 3 epochs. Therefore, my guess was that the trained model would only beat the baseline on negative with F1 of 0.600, because it predicted negative for almost every post, and would lose badly on neutral with lowest F1 (0.154).

**What the gap actually means:**

My fine-tuning added nothing; the short fine-tuning even created confusion in making prediction. The zero-shot baseline beat my trained model on every measure (macro F1 0.686 vs. 0.372). My prediction was half right: it lost worst on `neutral` (−0.542), but it also lost on `negative`, because calling 28 of 32 posts `negative` gave perfect recall but poor precision (0.43).

The gap comes from my training run, not the task. After only 27 steps the loss was still near guessing level (about 1.10), so the model fell back to my largest label. The baseline already understands praise vs. complaint vs. question, and it read each whole post while mine read only the first 128 tokens. Criterion 2 is missed: the trained model needed 0.736 and reached 0.372.

---

## Run Log — Before

All three seeds trained on an Apple Silicon GPU (MPS), torch 2.14.1, same settings as unit 5 (3 epochs).

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Macro F1 | ≥ 0.55 | 0.372 | 0.182 | 0.361 | MISSED |
| 2. Beats baseline macro F1 | ≥ +0.05 (≥ 0.736) | −0.314 (0.372) | −0.504 (0.182) | −0.325 (0.361) | MISSED |
| 3. Every label's F1 | ≥ 0.45 | positive 0.364, neutral 0.154 | positive 0.000, neutral 0.000 | positive 0.000 | MISSED |
| 4. positive↔negative swaps | ≤ 20% | 6/21 = 28.6% | 9/21 = 42.9% | 8/21 = 38.1% | MISSED |
| 5. Staff agreement | ≥ 21/30 | 30/30 | 30/30 | 30/30 | MET |

Criterion 2 compares every seed against the baseline's seed-42 score (0.686), because `baseline.py` scored only the seed-42 test split. Only the seed-42 column is like-for-like. Seeds 7 and 2024 test on different posts, so their differences are approximate.

**Spread across seeds** (from the notebook's section 6):

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.469 | 0.375 | 0.469 | 0.094 |
| Macro F1 | 0.372 | 0.182 | 0.361 | 0.191 |
| F1 — `positive` | 0.364 | 0.000 | 0.000 | 0.364 |
| F1 — `negative` | 0.600 | 0.545 | 0.556 | 0.055 |
| F1 — `neutral` | 0.154 | 0.000 | 0.526 | 0.526 |

Accuracy moved 0.094 across seeds, just under the 10-point line the notebook flags. But macro F1 moved 0.191 and per-label F1 moved as much as 0.526, and seed 7 predicted `negative` for every post. On a 32-post test set the per-label scores aren't stable enough to trust from a single split, which is why every verdict above uses all three seeds.

### Confusion matrix

| true \ predicted | positive | negative | neutral |
|---|---|---|---|
| **positive** | 2 | 6 | 1 |
| **negative** | 0 | 12 | 0 |
| **neutral** | 0 | 10 | 1 |

**My biggest off-diagonal number, and what it means:**

10 real `neutral` posts were called `negative`, and 0 `negative` posts went the other way. The model never learned the `neutral` boundary; it defaulted to `negative`, my largest training label (55 of 144). This is a collapse to one label, not confusion between two: 6 of 9 `positive` posts also went to `negative`, and 28 of 32 predictions overall were `negative`. Seed 7 went all the way: it predicted `negative` for every post (accuracy 0.375 = 12/32, `positive` and `neutral` F1 both 0). The notebook says a lopsided pair usually points at labelling, but because every label drains into the same column and the training loss stayed at guessing level, I read this as an undertrained model. The agreement report will check whether my `neutral`/`negative` labels are also part of the problem.


---

## Verdicts and Diagnoses

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Macro F1 on held-out set | ≥ 0.55 | MISSED | 0.372 / 0.182 / 0.361 across seeds 42 / 7 / 2024; below target on all three |
| 2 | Trained beats baseline macro F1 | ≥ +0.05 (≥ 0.736) | MISSED | Each seed vs the baseline's 0.686: −0.314 / −0.504 / −0.325 across seeds 42 / 7 / 2024; below the baseline on all three. Only seed 42 is like-for-like, because the baseline scored only that test split |
| 3 | Every label's F1 | ≥ 0.45 | MISSED | `positive` 0.364 / 0.000 / 0.000; `neutral` 0.154 / 0.000 / 0.526; at least one label below target on every seed |
| 4 | positive↔negative swaps | ≤ 20% | MISSED | 6/21 (28.6%), 9/21 (42.9%), 8/21 (38.1%); all swaps were positive → negative |
| 5 | Agreement with staff labels | ≥ 21/30 | MET | 30/30 (100%) from `agreement.py` |

**Diagnoses**

All four misses share one main cause: **the model can't reach this task with this much training.** It collapsed to my largest training label, `negative` (55 of 144). Evidence: the training loss stayed near the 1.10 guessing level after only 27 update steps; seed 42 predicted `negative` for 28 of 32 posts and seed 7 for all 32; and confidence averaged about 0.37 whether right or wrong, which is a model scoring all three labels nearly equally.

**The agreement report points away from a labelling problem as the main cause.** I matched staff on 30/30 posts, so pile (a), "applying a written rule loosely," is empty, and I can apply a clear rule consistently. If my labels were the main problem, I'd expect the model to confuse specific label pairs in both directions. Instead every label drains into one column. Two limits: the staff set tested a different, easier taxonomy, and 188 of my 208 labels were AI pre-labelled, with six boundary posts I know contradict my own rule. Labelling noise may still matter, but it can't show up until the model learns enough to make real distinctions.

- **Criterion 1 (macro F1):** undertrained model collapsing to the majority label, as above.
- **Criterion 2 (baseline):** same cause, plus a head start for the baseline. BART-MNLI is pre-trained to judge whether text fits a description, and it read each whole post. My model read only the first 128 tokens (about 63% of my posts are longer) and had 144 examples.
- **Criterion 3 (per-label F1):** collapse plus **too few examples for `positive`**. It's my smallest label (42 in training, 9 in test), so one post moves its F1 about 10 points, and it scored 0.000 on two seeds. `neutral` swung from 0.000 to 0.526, which shows how much the per-label scores depend on which posts land in the split.
- **Criterion 4 (positive↔negative):** every swap on every seed went positive → negative, with 0 going the other way. That's the collapse again, not real confusion on my hardest boundary, so this run can't yet tell whether that boundary is **a genuinely hard pair** or inconsistently labelled. A better-trained model is needed before this criterion can test the boundary.

---

## Agreement Report

**Agreement rate:** 30 / 30 = 100%

**Disagreements**

None. I matched staff on all 30 posts, so there is nothing to sort into piles (a), (b) or (c).

**Was the set easy, or is the rule tight?** Some of both. Most posts fit one definition cleanly, and the ids are grouped by label (sp_001–010, 011–020, 021–030), which may have hinted at the answer. But staff's own file flags 7 of the 30 as hard, and I matched all 7: sp_005 and sp_010 (`analysis` that reads like opinion or rests on a tiny sample), sp_014 (a 3% stat used as decoration, so `hot_take` under rule 1), sp_019 and sp_020 (general claims that sound reflective or observational, so `hot_take`), and sp_028 and sp_029 (personal posts with a countable fact or a shoe observation, but still `reaction` under rules 2 and 3). Agreeing on the posts chosen to be hard, across all three rules, suggests the rules are tight enough to apply the same way twice, with the caveat that the id order made this set easier than a shuffled one.

**What the pattern in my disagreements tells me:**

With no disagreements, there's no evidence that I apply a written rule loosely, so pile (a) is empty. That counts against "inconsistent labelling" as the cause of my model's misses, and supports my reading that the model collapsed to `negative` because it was undertrained (loss near 1.10 after 27 steps, every label draining into one column). Two limits on that conclusion: this tested a different, easier taxonomy, not my own; and 188 of my 208 labels were AI pre-labelled, and I already know six boundary posts in my data were labelled against my own decision rule. So it shows I can apply a clear rule, not that my own labels are clean.


---

## The Improvement 1

**What I changed:** `EPOCHS` from 3 to 10 (about 90 update steps instead of 27). Nothing else.

**Which diagnosis pointed at it:** All four misses traced to the model collapsing to `negative`: training loss stuck at the 1.10 guessing level after 27 steps, and seed 7 predicted `negative` for every post. My 30/30 staff agreement argued against a labelling cause, so I changed training rather than labels.

### Run Log — After

`EPOCHS` changed from 3 to 10 (about 90 update steps instead of 27); every other setting the same as Before. Apple Silicon GPU (MPS), torch 2.14.1.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Macro F1 | ≥ 0.55 | 0.500 | 0.408 | 0.586 | MISSED |
| 2. Beats baseline macro F1 | ≥ +0.05 (≥ 0.736) | −0.186 (0.500) | −0.278 (0.408) | −0.100 (0.586) | MISSED |
| 3. Every label's F1 | ≥ 0.45 | all 0.500 | positive 0.421, neutral 0.250 | all ≥ 0.533 | MISSED |
| 4. positive↔negative swaps | ≤ 20% | 6/21 = 28.6% | 6/21 = 28.6% | 4/21 = 19.0% | MISSED |
| 5. Staff agreement | ≥ 21/30 | 30/30 | 30/30 | 30/30 | MET |

Criterion 2 compares every seed against the baseline's seed-42 score (0.686), because `baseline.py` scored only the seed-42 test split. Only the seed-42 column is like-for-like.

**Spread across seeds:**

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.500 | 0.438 | 0.594 | 0.156 |
| Macro F1 | 0.500 | 0.408 | 0.586 | 0.178 |
| F1 — `positive` | 0.500 | 0.421 | 0.533 | 0.112 |
| F1 — `negative` | 0.500 | 0.552 | 0.615 | 0.115 |
| F1 — `neutral` | 0.500 | 0.250 | 0.609 | 0.359 |

Accuracy moved 0.156 across seeds, more than the 10-point line, so the dataset is too small for a stable measure. Seed 2024 meets criteria 1, 3 and 4, and seed 42 meets criterion 3, but a target has to hold on all three seeds. Seed 7 misses all of them.

**Did it help, and how do I know:**

Yes, partly. Comparing Run Log — Before and After on the same three seeds:

- **Macro F1 rose on every seed:** 0.372 → 0.500, 0.182 → 0.408, 0.361 → 0.586 (mean 0.305 → 0.498).
- **The collapse is gone.** Before, seed 7 predicted `negative` for every post and `positive` F1 was 0.000 on two seeds. After, `positive` F1 is at least 0.421 on every seed, and the seed-42 matrix spreads predictions across all three labels.
- **Positive↔negative swaps now go both ways** (seed 7: 3 and 3) instead of all positive → negative. The model is now making real distinctions on that boundary, so criterion 4 is now measuring the boundary itself, not the collapse.
- **Confidence now means something:** on seeds 42 and 7 the most-confident third was right 70% of the time and the least-confident third only 20% (seed 2024 didn't follow the pattern).

It still misses criteria 1–4, and it's still below the baseline (0.500 vs 0.686 on seed 42). Seed 2024 meets criteria 1, 3 and 4, but seed 7 misses them all, and a target has to hold on all three. Two results limit how far the fix can go. First, accuracy now moves 0.156 across seeds, over the 10-point line, so with 32 test posts the measurement itself is unstable. Second, in the 10-epoch training run, training loss fell from 1.10 to 0.63 while validation loss only moved from 1.09 to about 1.02. The model fits its 144 training posts far better than new ones. So more epochs fixed the undertraining, and what limits the model now is the amount of data, not the number of training steps.

---

## The Improvement 2 (Stretch Features)

**Stretch features:** a second measured improvement (`MAX_LENGTH` 128 → 256) and a confidence check (most- vs. least-confident accuracy, reported in both improvements).

**What I changed:** `MAX_LENGTH` from 128 to 256 tokens. Nothing else: `EPOCHS` stayed at 10, with the original labels and the same three seeds as Run Log — After.

**Which diagnosis pointed at it:** After Improvement 1, the biggest confusion was still `neutral` vs `negative`, and the positive↔negative swaps (28.6% on two seeds) still missed criterion 4. About 63% of my posts are longer than 128 tokens, and many only reveal their stance late (criticism framed as a question, "no shade, but…"), so the model was labelling posts it had only partly read.

### Run Log — After 2

`MAX_LENGTH` changed from 128 to 256; every other setting the same as Run Log — After. Apple Silicon GPU (MPS), torch 2.14.1.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Macro F1 | ≥ 0.55 | 0.586 | 0.446 | 0.489 | MISSED |
| 2. Beats baseline macro F1 | ≥ +0.05 (≥ 0.736) | −0.100 (0.586) | −0.240 (0.446) | −0.197 (0.489) | MISSED |
| 3. Every label's F1 | ≥ 0.45 | all ≥ 0.471 | neutral 0.222 | all ≥ 0.455 | MISSED |
| 4. positive↔negative swaps | ≤ 20% | 4/21 = 19.0% | 3/21 = 14.3% | 4/21 = 19.0% | **MET** |
| 5. Staff agreement | ≥ 21/30 | 30/30 | 30/30 | 30/30 | MET |

Criterion 2 compares every seed against the baseline's seed-42 score (0.686). Only the seed-42 column is like-for-like.

**Spread across seeds:**

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.594 | 0.469 | 0.500 | 0.125 |
| Macro F1 | 0.586 | 0.446 | 0.489 | 0.140 |
| F1 — `positive` | 0.667 | 0.500 | 0.462 | 0.205 |
| F1 — `negative` | 0.621 | 0.615 | 0.552 | 0.069 |
| F1 — `neutral` | 0.471 | 0.222 | 0.455 | 0.248 |

Accuracy moved 0.125 across seeds, still over the 10-point line, so the measurement is still unstable.

### Confusion matrix — after 2 (seed 42)

| true \ predicted | positive | negative | neutral |
|---|---|---|---|
| **positive** | 6 | 3 | 0 |
| **negative** | 1 | 9 | 2 |
| **neutral** | 2 | 5 | 4 |

**Biggest off-diagonal:** 5 real `neutral` posts were called `negative`, and only 2 went the other way.

**Did it help, and how do I know:**

Partly. Comparing Run Log — After and After 2 on the same three seeds:

- **Criterion 4 is now met on every seed** (19.0% / 14.3% / 19.0%, down from 28.6% / 28.6% / 19.0%). This is the first model criterion I've met, so reading more of each post helped the model separate `positive` from `negative`.
- **Macro F1 barely moved overall** (mean 0.498 → 0.507). Seed 42 rose (0.500 → 0.586) and seed 7 rose a little (0.408 → 0.446), but seed 2024 fell (0.586 → 0.489). That change is smaller than the 0.125 spread between seeds, so I can't call it real.
- **Confidence is now consistent:** on every seed, the most-confident third was right 70–80% of the time and the least-confident third only 20–30%.

It did not fix `neutral`. That label is still the weakest (0.222 on seed 7), and the biggest confusion is still `neutral` posts called `negative`, 5 one way and 2 the other. Reading more of each post helped the positive/negative boundary but not the line between a genuine question and criticism phrased as one.

---

## What's Still Broken

**Criterion 1 (macro F1 ≥ 0.55): 0.586 / 0.446 / 0.489.** Only seed 42 clears it. What I'd do: label more posts, especially `neutral`, the weakest label on every run. Improvement 1 showed the model is limited by data, not training steps (training loss fell to 0.63 while validation loss stayed near 1.02), and Improvement 2's longer inputs barely moved macro F1 (mean 0.498 → 0.507). Why I stopped: another 100+ carefully labelled posts is a full labelling pass, not a one-change fix.

**Criterion 2 (beat the baseline by 0.05): −0.100 to −0.240 behind.** What I'd do: score the baseline on all three seed splits so every column is like-for-like, and try a larger pre-trained model fine-tuned on my labels. Why I stopped: zero-shot BART with my written definitions is the stronger classifier on this data. Two training changes cut the seed-42 gap from −0.314 to −0.100, but not to a lead.

**Criterion 3 (every label ≥ 0.45): seed 7's `neutral` at 0.222.** Seeds 42 and 2024 now clear it, but `neutral` swings from 0.222 to 0.471 depending on the split. What I'd do: review the `neutral`/`negative` rows against my second rule (criticism framed as a question is `negative`), since that pair is the model's biggest confusion in every run, and relabel the 6 `positive` posts I found that break my hardest-boundary rule. Why I stopped: each relabel needs its own measured run, and with 32-post test sets (accuracy moving 0.125–0.156 between seeds), I couldn't tell a small real gain from split noise.

**The gap between what I meant my labels to capture and what the model learned:**

I meant `neutral` to mean "asks or explains without taking a stance," but the model learned something closer to "doesn't sound angry": on seed 42 it still called 5 of 11 real `neutral` posts `negative`, with only 2 going the other way. It did learn my `positive`/`negative` boundary well enough to meet criterion 4 on every seed, but not the subtler line between a genuine question and a criticism phrased as one.

<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**