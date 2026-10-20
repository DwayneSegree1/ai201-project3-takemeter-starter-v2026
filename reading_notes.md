# Reading notes — unit 5, step 1

## Community

**r/nba** (https://www.reddit.com/r/nba). Public, no invite needed, and not
built around one show or release. It's huge, so 200 posts is easy: the
comment feed alone turns over 100 comments in under an hour on a game day.

I read comments, not post titles. Titles are mostly copied-in news headlines
("[Charania] LeBron James and Joel Embiid are expected to…"). The opinions are
in the comments.

**Sample read:** the 100 newest comments across the subreddit on
2026-10-20, which was opening day (Celtics vs Pistons at 12pm PT on
NBC/Peacock). That timing skews the sample. About a third of it is live game
thread chatter and complaints about the broadcast. My 200 should come from
several days, including non-game days, or one label will swamp the rest.

## Candidate distinctions

### 1. Some posts name a checkable fact. Most don't.

The fact can be a number, a rule, an injury, a price, or a date: something
you could look up and find wrong. About 10–12 of the 100 had one.

> "prediction markets require users only to be 18, not the 21 that most
> sportsbooks require"

> "The CBA technically requires sponsorships to be 'fair value' to prevent
> this scenario"

> "Wade has a sprained ankle so he is going to miss a bit. Barlow is also hurt
> and will miss at least first couple of weeks. They have no bigs right now."

Versus the same kind of claim with nothing to check:

> "Celtics shouldve kept Brown"

> "Paul George doesn't know how to pass."

### 2. Some posts give a reason. Others just give a verdict.

You can point at the reason. It shows up as a "because", "the problem is",
"the reason is", or "except". This is a different line from #1, because a
reason doesn't need a fact in it:

> "Trying to appeal to the European fans and make it like MLB. The problem is
> MLB has a 100+ year tradition and people will take off of work to go to
> opening day. It's not the same with the NBA."

> "Finding players is not an issue, if you have money. The issue will be
> finding fans. Fans usually support their teams long-term…"

A verdict on its own, said just as confidently:

> "All I see here is that NBA teams have too much fucking money."

**Hard case I already hit:** "NBA will start the project, see it going
bankrupt in 3 years, because nobody is supporting it…" It has a "because"
and a number, but both just restate the prediction. That's the same problem
as the staff "3% since 2017" example: the number is decoration, not support.

### 3. Some posts are about the writer's own last five minutes. Others are about the league.

> "I've got dateline and not the game…. This sucks"

> "Kind of fun having basketball playing in the background while I work"

> "Shoutout remote working for days like these"

Versus general claims, even angry ones:

> "I hate this fucking league so much. they're overpaid crybabies.
> Meanwhile, we can't even afford a fucking ticket."

The last one is the hard case: it's heated and personal, but its claim
("overpaid crybabies") is about the world, not about something that just
happened to the writer.

### 4. Some posts only make sense next to the comment they reply to

About 15 of the 100 can't be read on their own:

> "Hot air balloon"  ·  "Alopecia"  ·  "Both at the same time my friend"  ·
> "He invented the sport"  ·  "I will make it legal"

These aren't a label. They're a collection rule: **drop any comment that
can't be understood without its parent.** No reader could label them
consistently, and neither could a model. Long replies that restate what
they're answering are fine ("Except the new rules DO spread the money out
better…").

## What this suggests

Distinctions 1–3 line up with an analysis / hot take / reaction split, and
the hard boundaries look like the staff ones. The main risk is balance:
fact-bearing posts were about 1 in 10 here. To get enough of them, I'll need
to pull from news and trade threads, not only game threads.

---

## Second feed: r/movies (100 newest comments, 2026-10-20)

Read for comparison. It's public, has no single release at its centre, and
has plenty of text.

**What carried over:**

- **#1 (checkable fact)** still applies, at about the same rate (~8 in 100):
  > "I believe they only own half of Saw. Those movies are all coproductions"

  > "Brian Deer didn't back off, engaged a solicitor, and asked for all the
  > documents as part of the suit…"
- **#2 (reason vs verdict)** applies too:
  > "Not surprising given how few of their films actually broke through
  > culturally during his tenure. Netflix keeps throwing money at movies…"

  versus
  > "certified flop for sure"
- **#4 (context-dependent fragments)** is worse here, about 20 in 100. A lot
  of them are one-title answers to "what movie…" threads: "Baby Driver.",
  "Stuart Saves His Family.", "Mushrooms", "My girl".

**What changed:**

- **#3 doesn't fit.** Nothing is happening live, so there's no "last five
  minutes." The personal posts are about taste instead: "Double feature for
  me.", "Hans Zimmer's Dune soundtracks are genuinely some of the greatest
  film music I've ever heard." On r/movies, the split between taste and
  hot take is blurrier than the split between reaction and hot take on r/nba.
- **New candidate: questions.** About 10 in 100 are asking, not claiming:
  "When is the next trailer coming", "Why do astronauts have scuba goggles
  on board?" None of the three labels covers these, so I'd have to drop them
  or add a label.
- **Spoiler and off-topic risk.** The feed included an
  "Official Discussion … [SPOILERS]" thread, Dune: Part Three posts, and a
  cheese-shop AMA (two long comments about cheese rind). All of these need
  filtering out.

**Verdict:** I'm staying with **r/nba**. Its posts split cleanly along
distinctions 1–3, and spoilers aren't an issue. r/movies would need a fourth
label (questions) or heavy filtering, and its line between taste and hot
take is harder to draw.

---

## Stress test (unit 5, step 2)

A separate AI agent saw only my three draft definitions and wrote 8 r/nba
comments meant to sit on the boundaries. **Only 2 of 8 classified cleanly**
under the draft rules, so I tightened them before labelling anything.

| # | Post | First pass | Final | Rule it forced |
|---|---|---|---|---|
| 1 | "Ant dropped 40 last night and people still won't admit he's a top 3 player…" | unsure | hot_take | Scale: one game's stat can't carry a claim about a whole player |
| 2 | "…Jokic… won MVP in 2022 with Murray out the whole season and MPJ out basically all of it too, so please just stop." | analysis | analysis | (clean: heated, but the fact refutes the claim) |
| 3 | "Another Tatum stepback brick… This dude will never be a closer" | hot_take | hot_take | (clean: "would it still be a claim after tonight") |
| 4 | "Oh cool, another moving screen not called. Glad to see the refs are still getting their Lakers checks on time." | unsure | hot_take | Sarcasm: label the claim it implies |
| 5 | "Wemby has 6 blocks and it's not even halftime. Nobody is driving the lane anymore tonight…" | unsure | analysis | A fact from tonight counts if it supports a conclusion beyond the fact |
| 6 | "…left Curry wide open off the inbound AGAIN, that's the third time this quarter. Who is coaching this defense??" | unsure | reaction | A rhetorical question isn't a conclusion |
| 7 | "Knicks are winning the East, book it. Brunson took a discount extension at like $35M…" | unsure | hot_take | Label by the main claim; here the fact supports only a side point |
| 8 | "Luka is the best player in the world tonight… 45 on a bad ankle" | unsure | reaction | A fact that just restates the conclusion isn't support |

Pattern: the draft "delete the fact" test was too weak. Any fact offered as a
reason passes it. The real problems were facts at the wrong **scale** (#1),
facts aimed at a **side point** (#7), and stats from tonight that just
**restate** the feeling (#5, #6, #8). All of these rules are now in the
README.

---

## Collection and labelling log (unit 5, step 4)

**Collected:** 1,619 unique comments from r/nba's public RSS feeds on
2026-10-20 (opening day). They came from 19 threads: game threads, the Daily
Discussion, and news threads on contracts, injuries, broadcasting and trades.
The scraper was timeboxed and stopped once there was enough data.

**Filtered:** I read 360 candidates and dropped 150. The drop reasons were
the ones set out in the taxonomy: can't be read without the parent comment,
questions and AMA questions, jokes and bits, off-topic (harps, commutes,
John Tesh), insults, and politics.

**Balanced:** that left 45 `analysis`, 84 `hot_take` and 71 `reaction`,
which breaks criterion 1. To fix it, I read 45 more fact-heavy comments and
added the 10 that were clearly `analysis`, then randomly removed 9
`hot_take` rows (seed 2026, hard cases protected).

**labels.csv:** 201 rows, no duplicates.
- **20 rows** have `cold` in the note column and **no label**. These are
  for you to label unaided, before you look at anything else. Claude's
  labels for them are kept outside the repo.
- **181 rows** are marked `pre-labelled by Claude, NOT yet checked`. Read
  and correct every one, then change the note to `pre-labelled, checked`.

Counts using Claude's labels for all 201 rows: analysis 55 (27%),
hot_take 75 (37%), reaction 71 (35%). That's within criterion 1, and
nothing is near the 70% warning.

**Definition change made while labelling:** `reaction` now covers *news that
just broke*, not only live games. Many comments in news threads were plain
responses like "Genuinely hilarious situation" or "Really awesome letter".
They are reactions, but the old definition only covered games and
broadcasts.

## Hard cases (flagged `HARD CASE` in the note column)

1. **"Tyrese Maxey and Jalen Brown are 50PPG. They will carry this team…"**
   It's `analysis` or `hot_take`. → `analysis`: the 50 PPG is a season-level
   stat offered as the reason, so it passes the scale rule. It's shaky,
   because the number may be wrong, but checkable isn't the same as correct.
2. **"Get in price at MSG is $1,900. I don't think there has ever been a
   regular season NBA game that expensive before"**: `reaction` or
   `analysis`. → `analysis`: posted on the day, but the $1,900 supports a
   claim about all of history.
3. **"Let's go. In Georgia you have to pay like $40 a month for Bally
   sports… It's destroyed the fan base. Being able to watch on prime will
   be awesome."**: `reaction` or `analysis`. → `reaction`: the fact backs a
   side claim, and most of the post is excitement. This is the main-claim
   rule.
4. **"Same old nba...hucking 3s after 3s after 3s. A shoulder brush is a
   foul...garbage"**: `reaction` or `hot_take`. → `hot_take`: posted live,
   but "same old nba" is a claim about the league, not tonight.
5. **"It's $25 million a year. That's really nothing in today's NBA."**
   It's `analysis` or `hot_take`. → `hot_take`: the number is the thing
   being judged, not evidence. The post citing "86th highest paid" is
   `analysis`.
6. **"…historically bad things happen when Joel exceeds that [30 mpg]"**:
   `analysis` or `hot_take`. → `hot_take`: lots of minutes numbers, but the
   one doing the arguing ("historically bad things") isn't specific.

**Consistency watch:** "he was injured last year" counted as a checkable fact
when it was the only reason given (→ `analysis`), but not when it was
buried in a rant (→ `hot_take`). That's the main-claim rule, and it's worth
checking when you review.
