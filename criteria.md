# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Balance: every label is between 25% and 40% of the 200 posts

At least 50 posts and at most 80 posts per label in `labels.csv`.

**Why this target:** In my reading, posts with a checkable fact were about 1
in 10 on r/nba, so if I sampled naturally, `analysis` would get about 20
examples and roughly 3 in the test split. That's too few to measure. With 50
as the floor, every label gets about 8 test posts, the point where the
notebook stops flagging a label as thin. The 40% cap keeps an
always-guess-the-biggest-label model at or below 0.40, so criterion 2 still
means something.

---

## 2. Overall accuracy: at least 0.65 on the held-out test split, on all three seeds

**Why this target:** The test split is about 30 posts, so 0.65 means at
least 20 right on the worst seed. That's well above the 0.33 a coin flip gets
and the 0.40 that criterion 1 caps majority-guessing at. The zero-shot
baseline scored 0.63 on the staff running posts with these same three
labels. A fine-tuned model that can't beat a model that never saw my data
isn't worth training. One extra wrong post moves accuracy by 0.033, and all
three seeds have to clear the bar, so I didn't go higher.

---

## 3. Per-label: every label has an F1 of at least 0.55 on all three seeds

**Why this target:** `hot_take` is defined by what's missing (no support),
and it sits on both of my hard boundaries. The zero-shot baseline scored
0.00 F1 on it. A model can hit 0.65 accuracy by getting `analysis` and
`reaction` right and giving up on `hot_take`, and this criterion is there to
catch that. I set it 0.10 below criterion 2 because with about 8–10 test
posts per label, one post moves a label's F1 by roughly 0.1 between seeds.

---

## 4. Consistency: I agree with myself on at least 85% of 30 re-labelled posts

At least a week after labelling, I'll re-label 30 random posts from
`labels.csv` cold, without looking at my first labels, and compare the two
passes with `agreement.py`
(`--staff-posts` / `--staff-labels` pointed at my first pass).

**Why this target:** 85% allows 4 of the 30 to flip. That's about the number
of genuinely borderline posts I'd expect, given that 6 of the 8 stress-test
posts needed a new rule. More flips than that would mean the rules in my
README aren't being applied the same way each time, and the model can't
learn a boundary I don't hold steady myself. It's higher than the 70s staff
call normal for the staff set, because these are my own rules on my own
community, not someone else's.

---

## 5. Confidence: the most-confident third is at least 0.20 more accurate than the least-confident third, on all three seeds

Measured with the notebook's confidence check (most-confident third vs
least-confident third accuracy).

**Why this target:** My stress test showed where the borderline posts are:
mid-game comments with a stat in them, and sarcasm. I want the model to be
unsure on those and sure on clean ones like "I've got dateline and not the
game". Then a low-confidence prediction can be sent to a human instead of
trusted. Each third is about 10 posts, so 0.20 is a 2-post gap. That's the
smallest difference that isn't one lucky post, and it has to hold on all
three seeds.

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
