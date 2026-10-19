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
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `positive` |  |  |  |
| F1 — `negative` |  |  |  |
| F1 — `neutral` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



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