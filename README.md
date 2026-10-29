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

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

I have chosen the community about "What's for Dinner Tonight?" where people shared what they have for dinner. Some posts mentioned the recipe for food, some posts mentioned restaurants' name and some posts are just comments of people being hungry. A lot of posts also mentioned their family member cooked for them. A very rarely amount of these posts cite number.

My classifier sorts posts into: home_cooked, bought_prepared and chatter

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `home_cooked`

**Definition:** The poster or someone in their household made the meal at home.

**Example 1:**

> Large bowl of my special split pea and ham soup and 3 glasses of milk. :chef: Later tonight :fart2:. Good thing my sweety is away for a couple nights.:whistling:

**Example 2:**

> blackeyed peas,fried squash,corn on the cob,all made with love by my wonderful wife without whom i would be a skinny sickly looking runt:brows:

### `bought_prepared`

**Definition:** This covers posts that mentioned a business made the meal such as restaurants, takeout, delivery and fast food.

**Example 1:**

> It's a subway night here. We did grocery shopping tonight. Did regular shopping plus got all the fixings for the Thanksgiving day dinner. So no cooking here tonight.

**Example 2:**

> We're celebrating Mother's Day tonight so we don't hit crowds tomorrow. My parents, my bf and I are hitting a Brazilian steakhouse. Haven't been to this one though. I'm trying to starve myself so I have room lol.

### `chatter`

**Definition:** The post doesn't report a meal. It's a reaction, a question, a compliment or off-topic.

**Example 1:**

> Why do I open threads like this while starving to death at lunch time?:unsure:

**Example 2:**

> bachelor experimental cooking? looks nice nonetheless. what did you have as a side dish?

### The hardest boundary

**Which two labels:** home_cooked vs bought_prepared

**The decision rule I used every time:**

If the post mentions that the user eats/uses specific brand of ingredients, frozen food or prepackaged items, it's 'home_cooked' even when user bought them. Only meals that were prepared by an establishment can be considered 'bought_prepared'.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** The posts came from 'forumsforums.com' and the thread is 'What's for Dinner Tonight?'. I specifically pulled the posts from page 157-164. Because `home_cooked` came out above 70%, I also added only the `bought_prepared` and `chatter` posts from pages 165-167 to balance it.

**How I labelled them:** Pre-labelled with AI (Claude), using my definitions and my decision rule. Every row's `note` column says so. I then read every post and corrected the labels I disagreed with. I changed 6 of the 238 labels, all between `home_cooked` and `chatter`.

**Counts per label:**

| Label             | Count | Share |
| ----------------- | ----- | ----- |
| `home_cooked`     | 161   | 67.6% |
| `chatter`         | 49    | 20.6% |
| `bought_prepared` | 28    | 11.8% |
| **Total**         | 238   | 100%  |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**

> _The post:_ "Chicken breasts and a salad made of broccoli/cauliflower-chopped walnuts and bleu cheese dressing. The chicken was the Tyson fully cooked fillets, and they tasted great right out of the oven."
>
> _Could have been:_ `home_cooked` or `bought_prepared`
>
> _I chose, because:_ `home_cooked`. Tyson fillets are a packaged product heated at home, not a meal from a business.

**2.**

> _The post:_ "Last night we went to Asian Grill buffet. I had peanut butter chicken, shrimp, fried fish, fried rice, crab rangoon. Tonight sonny boy is going to grill."
>
> _Could have been:_ `bought_prepared` or `home_cooked`
>
> _I chose, because:_ `bought_prepared`. The meal the post actually reports is the buffet; the grilling hasn't happened yet.

**3.**

> _The post:_ "We got quesadillas from a place called Hawk's and I made my romaine and walnut salad."
>
> _Could have been:_ `bought_prepared` or `home_cooked`
>
> _I chose, because:_ `bought_prepared`. The main dish came from a restaurant; the homemade salad is only a side.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, 128 tokens per post, seed 42.

**Anything I changed from the defaults, and why:** Nothing. I kept the defaults.

**Split sizes:** train 166 / val 36 / test 36.

| Label             | Train | Val | Test |
| ----------------- | ----- | --- | ---- |
| `home_cooked`     | 112   | 25  | 24   |
| `chatter`         | 34    | 7   | 8    |
| `bought_prepared` | 20    | 4   | 4    |

`bought_prepared` has only 4 posts in the test split, so its F1 will move a lot between seeds. `chatter` is right at 8.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- _What I asked for:_ I asked Claude to collect 238 posts from the forum, and pre-label 218 of them for me.
- _What came back:_ It populated the rest of `labels.csv` and specified which posts were labeled by it and whether I had rechecked the labels or not.
- _What I changed:_ I read through the 238 posts and fixed 6 of the labels that it got wrong.

**Moment 2**

- _What I asked for:_ I have asked Claude to challenge my criteria and analyze whether they make sense.
- _What came back:_ Based on the analysis, my last two criteria were weak and overlapped with each other. Claude suggested ways to enhance them, and it also gave me ideas for a new criterion.
- _What I changed:_ I made criterion 4 a concrete number: at least 24 of the 30 staff posts (80%), allowing about one miss in five. I replaced criterion 5 with a confidence check: predictions at 0.80 confidence or higher must be right at least 85% of the time.

**Pre-labelling disclosure:** 218 posts in `labels.csv` were pre-labelled by Claude. I read every one and corrected the ones I disagreed with (6 changed).

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure                | Baseline | Trained | Difference |
| ---------------------- | -------- | ------- | ---------- |
| Overall accuracy       | 0.667    | 0.667   | +0.000     |
| Macro F1               | 0.460    | 0.267   | -0.194     |
| F1 — `bought_prepared` | 0.250    | 0.000   | -0.250     |
| F1 — `chatter`         | 0.308    | 0.000   | -0.308     |
| F1 — `home_cooked`     | 0.824    | 0.800   | -0.024     |

**What I predicted before I looked:** I predicted that the trained model will produce similar results to the zero-shot model because the dataset is skewed to home_cook. Therefore, I think the trained model will end up predicting home_cook for the majority of the posts.

<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:** My baseline model matched with the trained model for the overall accuracy. However, the trained model did worse in the macro f1, and the f1 for each of the labels. This means that my fine-tuning didn't help. 

The trained model predicted home_cooked for all 36 posts, and 24 of 36 are home_cooked, so it scored 0.667 without telling any posts apart. The baseline got the same accuracy while actually catching a few chatter and bought_prepared posts.

My prediction was slightly off but it was correct that the trained model didn't do better. 

<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
| --------- | ------ | ------- | ------ | --------- | ------- |
| 1. Overall accuracy | ≥ 0.70 | 0.667 | 0.667 | 0.667 | MISSED |
| 2. Per-label F1 | `home_cooked`, `chatter` ≥ 0.65; `bought_prepared` ≥ 0.55 | hc 0.80, ch 0.00, bp 0.00 | hc 0.80, ch 0.00, bp 0.00 | hc 0.80, ch 0.00, bp 0.00 | MISSED |
| 3. `bought_prepared` predicted as `home_cooked` | < 20% | 100% (4/4) | 100% (4/4) | 100% (4/4) | MISSED |
| 4. Agreement with staff labels | ≥ 24/30 | 23/30 | 23/30 | 23/30 | MISSED |
| 5. Most-confident third is correct (revised, see `criteria.md`) | ≥ 0.85 | 1.000 | 0.917 | 0.833 | MISSED |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted | home_cooked | bought_prepared | chatter |
|---|---|---|---|
| **home_cooked** | 24 | 0 | 0 |
| **bought_prepared** | 4 | 0 | 0 |
| **chatter** | 8 | 0 | 0 |

**My biggest off-diagonal number, and what it means:**

My biggest off-diagonal number is 8: all 8 real chatter posts were predicted as home_cooked, and 0 went the other way. But this isn't the model confusing two labels. It never predicted chatter or bought_prepared at all, and all 4 bought_prepared posts also went to home_cooked. 

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| #   | Criterion | Target | Verdict | How I decided |
| --- | --------- | ------ | ------- | ------------- |
| 1   | Overall accuracy | ≥ 0.70 | MISSED | 0.667 on all three seeds, below 0.70 every time. |
| 2   | Per-label F1 | `home_cooked`, `chatter` ≥ 0.65; `bought_prepared` ≥ 0.55 | MISSED | `chatter` and `bought_prepared` were 0.00 on all three seeds. |
| 3   | `bought_prepared` predicted as `home_cooked` | < 20% | MISSED | 4 of 4 (100%) on all three seeds. |
| 4   | Agreement with staff labels | ≥ 24/30 | MISSED | `agreement.py` gave 23/30; this doesn't depend on the seed. |
| 5   | Most-confident third is correct (revised) | ≥ 0.85 | MISSED | 1.000 and 0.917 passed, but seed 2024 was 0.833. |

**Diagnoses**

**1. Overall accuracy — too few examples for two labels.** The model predicted `home_cooked` for all 36 test posts on every seed, and 24 of 36 are `home_cooked`, so 0.667 is just the score for always guessing the biggest label. I know it's a data problem and not an impossible task because the zero-shot baseline, with no training at all, still caught some `chatter` and `bought_prepared` posts (F1 0.31 and 0.25).

**2. Per-label F1 — too few examples, plus a boundary I applied inconsistently.** `bought_prepared` had only 20 training posts and `chatter` 34, against 112 for `home_cooked`, so the model never learned to predict either. My agreement report also shows I label by how a post sounds rather than what it contains (5 of my 7 disagreements), and all 6 corrections I made to my own labels were on `home_cooked` / `chatter`, so my `chatter` labels are probably noisy too.

**3. `bought_prepared` → `home_cooked` — too few examples, on a genuinely hard pair.** With 20 training posts, the model never predicted `bought_prepared` once, so all 4 test posts went to `home_cooked`. This is also my hardest boundary by design, since store-bought and frozen food count as `home_cooked`, but the model never got far enough to show whether that rule was learnable; the imbalance hides it.

**4. Agreement with staff — a labelling consistency problem.** All 5 of my pile (a) disagreements are the same mistake: I labelled from the post's final opinion and skipped the numbers that supported it. That is a problem with how I apply a written rule, not with the model, and it's direct evidence that my own 238 labels may not be fully consistent.

**5. Confidence — the confidence only measures "how `home_cooked` does this look".** Since every prediction is `home_cooked`, the most-confident third is just the posts that look most like `home_cooked`, and on seed 2024 two of those 12 were actually `chatter` or `bought_prepared`. That fits the overlap in diagnosis 2, where many `chatter` posts still talk about food, so confidence can't mean much until the model predicts more than one label.

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

**Agreement rate:** **23 / 30 = 77**%

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

> _The post:_ I tracked sleep against session quality for eleven weeks. Under seven hours the next day's intervals were 6-8 seconds per mile slower, every time bar one. Small sample, one runner, but it's consistent enough that I've stopped scheduling hard days after late shifts.
>
> _Staff said / I said:_ analysis / reaction
>
> _My call, and why:_ Staff is right. Eleven weeks of tracked sleep and pace numbers carry the conclusion, and rule 3 says personal data is still `analysis`; I read it as a feeling about sleep and missed the facts.
>
> _Which pile:_ a

**2.**

> _The post:_ Everyone blames the hill at 18 miles. I split the field into people who ran the first half under 1:45 and over, and the slowdown after 18 was almost identical for both groups. If the hill were the problem the fast starters would fall apart harder.
>
> _Staff said / I said:_ analysis / hot_take
>
> _My call, and why:_ Staff is right. I judged only the final sentence, but the split at 1:45 and the matching slowdown are the reason for it, so rule 1 makes it `analysis`.
>
> _Which pile:_ a

**3.**

> _The post:_ Carbon plates aren't the whole story. My times dropped 2% in the shoes and another 2% the following year in the same pair, when the only thing that changed was going from 35 to 50 miles a week.
>
> _Staff said / I said:_ analysis / reaction
>
> _My call, and why:_ Staff is right. The two 2% drops with mileage as the only change are the argument, and an estimate from the writer's own running is still a checkable fact.
>
> _Which pile:_ a

**4.**

> _The post:_ Ran the same loop in 4C and in 24C within a fortnight, same effort by heart rate. 90 seconds slower in the heat over six miles. People keep saying heat costs you 'a bit' and it's more than that.
>
> _Staff said / I said:_ analysis / hot_take
>
> _My call, and why:_ Staff is right. The same loop at 4C and 24C with a 90-second gap is what the last sentence rests on, and again I labelled from the conclusion instead of the evidence.
>
> _Which pile:_ a

**5.**

> _The post:_ Three of us ran the same session on the same day, different shoes, within 10 seconds of each other over 5 miles. I'm not saying shoes do nothing, but whatever they do was smaller than the noise between three people.
>
> _Staff said / I said:_ analysis / reaction
>
> _My call, and why:_ Staff is right. The post is written right after a run, but the three-runner, 10-second comparison is what the conclusion rests on, and rule 3 says recency does not outrank evidence.
>
> _Which pile:_ a

**6.**

> _The post:_ Track sessions are wasted on anyone running slower than 8 minute miles. You'd get more out of just running more.
>
> _Staff said / I said:_ hot_take / reaction
>
> _My call, and why:_ I read it as a reply to a slower runner earlier in the thread, which makes it a response to something that just happened. The definitions don't say whether a reply to another post counts as `reaction` when it's phrased as a general rule.
>
> _Which pile:_ c

**7.**

> _The post:_ Every injury thread on this forum is the same injury and the same three replies.
>
> _Staff said / I said:_ hot_take / reaction
>
> _My call, and why:_ I'm right, or at least defensible. It is a fed-up response to the thread the writer just read, and it gives no evidence, so it's about the writer's experience rather than a claim they're trying to argue; staff themselves flagged it as borderline.
>
> _Which pile:_ c

**What the pattern in my disagreements tells me:**

Five of my seven disagreements are the same mistake: staff said analysis and I said reaction or hot_take, because I labelled from the post's final opinion and skipped the numbers that carried it (rules 1 and 3). That's pile (a), so it's a consistency problem in how I apply a rule, not a gap in the rules. It suggests my own labels may be judged by tone rather than content too, especially on the home_cooked / chatter boundary, where all 6 of my corrections were.

---

## The Improvement

**What I changed:** I added 44 more `bought_prepared` posts from pages 168–200 of the same thread, taking that label from 28 to 72 posts.

**Which diagnosis pointed at it:** Diagnoses 1–3, too few examples. `bought_prepared` had only 20 training posts against 112 for `home_cooked`, and its F1 was 0.00 on all three seeds, while the zero-shot baseline still caught some (0.25), so the label was learnable and the model just hadn't seen enough of it.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
| --------- | ------ | ------- | ------ | --------- | ------- |
| 1. Overall accuracy | ≥ 0.70 | 0.581 | 0.581 | 0.581 | MISSED |
| 2. Per-label F1 | `home_cooked`, `chatter` ≥ 0.65; `bought_prepared` ≥ 0.55 | hc 0.74, ch 0.00, bp 0.00 | hc 0.74, ch 0.00, bp 0.00 | hc 0.74, ch 0.00, bp 0.00 | MISSED |
| 3. `bought_prepared` predicted as `home_cooked` | < 20% | 100% (11/11) | 100% (11/11) | 100% (11/11) | MISSED |
| 4. Agreement with staff labels | ≥ 24/30 | 23/30 | 23/30 | 23/30 | MISSED |
| 5. Most-confident third is correct (revised, see `criteria.md`) | ≥ 0.85 | 0.857 | 1.000 | 0.786 | MISSED |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->

No. All five criteria are still missed, and the model still predicts `home_cooked` for all 43 test posts on every seed (11 of 11 `bought_prepared` posts went to `home_cooked`). Accuracy dropped from 0.667 to 0.581, but that's not the model getting worse: `home_cooked` is now 25 of 43 test posts, so 0.581 is just the new score for always guessing it.

So too few examples wasn't the whole story. `bought_prepared` now has more training posts than `chatter` had before, and the model still never predicted it once. The data is still lopsided (`home_cooked` is 57%), and with 3 epochs at the default learning rate on about 200 training posts, the model never got past guessing the biggest label. The scores are identical across all three seeds, which shows the model is stuck rather than unstable. Criterion 4 didn't change because it measures my labelling, not the model.

---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

**The gap between what I meant my labels to capture and what the model
learned:**

<!-- Two sentences. Your confusion matrix is the evidence. -->

<!-- ═════════════════════════════════════════════════════════════════════

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
