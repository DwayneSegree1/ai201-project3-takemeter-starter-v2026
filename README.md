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

TakeMeter sorts comments from **r/nba**, Reddit's main NBA community, by how
substantial they are. Each comment is labelled `analysis` (a conclusion
backed by a specific, checkable fact), `hot_take` (a confident general claim
with no support), or `reaction` (an in-the-moment response to a play, a
game, the broadcast, or news that just broke). The classifier is a
fine-tuned `distilbert-base-uncased`, trained on 201 labelled r/nba
comments. The goal is to separate argued posts from asserted ones and live
chatter, which r/nba's game and news threads mix together constantly.


---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** States a conclusion and backs it with a specific, checkable
fact, such as a stat, an age, an injury, a contract or rule detail, or a
named comparison. The fact has to be the reason for the conclusion, not a
restatement of it, and it has to be broad enough to support it.

**Example 1:**
> Wade has a sprained ankle so he is going to miss a bit. Barlow is also hurt
> and will miss at least first couple of weeks. They have no bigs right now.

**Example 2:**
> Most regular people can actually. Because of the differences polymarket,
> kalshi, and all other prediction markets are by default allowed in all US
> states, compared to normal sportsbooks which only operate in states which
> have legalized them. As well as the age limit being different, where
> prediction markets require users only to be 18, not the 21 that most
> sportsbooks require. That's a lot of regular people that have completely
> different access to this kind of thing

### `hot_take`

**Definition:** A confident claim about players, teams, or the league that
would still be a claim after tonight's game is over, with no checkable
support offered.

**Example 1:**
> Celtics shouldve kept Brown

**Example 2:**
> All I see here is that NBA teams have too much fucking money.

### `reaction`

**Definition:** The writer's immediate response to something that just
happened, such as a play, tonight's game, the broadcast, or news that just
broke, and it describes what they're seeing or feeling rather than claiming
anything beyond it.

**Example 1:**
> I've got dateline and not the game…. This sucks

**Example 2:**
> Kind of fun having basketball playing in the background while I work

### The hardest boundary

**Which two labels:** `reaction` and `hot_take`. Game threads are full of
comments written mid-play that slide from "what I just saw" into "what this
player is."

> Paul George doesn't know how to pass.

That was posted live, one quarter into the season, probably right after a
turnover. It reads like a reaction, but it claims something about Paul George
in general.

**The decision rule I used every time:**
Ask whether the claim would still be a claim once tonight's game ends. If it
is limited to tonight, this play, or the writer's own experience ("Turnover
machine Cs", "I've got dateline and not the game"), it's `reaction`. If it
says something about a player, team, or the league in general ("doesn't know
how to pass", "is looking fantastic this year"), it's `hot_take`, however
fresh or heated it is. So the Paul George post is `hot_take`.

**Secondary rules** (tightened after the stress test below):

- *`analysis` vs `hot_take`: the fact has to carry the main claim.*
  - A number that's part of the prediction isn't evidence. "NBA will start
    the project, see it going bankrupt in 3 years" is `hot_take`.
  - **Scale:** one game's stat can't carry a claim about a whole player or
    season. "Ant dropped 40 last night… he's top 3" is `hot_take`.
  - **Main claim:** if the fact supports only a side point, label by the
    headline claim. "Knicks are winning the East… Brunson took a discount" is
    `hot_take`.
- *`reaction` vs `analysis`: a fact from tonight's game counts only if it
  supports a stated conclusion that goes beyond the fact.*
  - "Wemby has 6 blocks… they've completely given up on the paint" is
    `analysis`: the blocks are the reason for a claim about behaviour.
  - "Luka is the best player in the world tonight… 45 on a bad ankle" is
    `reaction`: the 45 just restates the praise.
  - A rhetorical question or outburst isn't a conclusion. "…third time this
    quarter. Who is coaching this defense??" is `reaction`.
- *Sarcasm:* label the claim it implies. "Glad to see the refs are still
  getting their Lakers checks" claims the refs favour the Lakers in general,
  so it's `hot_take`.
- *Accuracy doesn't matter:* "checkable" means you could look it up, not
  that it's correct.

**Not labelled, so dropped at collection:** comments that can't be understood
without their parent ("Hot air balloon"), questions and tech support ("Does
anyone have a solution for YoutubeTV…"), and off-topic or political trash
talk.


---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** r/nba's public RSS feeds
(reddit.com/r/nba), collected on 2026-10-20, the opening day of the 2026-27
season. A short, rate-limited script pulled 1,619 unique comments from 19
threads: the opening game thread, the Daily Discussion, and news threads
about contracts, injuries, broadcasting deals and trades. 395 candidates were
read. Comments were dropped if they couldn't be understood without their
parent, were questions, jokes, off-topic, insults or political, or were
shorter than 8 words. To meet criterion 1, 10 more fact-heavy comments were
added for `analysis`, and 9 `hot_take` rows were removed at random.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->

**No cold check was done.** None of the 201 rows were labelled by me unaided.
Twenty rows were set aside as the cold check, but I asked Claude to label
them instead. They're marked
`pre-labelled by Claude (cold slot: no unaided labelling done)` in the note
column, and no row in `labels.csv` is marked `cold`. That means next unit's
agreement report has no unaided baseline of mine to compare against.

All 201 rows were pre-labelled by Claude (Claude Code) using the definitions
and decision rules in the Label Taxonomy section above. The note column
records this on every row.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `analysis` | 55 | 27% |
| `hot_take` | 75 | 37% |
| `reaction` | 71 | 35% |
| **Total** | 201 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

These were flagged `HARD CASE` in the note column. Each label follows the
rules in the Label Taxonomy section.

**1.**
> *The post:* "Same old nba...hucking 3s after 3s after 3s. A shoulder brush
> is a foul...garbage" (posted live in the opening game thread)
>
> *Could have been:* `reaction` or `hot_take`
>
> *Chose `hot_take`, because:* it was written mid-game, but "same old nba" is
> a claim about the league, not about tonight. It would still be a claim
> after the game ends, and that's the test for the hardest boundary.

**2.**
> *The post:* "It's $25 million a year. That's really nothing in today's NBA."
>
> *Could have been:* `analysis` or `hot_take`
>
> *Chose `hot_take`, because:* the number is the thing being judged, not
> evidence for the judgement. A comment in the same thread said Braun "will
> be the 86th highest paid player in the league". That one is `analysis`,
> because the ranking is the reason $25M isn't much.

**3.**
> *The post:* "Let's go. In Georgia you have to pay like $40 a month for
> Bally sports to watch the Hawks. It's destroyed the fan base. Being able
> to watch on prime will be awesome. Definitely going to be tuning in more"
>
> *Could have been:* `reaction` or `analysis`
>
> *Chose `reaction`, because:* the $40 does back a claim ("destroyed the fan
> base"), but that's a side point. The post is mostly the writer's
> excitement about news that just broke. Under the main-claim rule, it's
> labelled by its headline.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, max length 128
tokens, seed 42. Training took about 24 seconds.

**Hardware:** MacBook Pro 16" 2019 (MacBookPro16,1), Intel Core i9-9880H, AMD
Radeon Pro 5500M 4 GB, macOS 26.6.2. Trained on `mps`, with torch 2.2.2 and
Python 3.12.14.

> `results.json` records `device_name` as "Apple Silicon GPU (MPS)". That's
> the notebook's fixed label for any `mps` device and it's wrong for this
> machine. On an Intel Mac, `mps` runs on the AMD Radeon Pro 5500M through
> Metal. I've left the generated file unedited. Keep this in mind when
> comparing numbers with people on Apple Silicon, CUDA or CPU.

**Anything I changed from the defaults, and why:** Nothing. All settings are
the notebook defaults.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->

train 139 · val 31 · test 31 (stratified 70/15/15, seed 42)

| Label | Train | Val | Test |
|---|---|---|---|
| `analysis` | 38 | 9 | 8 |
| `hot_take` | 52 | 11 | 12 |
| `reaction` | 49 | 11 | 11 |

`analysis` has only 8 test posts, right at the "about 8" line, so each
post moves its F1 by about 0.1. Expect it to be the jumpiest number across
next unit's seeds. It's my smallest label because checkable-fact posts were
only about 1 in 10 of what I read.


---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

I used Claude (Claude Code) throughout this unit: to read the community,
draft the taxonomy and criteria, collect and pre-label the posts, and run the
notebook.

**Moment 1: stress-testing the labels**

- *What I asked for:* A separate AI agent was given only my three draft
  definitions. It was asked to write eight r/nba-style comments that sit on
  the boundaries between labels.
- *What came back:* Eight posts. Only 2 of the 8 could be classified cleanly.
  "Ant dropped 40 last night… he's a top 3 player" passed my "delete the
  fact" test even though one game can't prove that. "Luka is the best player
  in the world tonight… 45 on a bad ankle" had a stat that only restated the
  praise.
- *What I changed:* The decision rules were tightened before any labelling.
  The new rules are scale (one game's stat can't support a claim about a
  whole player), main claim (a fact backing a side point doesn't count), and
  restatement (a stat from tonight that just repeats the feeling is
  `reaction`). Rhetorical questions and sarcasm got their own rules. All of
  these are in the Label Taxonomy section.

**Moment 2: the `reaction` definition while pre-labelling**

- *What I asked for:* Pre-label the collected comments under the tightened
  definitions.
- *What came back:* Many comments in news threads didn't fit any label. They
  were plain responses to news that had just broken, like "Genuinely
  hilarious situation" and "Really awesome letter. Nothing quite like a
  mom's love". The `reaction` definition only covered live games and the
  broadcast.
- *What I changed:* `reaction` was widened to "something that just happened,
  such as a play, tonight's game, the broadcast, or news that just broke".
  The change was made in the README and in `label_definitions.txt`, so the
  baseline is judged on the same wording.

**Pre-labelling disclosure:**

**Every one of the 201 rows in `labels.csv` was pre-labelled by Claude.**
That includes the 20 rows meant for the unaided cold check, which I asked
Claude to label instead. **No cold check was done** (see The Dataset). The
note column marks every row as pre-labelled. I haven't yet reviewed or
corrected these labels, and the unit 5 results were trained on them as they
are. Claude also wrote the collection script, the taxonomy
and criteria drafts, and ran notebook sections 1–5.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

Baseline: `facebook/bart-large-mnli`, zero-shot, prompted with my
definitions from `label_definitions.txt`. It was scored on the same 31
held-out posts in `test_split.csv` as the trained model (`held_out: true`).

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.258 | 0.613 | +0.355 |
| Macro F1 | 0.137 | 0.482 | +0.346 |
| F1 — `analysis` | 0.410 | 0.000 | -0.410 |
| F1 — `hot_take` | 0.000 | 0.647 | +0.647 |
| F1 — `reaction` | 0.000 | 0.800 | +0.800 |

**The same baseline with bare label names**, no definitions
(`baseline_results_bare_names.json`, same 31 posts):

| Measure | Bare names | Definitions | What the definitions were worth |
|---|---|---|---|
| Overall accuracy | 0.613 | 0.258 | **-0.355** |
| Macro F1 | 0.531 | 0.137 | -0.394 |
| F1 — `analysis` | 0.222 | 0.410 | +0.188 |
| F1 — `hot_take` | 0.621 | 0.000 | -0.621 |
| F1 — `reaction` | 0.750 | 0.000 | -0.750 |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

The trained model will beat the baseline on `reaction`, where game-thread
posts share surface cues ("lol", NBC/Peacock, "tonight") that fine-tuning
picks up easily. It will lose to the baseline on `analysis`, where a
definition that says "checkable fact" gives the zero-shot model a better
handle than 38 training examples did.

> Written and committed before running `baseline.py`. Honest caveat: the unit
> 5 run had already printed the trained model's per-label F1 (including 0.00
> for `analysis`), so this predicts the baseline's side, not the trained
> model's.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

**The +0.355 is mostly the baseline breaking, not the trained model being
good.** With my definitions, the zero-shot model predicted `analysis` for
all 31 posts. Its 0.258 accuracy is just the share of `analysis` in the test
split (8 of 31).

The likely cause is how `baseline.py` builds the prompt: `"This post is {}."`
with the definition dropped in. My definitions start with verbs, so the model
was scoring sentences like "This post is states a conclusion backed by a
specific, checkable fact…". The longest, most detailed definition then wins
every time. So my definitions were worth **-0.355** accuracy, measured. They
were written for a human labeller, not for a sentence template.

**Against the fairer opponent, fine-tuning added nothing on accuracy.** The
bare-names baseline scores 0.613, exactly what the trained model scores. Its
macro F1 is higher (0.531 vs 0.482), because it gets some `analysis` right
and the trained model gets none. The trained model is only slightly better
on `hot_take` (+0.026) and `reaction` (+0.050), which is one or two posts on
a test split this size.

**The prediction held on both counts.** The trained model beat both
baselines on `reaction` and lost to both on `analysis`. `analysis` is the
problem: the trained model never predicts it.



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

Section 6, `RUN_LABEL = "before"`, written to
`results_three_seeds_before.json`. Default settings (3 epochs, lr 2e-5,
batch 16, max length 128), trained on `mps` (AMD Radeon Pro 5500M via Metal,
torch 2.2.2). Seed 42 reproduced the unit 5 run exactly (0.613).

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Balance | each label 25–40% of 201 | 27 / 37 / 35% | (same file) | (same file) | MET |
| 2. Accuracy | ≥ 0.65 on all seeds | 0.613 | 0.387 | 0.710 | MISSED |
| 3. Per-label F1 | every label ≥ 0.55 on all seeds | min 0.000 (`analysis`) | min 0.000 (`analysis`) | min 0.571 (`hot_take`) | MISSED |
| 4. Self-consistency | ≥ 85% on 30 cold re-labels | not measured | not measured | not measured | MISSED |
| 5. Confidence gap | most-sure third ≥ least-sure third + 0.20 | 0.80 vs 0.40 (+0.40) | 0.70 vs 0.30 (+0.40) | 0.90 vs 0.50 (+0.40) | MET |

**The full spread, as section 6 printed it:**

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.613 | 0.387 | 0.710 | 0.323 |
| Macro F1 | 0.482 | 0.260 | 0.704 | 0.444 |
| F1 — `analysis` | 0.000 | 0.000 | 0.706 | 0.706 |
| F1 — `hot_take` | 0.647 | 0.513 | 0.571 | 0.134 |
| F1 — `reaction` | 0.800 | 0.267 | 0.833 | 0.567 |

**The spread is a diagnosis.** Accuracy moved **0.323** across three seeds,
more than three times the ten-point line. The cause shows in the training
log: final training loss was 1.03, 1.06 and 1.02, barely below the 1.10
(ln 3) a model guessing uniformly across three labels would score. With
139 training posts, 3 epochs and batch 16, the model gets about 27
optimiser steps, and it hasn't learned much in that time. Seeds 42 and 7
collapsed towards the biggest label: they predicted `hot_take` for 22 and
27 of 31 posts, and `analysis` for none. Seed 2024 was the only one that
broke out (it predicted `analysis` 9 times and got 6 right). So the
0.39–0.71 range shows how undertrained the model is, more than how good it
is.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

Seed 42 (section 7 prints the first seed):

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 0 | 8 | 0 |
| **hot_take** | 0 | 11 | 1 |
| **reaction** | 0 | 3 | 8 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

All 8 real `analysis` posts were called `hot_take`, and no post went the
other way, so the model hasn't learned the `analysis`/`hot_take` boundary
at all. It treats "confident claim about the NBA" as `hot_take` whether or
not a checkable fact backs it.

That's the direction you'd expect from an undertrained model that falls
back on the biggest label. Section 7 says a lopsided direction "usually
points at your labelling", so the agreement report should test that too.
For this pair, though, the training loss suggests the model never got far
enough to learn the fact-versus-no-fact cue.


---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Balance | each label 25–40% of the 201 posts | MET | 55 / 75 / 71 = 27% / 37% / 35% in `labels.csv`. It's a property of the file, so it's the same for every seed |
| 2 | Overall accuracy | ≥ 0.65 on all three seeds | MISSED | 0.613, 0.387, 0.710. Only seed 2024 clears it |
| 3 | Per-label F1 | every label ≥ 0.55 on all three seeds | MISSED | Seeds 42 and 7: `analysis` 0.000. Seed 7: `hot_take` 0.513, `reaction` 0.267. Only seed 2024 clears it (lowest 0.571) |
| 4 | Self-consistency | ≥ 85% agreement on 30 cold re-labels | MISSED | Never measured: no unaided labelling or re-labelling was done, so there's no number to hold against the target |
| 5 | Confidence gap | most-sure third ≥ least-sure third + 0.20, all seeds | MET | +0.40 on every seed (0.80/0.40, 0.70/0.30, 0.90/0.50) |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**Misses 2 and 3 (accuracy and per-label F1): the model is undertrained, so
in this setup it can't learn the task from 139 training posts. This is a
model problem, not a labelling problem.**

- **What the numbers show.** `analysis` F1 is 0.000 on seeds 42 and 7
  because those models never predict `analysis` at all: precision and
  recall are both 0 across 8 test posts. On the same seeds, `hot_take`
  recall is high (0.92, 0.83) and its precision is low (0.50, 0.37). The
  models are defaulting to the biggest label. Seed 42 predicted `hot_take`
  for 22 of 31 posts, and seed 7 for 27.
- **Why it isn't a hard pair.** The seed-42 matrix sends all 8 `analysis`
  posts to `hot_take`, and 0 go the other way. A genuinely hard pair gets
  confused in both directions. A one-way flow into the majority label is
  what a model that hasn't learned anything yet looks like.
- **Why it's undertraining.** Final training loss was 1.03, 1.06 and 1.02
  across the seeds. ln 3 = 1.10 is what you get by guessing uniformly over
  three labels, so the model barely moved off its starting point. With 139
  posts, 3 epochs and batch 16, it gets about 27 optimiser steps.
- **Why the task is still reachable.** When seed 2024 did break out, it got
  `analysis` to P 0.67 / R 0.75 / F1 0.71. The zero-shot baseline with bare
  label names also picks out some `analysis` (F1 0.22) with no training at
  all. A fact-versus-no-fact signal is there to learn.
- **Why it isn't my labelling (the agreement report).** The same labeller
  produced both the 201 training labels and the 30 staff-set labels:
  Claude, using rules that copy the staff structure (a load-bearing fact,
  then "about the writer vs about the world", then "recency doesn't outrank
  evidence"). On the staff set, that labeller matched staff 30/30, with
  nothing in pile (a). That included all 4 `analysis`/`hot_take` posts
  staff flagged as hard (sp_005, sp_010, sp_014, sp_020). If the
  `analysis`/`hot_take` boundary had been applied two ways in training, the
  same rule on that pair is where it should have shown up. It didn't.
- **The limits of that evidence.** The staff set is easier than r/nba: it
  has 7 hard posts out of 30, and it's grouped by label in id order, which
  leaks the answer. The r/nba hard cases (for example "It's $25 million a
  year…") are messier than anything in it. And nobody checked the 201
  r/nba labels by hand. So the report makes a labelling cause unlikely but
  doesn't rule it out. Criterion 4 was the instrument for that, and it was
  never run.
- **A smaller, separate effect: too few `analysis` examples in the test
  set.** With 8 `analysis` posts in test, one post moves its F1 by about
  0.1. That explains wobble between 0.6 and 0.7. It can't explain 0.000
  twice and 0.706 once. That jump is the model either learning the label or
  not, which is the undertraining above.

**What would confirm it:** keep the same labels and train longer (more
epochs or a higher learning rate). If undertraining is the cause, training
loss should drop well below 1.10, and seeds 42 and 7 should start
predicting `analysis`. If the labels are the cause, more training won't fix
the `analysis`→`hot_take` flow.

**Miss 4 (self-consistency): never measured.** No unaided labelling was ever
done. The cold slot and the staff set were both labelled by Claude at my
request, and I never re-labelled 30 posts cold to compare against. There's
no number, so it counts as a miss, not a pass. This is a gap in the
evidence, not a model problem. It's also the main thing standing between
"probably not a labelling problem" and "not a labelling problem."



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 30 / 30 = 100%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Who labelled these:** the 30 posts in `my_staff_labels.csv` were labelled
by Claude (Claude Code), at my request, under the staff definitions and
rules in `data/staff_taxonomy.md`. They were committed (`50929ae`) before
`data/staff_labels.csv` was in the repo, so the answer key wasn't seen.
This is not a measure of my own labelling, the same way no cold check of
mine exists (see The Dataset).

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

None. Every one of the 30 labels matched staff, so piles (a), (b) and (c)
are all empty.

**The hard posts anyway.** Staff marked 7 posts as borderline. Before the
key arrived, the labelling also wrote down which posts it had hesitated on.
Comparing those two lists shows how the agreement was reached:

| Post | Staff flagged | Hesitated on before the key | Label (both) | Rule that settled it |
|---|---|---|---|---|
| sp_005 carbon plates, 35→50 mpw | yes | yes | `analysis` | 1: the mileage change carries the claim |
| sp_010 three runners, same session | yes | yes | `analysis` | 1: tiny sample, but the claim rests on it |
| sp_014 "3% since the shoe rules" | yes | no (it's the taxonomy's own example) | `hot_take` | 1: the number is decoration |
| sp_019 "nobody enjoys the long run" | yes | no | `hot_take` | 2: a claim about everyone, not the writer |
| sp_020 "every injury thread…" | yes | no | `hot_take` | 1: checkable in principle, no count given |
| sp_028 "third DNF this year" | yes | no | `reaction` | 2/3: countable, but nothing argued from it |
| sp_029 PB shoes, wrong size | yes | yes | `reaction` | 2: the writer's own realisation |
| sp_007 same loop, 4C vs 24C | no | yes | `analysis` | 3: recency doesn't outrank evidence |
| sp_017 "peaked in about 2015" | no | yes | `hot_take` | 1: the year is decoration |
| sp_023 "eight weeks out, two miles" | no | yes | `reaction` | 2: the numbers describe, they don't argue |

**What the pattern in my disagreements tells me:**

There are no disagreements to read a pattern from. My honest read is that
the set was easier than the 70s rate staff call normal, for two reasons.

1. **The written rules are tight for this set.** Every one of the 7 posts
   staff flagged was settled by a numbered rule (rule 1, 2 or 3). None of
   them needed the rule-4 tie-break. My own taxonomy copied that structure:
   a load-bearing fact, then "a claim about the world vs the writer",
   then "recency doesn't outrank evidence". So these rules were already the
   ones being applied every day.
2. **The set leaks its own answer.** The posts are grouped by label in id
   order: sp_001–010 are all `analysis`, sp_011–020 all `hot_take`, and
   sp_021–030 all `reaction`. A labeller reading top to bottom gets a strong
   prior from position. That inflates the rate, and I can't separate it from
   applying the rules well.

What this does **not** tell me is how consistent my own 201 r/nba labels
are. Those were pre-labelled by the same AI, and criterion 4 (self-agreement
on 30 cold re-labels) was never measured. The undertrained model in the run
log is still the main diagnosis, and this result doesn't change it.

---

## The Improvement

**What I changed:** one training setting: **`EPOCHS` from 3 to 10**. Nothing
else changed: same `labels.csv`, learning rate 2e-5, batch 16, max length
128, and the same seeds (42, 7, 2024), so the same splits. `RUN_LABEL` was
set to `"after"`, and the results are in `results_three_seeds_after.json`.
The change was committed (`Improvement: EPOCHS 3 -> 10`) before the
after-run, and I picked 10 once. I didn't try other values against the
test splits.

**Which diagnosis pointed at it:** misses 2 and 3. Final training loss in the
before-run was 1.03 / 1.06 / 1.02, barely under the 1.10 (ln 3) of uniform
guessing. That's about 27 optimiser steps, and two of three seeds never
predicted `analysis`. The agreement report made a labelling cause unlikely
(the same labeller matched staff 30/30, including every hard
`analysis`/`hot_take` post), so the fix had to come from training, not
relabelling. Ten epochs gives the model about 90 steps.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Balance | each label 25–40% of 201 | 27 / 37 / 35% | (same file) | (same file) | MET |
| 2. Accuracy | ≥ 0.65 on all seeds | 0.710 | 0.710 | 0.839 | MET |
| 3. Per-label F1 | every label ≥ 0.55 on all seeds | min 0.600 (`hot_take`) | min 0.636 (`hot_take`) | min 0.783 (`hot_take`) | MET |
| 4. Self-consistency | ≥ 85% on 30 cold re-labels | not measured | not measured | not measured | MISSED |
| 5. Confidence gap | most-sure third ≥ least-sure third + 0.20 | 0.90 vs 0.50 (+0.40) | 1.00 vs 0.30 (+0.70) | 0.80 vs 0.80 (+0.00) | MISSED |

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.710 | 0.710 | 0.839 | 0.129 |
| Macro F1 | 0.700 | 0.712 | 0.838 | 0.138 |
| F1 — `analysis` | 0.667 | 0.737 | 0.824 | 0.157 |
| F1 — `hot_take` | 0.600 | 0.636 | 0.783 | 0.183 |
| F1 — `reaction` | 0.833 | 0.762 | 0.909 | 0.147 |

Confusion matrix, seed 42, after:

| true \ predicted | analysis | hot_take | reaction |
|---|---|---|---|
| **analysis** | 6 | 2 | 0 |
| **hot_take** | 3 | 6 | 3 |
| **reaction** | 1 | 0 | 10 |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->

**Yes, on the two criteria it was aimed at. It also cost one criterion that
had been met.**

| | Before (3 epochs) | After (10 epochs) |
|---|---|---|
| Accuracy, seeds 42 / 7 / 2024 | 0.613 / 0.387 / 0.710 | 0.710 / 0.710 / 0.839 |
| Accuracy spread | 0.323 | 0.129 |
| `analysis` F1, seeds 42 / 7 / 2024 | 0.000 / 0.000 / 0.706 | 0.667 / 0.737 / 0.824 |
| Final training loss | 1.03 / 1.06 / 1.02 | 0.64 / 0.76 / 0.67 |
| Criteria met | 1, 5 | 1, 2, 3 |

- **The diagnosis was right.** Training loss dropped well below 1.10 on
  every seed. Seeds 42 and 7, which never predicted `analysis` before, now
  predict it 10 and 11 times out of 31, and their `analysis` F1 went from
  0.000 to 0.667 and 0.737. That's the result I said would confirm
  undertraining. A labelling problem wouldn't have been fixed by more
  steps.
- **The confusion changed shape.** Before, the `analysis`/`hot_take` flow
  was one-way: 8 posts went to `hot_take` and 0 came back. Now it's 2 one
  way and 3 the other. Section 7 calls that roughly symmetric. The model
  now treats this as a hard boundary rather than ignoring a label.
  `hot_take` is the weakest label on every seed (0.60–0.78), which fits:
  it's defined by an absence (no support), and it sits on both of my
  hard boundaries.
- **The spread is still more than 10 points (0.129).** Each seed's test
  split has only 31 posts, so one post is worth 0.032 accuracy, and the
  0.710 vs 0.839 gap is 4 posts. The dataset is still too small for a
  stable measure. The swing just shrank from "learned or didn't" to
  sampling noise.
- **What got worse: criterion 5.** Before, the confidence gap was +0.40 on
  every seed. After, seed 2024's least-sure third is as accurate as its
  most-sure third: 0.80 vs 0.80. That's a ceiling effect. With accuracy at
  0.839, 8 of the 10 least-confident posts are right, so there's no room
  for a gap. But the target says "on all three seeds", so it's a miss. On
  the best seed, confidence no longer tells you which predictions to
  double-check.
- **Not changed by this: criterion 4.** No amount of training measures
  self-consistency, so it's still missed.

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
