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



---

## Label Taxonomy

<!-- Milestone 2. Reddit blocked every automated fetch, so none of the posts below
     was independently retrieved by Claude. Titles and descriptions come from my
     own reading and are used as supplied. Exact post bodies are not quoted. -->

Exactly three labels, with no catch-all. Labels apply to the **post itself**
(title, body, caption, image), not to its comments. A factual headline or a
quotation is not `analysis` unless the post uses a checkable fact to support its
own main claim, and an unsupported factual statement is not a `hot_take` unless
the post actually expresses a judgment or prediction.

### `analysis`

**Definition:** A claim supported by specific, checkable evidence that meaningfully supports it.

**Include when:** the evidence is load-bearing, meaning the claim would be weaker without it; the evidence is something a reader could check, such as a score, a percentage, a date or a named match event.

**Exclude when:** the post only reports a fact, headline, event or quotation with no argument built on it; the fact is decoration on a conclusion the writer had already reached; the "evidence" is general reasoning with nothing checkable in it.

**Example 1:** INCOMPLETE. No candidate so far.

**Example 2:** INCOMPLETE. No candidate so far.

### `hot_take`

**Definition:** A confident judgment or prediction without meaningful supporting evidence.

**Include when:** the post expresses a judgment (a ranking, an evaluation of a player, an interpretation of someone's behavior) or a prediction; it offers only opinion-style reasoning, or a fact that does not actually support the conclusion; a statistic is present but only as a flourish.

**Exclude when:** checkable evidence carries the argument (`analysis`); the post is mainly an emotional or humorous response (`reaction`); the post states a fact or news item without expressing any judgment or prediction. An unsupported statement of fact is not a `hot_take` by itself, and a question is not a judgment until the post answers it.

**Example 1** (accepted, weak; wording from my screenshot and description, not independently verified): "Medvedev mocking Novak Injury?" with a 7-second video and Discussion flair, https://www.reddit.com/r/tennis/s/uaK8wcmHuo. The title puts an interpretation of Medvedev's behavior (mockery) to readers with nothing behind it except the clip, so it reads as an unsupported judgment. It is weak because it is phrased as a question and has no body text.

**Example 2** (accepted, weak; same caveat): the image of a Djokovic quote saying women's tennis is the most successful and best-paid women's sport globally, https://www.reddit.com/r/tennis/s/gCUUfQKWNE. It is a broad, confident claim with no figure or event supporting it. It is weak because I do not know whether the poster endorsed the quote or only shared it as news, and it is also my borderline case below.

These two were accepted at my own decision to have two examples, and both are borderline. A clearer `hot_take` (a poster's own confident claim or prediction, stated as a statement) would be a better example and should replace either one if I find it.

### `reaction`

**Definition:** A post that primarily expresses emotion or humor in the poster's own words or caption.

**Include when:** the title, caption or text conveys feeling, celebration, admiration, nostalgia or humor about a match, result, player or moment; any score or statistic is only the thing being reacted to.

**Exclude when:** the post turns the moment into a general claim about players or tennis (`hot_take`); it argues from evidence to a conclusion (`analysis`); it only reports or shows something and the poster's own words convey no feeling or humor.

**Example 1** (accepted on the caption as I supplied it; not independently verified): "This season in a nutshell" with a tennis meme, https://www.reddit.com/r/tennis/s/Cm9WcwcqHA. The caption and meme are a humorous response with no argument.

**Example 2** (accepted on the title and screenshot I supplied; not independently verified): "What an amazing answer to an amazing question" with a clapping emoji, a Novak Djokovic press-conference video, Big 3 flair, https://www.reddit.com/r/tennis/s/TytAry3m8l (link confirmed by me against my screenshot). The poster's own title expresses admiration for one specific moment and makes no argument. It is not a `hot_take` because it does not rank or predict anything general; it is a response to this answer.

### Priority rule

Apply in order and stop at the first yes:

1. Does specific, checkable evidence directly support the post's main claim? -> `analysis`.
2. Otherwise, does the post express a judgment or prediction? -> `hot_take`. This includes posts with a statistic that is only decoration, and posts that are heated but make a general claim.
3. Otherwise, does the poster's own title, caption or text primarily express emotion or humor? -> `reaction`.
4. If none of the three is a clean yes, exclude the post (next section). Do not force a label.

If two of the first three are still defensible, pick the one the post spends most of its words on.

### Posts outside the collection scope (excluded, not labelled)

My scope is posts that make a claim, take a stance or express a response. A post that only reports an event, announces news, or shows a photo with no expressive caption fails all three definitions. It is excluded from the dataset instead of being forced into a label, and there is no fourth label for it.

- **Reporting a fact is not analyzing it.** A post that says what happened ("player X got a warning", "event Y was postponed") is excluded unless it also argues something from specific evidence.
- **Excluded posts are logged.** I keep a short list of what I skipped and why, so the Dataset section can say how I sampled. If I find myself excluding most of what I read, the scope is too narrow and I should rethink it, not relabel the exclusions.
- **Two-label ambiguity is different.** A post that genuinely fits two of the three labels is resolved by the priority rule above, not excluded.

### Posts reviewed so far (all provisional, none independently retrieved)

| ID | Post (as supplied by me) | Link | Handling | Why |
|---|---|---|---|---|
| R1 | Title: "Medvedev mocking Novak Injury?" (Discussion flair, a 7-second video, no body text visible in my screenshot) | https://www.reddit.com/r/tennis/s/uaK8wcmHuo | `hot_take` (accepted, weak) | The title is a leading question and the screenshot shows a video with no body text. I first excluded it because a question is not a stated judgment. I later accepted it as a weak `hot_take` because the title asserts the mockery reading with no evidence beyond a 7-second clip |
| R2 | "Dame ya la puta toalla": Jodar receives an unsportsmanlike-conduct warning after cursing at a ball kid | https://www.reddit.com/r/tennis/s/pBdqI4gxKG | **Excluded** | Reports an event. It is not `analysis` because it argues nothing from the event. Re-open only if the body adds a judgment or evidence-backed argument |
| R3 | "ROGER (AGAIN) DOING ROGER THINGS" with Thierry Henry | https://www.reddit.com/r/tennis/s/lNnkYvsXE6 | Provisional `reaction` | The caption is playful and admiring. Not settled until I confirm the caption is the poster's own and the post has no argument |
| R4 | "This season in a nutshell" with a tennis meme | https://www.reddit.com/r/tennis/s/Cm9WcwcqHA | `reaction` (accepted) | Humorous response in the poster's caption, no argument |
| R5 | Picture of 12-year-old Carlos Alcaraz watching Roland Garros beneath the Eiffel Tower | https://www.reddit.com/r/tennis/s/WmBgCC61B0 | **Excluded** (for now) | I do not have a caption that expresses emotion, judgment or argument. Re-open if the original caption does |
| R6 | Title: "Six Kings Slam postponed" (News flair; the post is an image of the organizer's announcement moving the event from October 21-24 to December 27-30, 2026, with no poster caption visible in my screenshot) | https://www.reddit.com/r/tennis/s/IM1JiFutGH | **Excluded** | The image is a press release. It gives dates, but its only reason is "organizational and logistical challenges", which is not specific, checkable evidence, and no argument is built on it. Reporting a fact is not analyzing it |
| R7 | Headline: Nishikori retires as the most successful Asian male tennis player in history | https://www.reddit.com/r/tennis/s/x4mKkBVu7o | **Excluded** | News headline, no argument and no judgment of its own |
| R8 | Photo of Maja Chwalinska with the Wuhan trophy, her first WTA title | https://www.reddit.com/r/tennis/s/ih7361Xm8N | **Excluded** | Achievement photo with no expressive text as supplied |
| R9 | Image of a Djokovic quote saying women's tennis is the most successful and best-paid women's sport globally | https://www.reddit.com/r/tennis/s/gCUUfQKWNE | `hot_take` (accepted, weak), borderline with `analysis` | See the hardest boundary below. Accepted without knowing whether the poster endorsed the quote |
| R10 | Swiatek explains she did not want to retire so Chwalinska could win normally | https://www.reddit.com/r/tennis/s/vngwGkmCpo | **Excluded** | Report of a player's explanation, with no argument or response from the poster |
| R11 | Title: "Alcaraz celebrating Ruud's win" (Meme flair; a short clip of Alcaraz celebrating loudly on the Laver Cup bench; no body text visible in my screenshot). I have no link for this one yet | link needed | Provisional `reaction`, **unsettled** | The humor and emotion are in the clip and the Meme flair, but the title only describes what is shown. It counts only if I decide that the poster's own flair and clip satisfy "poster's own words or caption". Weaker than R4, whose caption itself carries the joke |
| R12 | Title: "Wu Yibing comments on Zverev's sportsmanship" (ATP flair; an image of an article quoting Wu comparing his matches against Alcaraz and Zverev). I have no link for this one yet | link needed | **Excluded**, borderline with `analysis` | The post is a reposted quote and the poster adds no claim of their own. Wu's quote does name specific incidents (falling down, complaining about the lights, touching the net tape) behind his view that the match was not a "pure" competition, but that argument is Wu's, not the poster's. Same rule as the Djokovic quote: a quotation repeating someone else's argument is not the poster's evidence-backed claim. Re-open only if the poster endorses or argues it in a body or caption |
| R13 | Title: "What an amazing answer to an amazing question" plus a clapping emoji (Big 3 flair; a Djokovic press-conference video; no body text visible in my screenshot) | https://www.reddit.com/r/tennis/s/TytAry3m8l (confirmed) | `reaction` (accepted) | The poster's own title expresses admiration for a specific moment. It makes no general claim and gives no evidence. Closest alternative is `hot_take` ("amazing" is an evaluation), but by the priority rule an enthusiastic response to one moment is `reaction`, while a ranking or general claim would be `hot_take` |
| R14 | Title: "How is the Men's Tennis GOAT debate even a debate now?" with an image of a Djokovic/Nadal/Federer major-record comparison table. Posted in **r/billsimmons**, about 2 years ago, not r/tennis | https://www.reddit.com/r/billsimmons/s/pA1w2Xzk1o | **Excluded** (wrong community) | Outside my community, so it cannot be an example for this project however well it fits. On content alone it would likely be `analysis`: the title makes a claim and the table of Grand Slams, weeks at No. 1 and head-to-heads is the load-bearing evidence. Useful only as a model of the kind of r/tennis post to look for |

**Accepted examples per label (needs 2 each):**

| Label | Accepted | Provisional (not counted) | Gap |
|---|---|---|---|
| `analysis` | 0 | none | Need 2 |
| `hot_take` | 2, both weak (R1, R9) | none | None, but a clearer example is worth finding |
| `reaction` | 2 (R4, R13) | R3, R11 | None (complete) |

### The hardest boundary

**Which two labels:** `analysis` vs `hot_take`.

**Real borderline post:** R9, https://www.reddit.com/r/tennis/s/gCUUfQKWNE, an image of a Djokovic quote saying women's tennis is the most successful and best-paid women's sport globally (described by me; wording not independently verified).

**Case for `analysis`:** the quote makes a claim that sounds factual and checkable (revenue, pay and success compared across women's sports), so a reader could verify it.

**Case for `hot_take`:** the post states a broad, confident judgment, and no specific figure, comparison or event is given to back it. The claim itself is the fact, and nothing is offered as support.

**The decision rule I used every time:** Checkable evidence must meaningfully support the post's main claim for the post to qualify as `analysis`. A claim that merely sounds factual, or a quotation repeating someone else's claim, is not support. Without supporting evidence, a confident general claim is `hot_take`.

**Applied to R9:** no supporting evidence is shown, so it would be `hot_take`. Caveat: if the post's own title or text adds a figure, this should be re-checked, and if the post only presents the quote as news without endorsing it, it should be excluded instead.

### Stress test

Eight fictional posts are in `data/stress_test_posts.md`. They are not real Reddit posts and are kept separate from the examples above. They test factual reporting, evidence-backed analysis, unsupported opinions, and emotional or humorous reactions. My classifications go in `data/stress_test_responses.csv`.

### The 70% limit

The notebook's dataset check stops training if any single label is more than 70% of the data, which means at most 140 of 200 posts. With no catch-all, the risk is that one label, most likely `hot_take` or `reaction`, grows because it is easy to find. I will count each label as I collect and sample on purpose for the thin ones. The check itself is unchanged.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->

**Counts per label:**

| Label | Count | Share |
|---|---|---|
|  |  |  |
|  |  |  |
|  |  |  |
| **Total** |  | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**2.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

**3.**
> *The post:*
>
> *Could have been:*
>
> *I chose, because:*

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

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
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

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
