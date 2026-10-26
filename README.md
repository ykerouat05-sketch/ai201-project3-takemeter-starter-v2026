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

TakeMeter is a classifier for public tennis-discussion posts. It sorts each post into one of three labels: `analysis` (a claim backed by specific, checkable evidence), `hot_take` (a confident judgment or prediction without meaningful support) or `reaction` (an emotional or humorous response). The community is the Tennis Warehouse (Talk Tennis) message board; the 200 posts in `labels.csv` all come from it. The model is `distilbert-base-uncased` fine-tuned on those posts. Training has been run on CPU with three seeds (42, 7 and 2024); the baseline comparison is done, and the diagnosis is still pending.


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

**Example 1** (a dataset post, not a Reddit one; its label is Claude Code's and is not yet checked by me): "What do you make of Elena Rybakina's poor record in finals?", https://tt.tennis-warehouse.com/index.php?threads/what-do-you-make-of-elena-rybakinas-poor-record-in-finals.747487/. The post lists the finals records of 16 active major winners (Raducanu 1-0 down to Rybakina 4-9, 30.8%) to support the claim that she has the worst record among them. The figures are checkable and carry the argument. It ends with a question, but the claim is clear from the table.

**Example 2** (same caveat): "Djokovic-Federer rivalry since 2012 YEC", https://tt.tennis-warehouse.com/index.php?threads/djokovic-federer-rivalry-since-2012-yec.710111/. The poster lists 13 named matches, "Djokovic has beaten Federer in 12 of the 13 biggest matches they played", and concludes "That's brutal." The list is the evidence for the claim.

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

**Exclusion log (dataset batches, Tennis Warehouse forum threads):**

| Post | URL | Why it was excluded |
|---|---|---|
| "Is this sandbagging or fair game?" | https://tt.tennis-warehouse.com/index.php?threads/is-this-sandbagging-or-fair-game.805059/ | A request for advice about whether to enter a tournament. It makes no claim, takes no stance and expresses no emotion or humor, so it fits none of the three labels. |
| "Tennis Warehouse playtest: Solinco Chroma prototype" | https://tt.tennis-warehouse.com/index.php?threads/tennis-warehouse-playtest-solinco-chroma-prototype.804637/ | An announcement of a racquet playtest program, with dates and rules. It reports information and argues nothing, so it fits none of the three labels. |
| "My life as a UTR 3" | https://tt.tennis-warehouse.com/index.php?threads/my-life-as-a-utr-3.805372/ | Rejected at my review. A joking request for cramp advice, not a clear fit for any label. |
| "Tsitsipas blacked out racket!" | https://tt.tennis-warehouse.com/index.php?threads/tsitsipas-blacked-out-racket.781874/ | Rejected at my review. A short observation with a guess about the brand, too thin to count as a judgment. |
| "Zverev vs Medvedev - Who is now the greatest of their generation?" (audit row 66) | https://tt.tennis-warehouse.com/index.php?threads/zverev-vs-medvedev-who-is-now-the-greatest-of-their-generation.800564/ | Audit exclusion: Full text lists title counts, finals and weeks at #1 for two players and ends 'Who is greater for you?'. The poster states no claim of their own. |
| "By Clinching the Prestigious YE1, where does Zverev stand compared to Murray, Roddick, Safin, Hewitt and Stanimal ?" (audit row 84) | https://tt.tennis-warehouse.com/index.php?threads/by-clinching-the-prestigious-ye1-where-does-zverev-stand-compared-to-murray-roddick-safin-hewitt-and-stanimal.805138/ | Audit exclusion: Full text is a vote request that ranks five other players but never says where Zverev stands. No claim about the question asked. |
| "Who has a superior peak between over35 years old Djokovic, Sinner and Alcaraz?" (audit row 88) | https://tt.tennis-warehouse.com/index.php?threads/who-has-a-superior-peak-between-over35-years-old-djokovic-sinner-and-alcaraz.805173/ | Audit exclusion: Full text gives three stat lines and asks which peak is superior. No conclusion. |
| "Has Zverev now surpassed Wawrinka all-time?" (audit row 153) | https://tt.tennis-warehouse.com/index.php?threads/has-zverev-now-surpassed-wawrinka-all-time.804053/ | Audit exclusion: Full text is a two-player stat table and the question 'Has Zverev now surpassed Wawrinka?'. No answer or claim. |
| "Djokovic in his 20s or in his 30s - which was greater?" (audit row 157) | https://tt.tennis-warehouse.com/index.php?threads/djokovic-in-his-20s-or-in-his-30s-which-was-greater.804909/ | Audit exclusion: Full text is a 20s-versus-30s stat table and the question 'which was greater?'. No answer or claim. |
| "During the Big 3 slam years, which surface had the longest periods of the best players playing on their clearly favoured surface. Hard or Clay?" (audit row 191) | https://tt.tennis-warehouse.com/index.php?threads/during-the-big-3-slam-years-which-surface-had-the-longest-periods-of-the-best-players-playing-on-their-clearly-favoured-surface-hard-or-clay.804563/ | Audit exclusion: Full text gives a method and a table, then 'I'll leave you to draw your own conclusions'. No claim. |

Four other posts were dropped from a batch for practical reasons, not because of their content: three had only a title and no body text, and one (Stanford Men's Tennis) had a URL already used by an existing dataset row. They are not exclusions on scope grounds.

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
| `analysis` | 2 (dataset posts above) | none | None, but both labels are Claude Code's and not yet checked by me |
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

The notebook's dataset check stops training if any single label is more than 70% of the data, which means at most 140 of 200 posts. With no catch-all, the risk is that one label, most likely `hot_take` or `reaction`, grows because it is easy to find. I counted each label as I collected and sampled on purpose for the thin ones. In the final 200 posts the largest label is `hot_take` at 39.0%. The check itself is unchanged.

---

## The Dataset

**Where the posts came from:** real, public tennis discussions from the Tennis Warehouse (Talk Tennis) message board, `tt.tennis-warehouse.com`. All 200 posts in `labels.csv` come from that one source. No Reddit post is in the dataset: automated requests to Reddit were refused (for example a `403` on its JSON feed), and I did not try to get around that. (The Reddit links in the Label Taxonomy section belong to the Milestone 2 examples.) Every post's URL is in `data/post_sources.csv`, in the same row order as `labels.csv`, and no URL appears twice.

- **First 20 posts (mine, `cold`).** I chose and pasted these myself.
- **Other 180 posts (collected with Claude Code).** A script read public forum listings and each thread's opening post, at about one request per second, with no login. It covered General Pro Player Discussion, Former Pro Player Talk, College Tennis Talk, Adult League & Tournament Talk, Odds & Ends, Tennis Tips/Instruction, Pros' Racquets and Gear, and Health & Fitness. That gave 1309 candidate threads, and I did not use anything I could not read. Each row's `text` is the thread title followed by the opening post's text, with whitespace collapsed. Posts are not edited, shortened or paraphrased. In the later label audit, all 180 of these threads were re-fetched and each saved text matched the live opening post exactly. The six replacement posts added during the audit were checked the same way.
- **Where the 180 came from (after the audit):**
  - General Pro Player Discussion: 122
  - Former Pro Player Talk: 25
  - College Tennis Talk: 15
  - Adult League & Tournament Talk: 13
  - Pros' Racquets and Gear: 3
  - Tennis Tips/Instruction: 1
  - Health & Fitness: 1

**How I labelled them:**

- **First 20: cold, by me.** I labelled these myself without AI suggestions. They are marked `cold` and have not been edited since.
- **Other 180: labelled by Claude Code, not yet checked by me.** Claude Code read each post against `label_definitions.txt` and chose a label. I have **not** checked these 180 labels row by row, although I made the audit decisions listed below. Their note is `pre-labelled, unchecked`. When I review a row and agree, I change its note to `pre-labelled, checked`, and if I change the label I will note that. Until then, treat these labels as AI suggestions. This is a disclosure, not a claim of human review.
- **What was left out.** Of the 1309 candidate threads, Claude Code reviewed the opening of most of them: roughly the first 200-420 characters of each, and it skipped very short ones on the first pass. The last 130 or so candidates from the College Tennis Talk pages were collected but never read. Reviewed threads that were questions, polls, ranking games, news and video links, equipment questions, advice requests, forum chit-chat or non-tennis threads were left out, since they make no claim and show no emotion (the exclude-don't-force rule). Claude Code did not keep a per-post log of every skipped thread, so the exclusion log above lists only the individually reviewed cases. Because the labels were chosen from the opening of each post, a long post could have changed direction later. 250 candidates were labelled and 180 were used. The other 70 were dropped for length (a few were over 5,000 characters), for weak or link-only text, or to keep the label mix balanced. No label was changed in order to balance the data.
- **Label audit (`data/label_audit.csv`).** Claude Code re-read the full text of all 180 rows and recommended 14 label changes and 10 exclusion reviews. The report keeps its original recommendations in `data/label_audit_proposals_original.csv`, and the dataset as it stood before the audit is in `data/labels_200_before_audit.csv`. I then decided: 8 labels changed (rows 36, 61, 95, 122, 156, 175 and 182 to `hot_take`; row 115 to `analysis`); 6 changes kept as they were for now, marked borderline (rows 65, 83, 106, 135, 181, 187); 6 rows excluded because the full text states no claim (rows 66, 84, 88, 153, 157, 191, listed in the exclusion log above); and 4 rows kept after full-text review as borderline (rows 51, 105, 189, 195). Six replacement posts, all public, with their full text, URL and uniqueness checked, keep the total at 200. Row numbers in the audit are the numbers in the 200-row file before the audit; the audit also gives each row's number in the current `labels.csv`.
- **Borderline posts.** 47 of the 180 were flagged borderline when labelled; after the audit, 10 rows are marked "reviewed, borderline" and the rest of the 47 were reviewed without a change. The priority rule in the Label Taxonomy section decides ties: checkable evidence that meaningfully supports the main claim makes it `analysis`; a judgment or prediction without such support makes it `hot_take`; an emotional or playful response makes it `reaction`.

**How the split works:** I do not split the data. `labels.csv` stays as one file, and the notebook's `split_dataset` makes the stratified 70/15/15 split, redrawn for each seed (42, 7, 2024). The seed-42 split that the notebook wrote to `test_split.csv` has 139 train, 31 validation and 30 test rows, with no text shared between splits; the test split has 11 `analysis`, 11 `hot_take` and 8 `reaction` rows. (An earlier version of this paragraph said 10, 12 and 8. That came from a check of mine that stripped the stray leading spaces from the labels of the 20 `cold` rows before splitting. The notebook does not strip them before splitting, so its split differs. The numbers here are from the notebook's own `test_split.csv`.)

**Counts per label (200 posts):**

| Label | Count | Share |
|---|---|---|
| `analysis` | 69 | 34.5% |
| `hot_take` | 78 | 39.0% |
| `reaction` | 53 | 26.5% |
| **Total** | 200 | 100% |

No label is above 70% (the assignment limit) or above 50% (my criterion 3), so the balance requirement is met. `reaction` is the smallest class, so its test split has only 8 rows and its F1 will probably vary a lot between seeds. The cold 20 are 4 `analysis`, 9 `hot_take` and 7 `reaction`. Before the audit the counts were 76, 73 and 51.

**Validation (run on the final files):** `python validate_labels.py` reports 200 labelled rows, 20 `cold`, and no problems. A separate check found 200 rows in `labels.csv` and 200 in `data/post_sources.csv`, every row with exactly the right number of columns, no empty fields, each source preview matching the start of its row's text, and no duplicate URLs or near-duplicate texts. The audit report's final labels and source URLs also match `labels.csv` row by row, and the 20 `cold` rows are byte-for-byte unchanged from before the audit. The validator checks the format and balance, not whether a label is right.

**Four hard cases** (all are real rows in `labels.csv`; labels are Claude Code's, pending my review, and the audit left all four unchanged)

**1. Six Kings Slam: exhibition or real event?** https://tt.tennis-warehouse.com/index.php?threads/should-we-consider-the-six-kings-slam-an-exhibition-event-or-an-alternative-pro-circuit-event.792256/
> *The post:* "considering the HUGE money incentive to win it, I think it should be seen as a professional invitational event... there's an actual betting market for it, which generally isn't the case for 'true' exhibition events." It then guesses that the organizers play it down as an exhibition so the ATP won't block it.
>
> *Could have been:* `hot_take` or `analysis`.
>
> *Labelled `analysis`, because:* two checkable facts (the prize money and the betting market) do support the main claim. The guess about the organizers' motives is unsupported, so it is borderline.

**2. "Are wins that tier 2s of the past got against the big 3 overrated?"** https://tt.tennis-warehouse.com/index.php?threads/due-to-nostalgia-bias-are-wins-that-tier-2s-of-the-past-got-against-the-big-3-sometimes-overrated-compared-to-current-players.805277/
> *The post:* Argues that Berdych's three Slam wins over Djokovic and Nadal came against "2010/2017 Djokovic and 2015 Nadal", which is "the same tier if not lower than Shelton beating Alcaraz at the USO".
>
> *Could have been:* `analysis` or `hot_take`.
>
> *Labelled `hot_take`, because:* it names specific matches, but the only thing that links them to the claim is the poster's own ranking of how good those opponents were, which is a judgment and not checkable evidence.

**3. "Does this graph prove Djokovic has the highest peak?"** https://tt.tennis-warehouse.com/index.php?threads/does-this-graph-prove-djokovic-has-the-highest-peak.805344/
> *The post:* Cites 2011-12 numbers (Slams: Djokovic 4, Nadal 2, Federer 1, Murray 1; big titles: Djokovic 13, Federer 6; year-end #1: Djokovic 2, others 0), then asks whether that proves his peak was the highest ever.
>
> *Could have been:* `analysis`, or excluded because the closing line is a question.
>
> *Labelled `analysis`, because:* the figures are specific and checkable and they support the claim of a dominant 2011-12. The question is the claim restated, so I did not exclude it. Whether they prove "highest ever" is a bigger claim than the figures support.

**4. "A tennis version of the fairy tale 'The Emperor's New Clothes'"** https://tt.tennis-warehouse.com/index.php?threads/a-tennis-version-of-the-fairy-tale-the-emperors-new-clothes.805373/
> *The post:* Retells the fairy tale and pastes a news story of a boy telling Bublik he is better than Sinner "because you do not use doping".
>
> *Could have been:* `reaction` or `hot_take`.
>
> *Labelled `reaction`, because:* the poster's own words are a playful parable, with no argument. It also insinuates that Sinner doped, so it could be read as a `hot_take`. I need to check this one.

**Pending suggestions from the earlier batch (not in `labels.csv`):** these two stay out until I approve them.
- *Race to Turin 2026* (https://tt.tennis-warehouse.com/index.php?threads/race-to-turin-2026.802727/). Recommendation: `hot_take`. The 390-point gap is checkable but only shows the race is open. The picks and "Medvdev will fall off, he's in terrible form" are predictions with no evidence.
- *Alcaraz/Sinner vs. Federer* (https://tt.tennis-warehouse.com/index.php?threads/would-alcaraz-sinner-really-be-able-to-challenge-this-federer.805323/). Recommendation: `hot_take`. The body asserts two judgments (Federer's level and the effect of racquet technology) with no results behind them.

**Limitations and remaining work**

- **I have not checked 180 of the 200 labels.** Claude Code applied the definitions and re-read every row in the audit, but the labels are still its judgments. 10 rows are marked borderline (65, 83, 106, 135, 181, 187, 51, 105, 189, 195, by their numbers in the audit), and several more depend on reading sarcasm or humor (24, 156, 182). I should review them, and change a row's note to `pre-labelled, checked` only after I have. Reviewing could shift the counts.
- **One source.** Every post is from one forum, and many of the 180 are about the Big 3, GOAT debates and 2026 results, so the model may learn forum-specific habits and may not transfer to other sites.
- **Evidence-backed posts are a big class.** Many `analysis` posts are lists of statistics. A model may learn "has numbers means analysis", which is only partly what the definition means.
- **Labelling the posts partly from the title.** Each text starts with the thread title. Some titles are questions, which are not judgments by themselves.
- **Whitespace in the `cold` rows' labels (unresolved).** The 20 `cold` rows were entered with a space after each comma, so their labels are stored as, for example, ` hot_take`, and their notes as ` cold`. I left `labels.csv` as it is because those rows must stay unchanged. Training and `baseline.py` strip labels before using them, so the training run and its metrics are not affected. But the notebook's `split_dataset` stratifies on the raw value, so it uses six label groups instead of three: the 20 `cold` rows are always split 14 train, 3 validation and 3 test, and the seed-42 test split is 11/11/8 where a normalized split would give 10/12/8. The notebook's dataset check also prints six label rows and warns "Only 4 examples of ' analysis'". Three rows of `test_split.csv` carry the leading space. The saved seed-42 split and `results.json` are kept as they are, and I have not decided whether to normalize in the notebook before the three-seed run. Doing so would change every split and require retraining.
- **Three training runs so far, no diagnosis.** I trained with seed 42 on CPU in Milestone 5, then with seeds 42, 7 and 2024 in Milestone 2 (see Run Log — Before). The baseline comparison (Milestone 1) is below. The diagnosis and any improvement are still pending.

---

## The Training Run

**Base model:** `distilbert-base-uncased`, fine-tuned for three-label classification. This is the notebook's `BASE_MODEL` as committed; `results.json` does not record it.

**Settings:** seed 42, 3 epochs, learning rate 2e-5, batch size 16 (the notebook's committed values; only the seed and max length are recorded in `results.json`), and a maximum of 128 tokens per post (`max_length` in `results.json`; longer posts are truncated).

**Device:** CPU (`device: cpu`, `device_name: CPU`, torch `2.14.1+cpu`, as recorded in `results.json`). The notebook warns that results can differ on other hardware even with the same seed. I have not tested that, so these numbers describe this CPU run only.

**Anything I changed from the defaults, and why:** nothing. `takemeter.ipynb` is unchanged from the version in the repository's history, so these are the notebook's defaults.

**Split sizes:** 139 train, 31 validation, 30 test (70/15/15, stratified, seed 42). Test split by label: 11 `analysis`, 11 `hot_take`, 8 `reaction`. `reaction` has the fewest examples in test, so its scores will vary a lot between seeds. (The stratification caveat for the `cold` rows is listed under Limitations above.)

**Results (one run: seed 42, 30 test posts):** accuracy 0.567 (17 of 30) and macro F1 0.440. Per-class F1: `analysis` 0.750 (n=11), `hot_take` 0.571 (n=11), `reaction` 0.000 (n=8). `results.json` also records precision, recall, a confusion matrix and confidence figures. These come from one seed and a 30-post test set, so they show what this run scored and are not evidence that the model is reliable. The other two seeds, the confusion matrix and the baseline comparison are further down. I have not diagnosed these numbers.

**Saved artifacts:** `results.json` (valid JSON, metrics consistent with its confusion matrix) and `test_split.csv` (the 30 held-out posts; it matches the notebook's own seed-42 split of `labels.csv`) were written by the notebook and committed in `2cc7427`, "Save Takemeter training results". `labels.csv` was committed earlier in `9db4e45`.


---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1: the label audit (Milestone 4)**

- *What I asked for:* After Claude Code had collected 180 posts and given them labels from the first few hundred characters of each, I asked it to re-read the full saved text of every one against `label_definitions.txt`, and to propose label changes and exclusions without editing the dataset.
- *What came back:* A report (`data/label_audit.csv`) with a recommendation for each row: keep 156, change 14, send 10 to exclusion review, with a confidence level and a reason. It also re-fetched every source thread and found each saved text matched the live post.
- *What I changed:* I accepted 8 of the 14 changes, told it to keep 6 for now (marked borderline), excluded 6 of the 10 review candidates after it confirmed from the full text that they state no claim, and had it add 6 verified replacement posts so the dataset stayed at 200. I did not accept the remaining labels as checked.

**Moment 2: how many labels there should be (Milestone 2)**

- *What I asked for:* Whether a fourth catch-all label (`other`) would work for posts that fit none of my three labels, and what it would affect.
- *What came back:* An explanation of the implications of a fourth label for the definitions, the label list and the balance checks.
- *What I changed:* I decided against a catch-all. I kept exactly three labels, wrote an exclusion rule so a post that fits none is left out and not forced into a label, and updated the README and `label_definitions.txt` to match.

**Pre-labelling disclosure:** The first 20 labels in `labels.csv` (note `cold`) are mine and were made without AI suggestions. The other 180 posts were collected and pre-labelled by Claude Code. I have **not** reviewed those 180 labels one by one, so each carries the note `pre-labelled, unchecked`, and I will change a note to `pre-labelled, checked` only for rows I have reviewed myself. The audit above is Claude Code's second pass over its own labels, plus my decisions on its recommendations. It is not an independent human check of every row. Two earlier suggestions (Race to Turin 2026, and Alcaraz/Sinner vs. Federer) were never added to `labels.csv`.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

> **Status (unit 6).** Milestone 1 (the baseline) and Milestone 2 (three seeds and the confusion matrix) have been run: see Baseline vs. Trained and Run Log — Before below. The verdicts and diagnoses, the agreement report and the improvement are still pending. The Baseline vs. Trained comparison uses the seed-42 run only.

## Baseline vs. Trained

**The baseline.** `baseline.py` runs `facebook/bart-large-mnli`, a general zero-shot model, with no training. For each post it scores each label's text in the hypothesis "This post is {}." and picks the highest. It runs locally on CPU and needs no API key. It scored the same 30 held-out posts as the trained model: it read them from `test_split.csv` (no fallback to a random sample), and I checked that its 30 posts and gold labels match that file in the same order. The label texts it used are my definitions from this README, copied word for word into `label_definitions.txt`. Results are in `baseline_results.json`.

| Measure | Baseline (my definitions) | Trained (seed 42) | Difference |
|---|---|---|---|
| Overall accuracy | 0.367 | 0.567 | +0.200 |
| Macro F1 | 0.361 | 0.440 | +0.079 |
| F1 — `analysis` | 0.444 | 0.750 | +0.306 |
| F1 — `hot_take` | 0.429 | 0.571 | +0.143 |
| F1 — `reaction` | 0.211 | 0.000 | -0.211 |

These are the actual outputs of `python baseline.py --trained results.json`. I recomputed accuracy and every per-label F1 from the saved predictions and they match. The test set is 30 posts (11 `analysis`, 11 `hot_take`, 8 `reaction`) and all labels are scored against the dataset labels as they stand, 180 of which are not yet checked by me. A difference of one post is 0.033 in accuracy.

**Bare label names, kept separate.** I also ran the baseline without my definitions (`python baseline.py --definitions does_not_exist.txt --out baseline_results_bare_labels.json`), so the model sees only the words `analysis`, `hot_take` and `reaction`. Results are in `baseline_results_bare_labels.json`, and they are not mixed with the table above.

| Measure | Baseline (bare names) | Baseline (my definitions) | Trained (seed 42) |
|---|---|---|---|
| Overall accuracy | 0.400 | 0.367 | 0.567 |
| Macro F1 | 0.356 | 0.361 | 0.440 |
| F1 — `analysis` | 0.154 | 0.444 | 0.750 |
| F1 — `hot_take` | 0.485 | 0.429 | 0.571 |
| F1 — `reaction` | 0.429 | 0.211 | 0.000 |

**What was run:** `python baseline.py`, `python baseline.py --trained results.json`, and the bare-names command above, all on CPU with the committed `test_split.csv`. An earlier `baseline_results.json` was already in the folder when I started this step. Re-running `python baseline.py` produced a byte-identical file, so that earlier run reproduced exactly on this machine.

**What I predicted before I looked:**

> I predict that the fine-tuned model will outperform the general-model baseline on analysis posts because training should help it recognize evidence-based claims, although it may still struggle to distinguish hot takes from reactions.

*Timing, stated plainly:* this prediction was written into the README on 26 October 2026, immediately before I re-ran the baseline commands in this step. It is not a strict pre-registration. The trained model's results (`results.json`, committed in `2cc7427`) already existed when I wrote it, and a `baseline_results.json` from an earlier run was already in the working folder when this step began (see "Results" below).

**What the gap actually means:**

On these 30 posts the fine-tuned model scored higher than the zero-shot baseline overall (accuracy 0.567 against 0.367, macro F1 0.440 against 0.361), so the training added something here. The gain is concentrated in `analysis` (F1 0.750 against 0.444), as I predicted, and `hot_take` improved too. It is not better on every label: the trained model's `reaction` F1 is 0.000 against the baseline's 0.211. Its confusion matrix in `results.json` shows why: 7 of the 8 `reaction` posts were called `hot_take` and none was called `reaction`. That fits the second half of my prediction, that hot takes and reactions would be hard to tell apart, though this is one seed and 8 reaction posts, so I am not drawing a firm conclusion. I have not yet diagnosed the cause.

The definitions did not clearly help the baseline. With them it scored 0.367 accuracy and 0.361 macro F1, and with bare label names 0.400 and 0.356, a one-post difference in accuracy. What changed was which label the model favored: with my definitions it called 16 of 30 posts `analysis` and 3 `hot_take`; with bare names it called 22 of 30 `hot_take` and 2 `analysis`. So on this test set my definitions shifted the baseline's errors and did not improve it overall. That is one run on 30 posts and not enough to say how much the definitions are worth.

These comparisons use the seed-42 run and its test split only. The three seeds in Run Log — Before give similar overall accuracy, but I did not re-score the baseline on the seed 7 and seed 2024 test sets, so I am not claiming the gap is stable or that the trained model is reliable. I have not checked whether these numbers would be the same on other hardware.



---

## Run Log — Before

**Milestone 2: three seeds, `RUN_LABEL = "before"`.** All three seeds completed: 42, 7 and 2024. The notebook's section 6 was run as one execution, and its results are saved in `results_three_seeds_before.json`. **Device:** CPU (`device: cpu`, torch `2.14.1+cpu`, `max_length` 128, as recorded in the file for each seed). Settings are the notebook's unchanged defaults: `distilbert-base-uncased`, 3 epochs, learning rate 2e-5, batch size 16. Each seed has its own 70/15/15 split of the same 200 posts, with 30 test posts (11 `analysis`, 11 `hot_take`, 8 `reaction`). Total training time for the three seeds was about 4.5 minutes. `results.json`, `test_split.csv` and the baseline files were not changed; I checked their checksums before and after. The seed-42 entry matches the Milestone 5 `results.json` exactly (accuracy, F1, confusion matrix and confidence figures).

*How it was run:* I ran the notebook's own code cells from a script (sections 1 to 3, the function definitions in sections 4 and 5, then sections 6 and 7) and did not save anything into `takemeter.ipynb`. The cells that train and write `results.json` and `test_split.csv` in sections 4 and 5 were cut off before those lines, so Milestone 5's files could not be overwritten. A first attempt failed at start-up, before any training, because my script looked up the notebook's cells wrongly; it produced no results and I fixed the script and re-ran.

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.567 | 0.533 | 0.533 | 0.033 |
| Macro F1 | 0.440 | 0.410 | 0.468 | 0.058 |
| F1 — `analysis` | 0.750 | 0.692 | 0.720 | 0.058 |
| F1 — `hot_take` | 0.571 | 0.538 | 0.462 | 0.110 |
| F1 — `reaction` | 0.000 | 0.000 | 0.222 | 0.222 |

Precision and recall per label, from the same file (each cell is precision / recall):

| Label | Seed 42 | Seed 7 | Seed 2024 |
|---|---|---|---|
| `analysis` | 0.692 / 0.818 | 0.600 / 0.818 | 0.643 / 0.818 |
| `hot_take` | 0.471 / 0.727 | 0.467 / 0.636 | 0.400 / 0.545 |
| `reaction` | 0.000 / 0.000 | 0.000 / 0.000 | 1.000 / 0.125 |

**Accuracy range:** 0.567 − 0.533 = 0.033, which is under the 0.10 point at which the assignment calls a dataset too small or lopsided for a stable measure, so I am not flagging the overall accuracy as unstable. The per-label scores move much more: `hot_take` F1 by 0.110 and `reaction` F1 by 0.222 (0.000, 0.000 and 0.222). That is what you would expect from test splits where `reaction` has only 8 posts and each post is worth 0.125 of that label's recall. The notebook prints "Stable enough to trust" for the accuracy spread, but I do not read it that way: three runs on a 200-post dataset, scored on 30-post test sets that draw on the same 200 posts, cannot show statistical significance or that the model is reliable. They show that overall accuracy was similar on these three splits, and that `reaction` is barely learned in any of them.

*Limits of this run:* the labels are mostly unchecked (180 of 200); this is CPU only, so I have not checked other hardware; the whitespace problem in the `cold` rows' labels (see Limitations above) affects how the notebook stratifies every seed and makes its section-3 preview table count only 27 of the 30 seed-42 test posts (it prints 10/10/7, because three padded labels are not counted; the real split is 11/11/8); and no diagnosis has been done.

Targets and verdicts for the five criteria are not filled in yet; they belong to the next milestone.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

Printed by the notebook's section 7. Rows are the true labels and columns are the model's guesses, in the notebook's label order. **This matrix is seed 42 only:** the notebook's section 7 reads the first entry of the three-seed results, which is seed 42. It is the same matrix as in `results.json` from Milestone 5, and I checked that. It is not an average of the seeds.

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 9 | 2 | 0 |
| **hot_take** | 3 | 8 | 0 |
| **reaction** | 1 | 7 | 0 |

**My biggest off-diagonal number, and what it means:** 7 real `reaction` posts were called `hot_take`, and no real `hot_take` post was called `reaction`, so the model has not learned the `reaction` / `hot_take` boundary and falls back to `hot_take` for posts I labelled as reactions. The other seeds point the same way: the largest error in each is `reaction` called `hot_take` (6 of 8 in seed 7, 7 of 8 in seed 2024; seed 2024 got 1 `reaction` post right). Adding the three test sets together gives 20 of 24 `reaction` posts called `hot_take`, but those test sets overlap, so I treat that only as a rough pointer. I have not yet diagnosed whether this is a labelling problem, too few `reaction` examples, or a genuinely hard pair. The notebook's remark that such a lopsided direction "usually points at your labelling" is a prompt for that diagnosis, not a finding.



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

       [x] criteria.md has five numbered criteria, each naming a NUMBER
       [x] Each has a reason underneath tied to your data or taxonomy
       [x] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [x] No label above 70%
       [ ] All five unit 5 sections have real content
       [x] Label Taxonomy includes the decision rule for your hardest boundary
       [x] The Dataset includes four hard cases (the assignment minimum is three)
       [x] results.json and test_split.csv committed (the notebook does this)
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
