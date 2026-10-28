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

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

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
