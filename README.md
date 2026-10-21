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

>It's a subway night here. We did grocery shopping tonight. Did regular shopping plus got all the fixings for the Thanksgiving day dinner. So no cooking here tonight.

**Example 2:**

>We're celebrating Mother's Day tonight so we don't hit crowds tomorrow. My parents, my bf and I are hitting a Brazilian steakhouse. Haven't been to this one though. I'm trying to starve myself so I have room lol.

### `chatter`

**Definition:** The post doesn't report a meal. It's a reaction, a question, a compliment or off-topic.

**Example 1:** 

>Why do I open threads like this while starving to death at lunch time?:unsure:

**Example 2:**

>bachelor experimental cooking? looks nice nonetheless. what did you have as a side dish?

### The hardest boundary

**Which two labels:** home_cooked vs bought_prepared

**The decision rule I used every time:**

<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->
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

- _What I asked for:_
- _What came back:_
- _What I changed:_

**Moment 2**

- _What I asked for:_
- _What came back:_
- _What I changed:_

**Pre-labelling disclosure:** All 238 posts in `labels.csv` were pre-labelled by Claude. I read every one and corrected the ones I disagreed with (6 changed).

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure          | Baseline | Trained | Difference |
| ---------------- | -------- | ------- | ---------- |
| Overall accuracy |          |         |            |
| Macro F1         |          |         |            |
| F1 — `label_one` |          |         |            |
| F1 — `label_two` |          |         |            |

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
| --------- | ------ | ------- | ------ | --------- | ------- |
| 1.        |        |         |        |           |         |
| 2.        |        |         |        |           |         |
| 3.        |        |         |        |           |         |
| 4.        |        |         |        |           |         |
| 5.        |        |         |        |           |         |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |     |     |     |
| ---------------- | --- | --- | --- |
| \*\*\*\*         |     |     |     |
| \*\*\*\*         |     |     |     |
| \*\*\*\*         |     |     |     |

**My biggest off-diagonal number, and what it means:**

<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| #   | Criterion | Target | Verdict | How I decided |
| --- | --------- | ------ | ------- | ------------- |
| 1   |           |        |         |               |
| 2   |           |        |         |               |
| 3   |           |        |         |               |
| 4   |           |        |         |               |
| 5   |           |        |         |               |

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

**Agreement rate:** **_ / 30 = _**%

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

> _The post:_
>
> _Staff said / I said:_
>
> _My call, and why:_
>
> _Which pile:_

**2.**

> _The post:_
>
> _Staff said / I said:_
>
> _My call, and why:_
>
> _Which pile:_

**What the pattern in my disagreements tells me:**

---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
| --------- | ------ | ------- | ------ | --------- | ------- |
| 1.        |        |         |        |           |         |
| 2.        |        |         |        |           |         |
| 3.        |        |         |        |           |         |
| 4.        |        |         |        |           |         |
| 5.        |        |         |        |           |         |

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
