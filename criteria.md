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

## 1. Overall Classification Accuracy

<!-- Your criterion. It must name a number. -->
The model must achieve an overall accuracy of at least 0.65 (65%) on the held-out test set.

**Why this target:** My classifier distinguishes between three types of Sephora discussions: advice requests, product experiences, and opinions or complaints. With three approximately balanced labels, random guessing would achieve about 33% accuracy. I chose 65% because it represents a meaningful improvement while accounting for posts that naturally overlap categories.

---

## 2. Per-Label F1-Score

Each of the three labels (`advice_request`, `product_experience`, and `opinion_complaint`) must achieve an F1-score of at least 0.55 on the held-out test set.

**Why this target:** The distinction between advice requests and product experiences is particularly challenging because users often describe products they have tried before asking for recommendations. With approximately 200 posts divided into three categories, each label will have relatively few held-out examples. An F1-score of 0.55 provides a reasonable minimum while preventing the model from completely ignoring a difficult category.

---

## 3. Dataset Balance

No individual label may represent more than 50% of the complete labeled dataset of at least 200 posts.

**Why this target:** The three categories should be sufficiently represented so the model can learn their differences. Product recommendations and reviews are common discussions in r/Sephora, so I want to prevent one category from dominating the training data. A maximum of 50% per label is stricter than the project's required 70% limit and encourages a more balanced dataset.

---

## 4. Labeling Consistency

Two independent annotators using the same taxonomy and decision rules must agree on at least 80% of a shared sample of 30 posts.

**Why this target:** My labels depend on identifying the primary purpose of each post rather than judging whether the content is good or bad. The written decision rules should help annotators consistently distinguish between firsthand product reviews, recommendation requests, and general complaints. I chose 80% because some overlap is expected, but most posts should have a clear classification.

---

## 5. Advice Request Recall

The model must achieve a recall of at least 0.65 (65%) for the `advice_request` label on the held-out test set.

**Why this target:** Recommendation requests are an important part of the Sephora community. Some users mention previous product experiences before asking for new suggestions, making these posts easy to confuse with `product_experience`. A recall target of 65% means the classifier should correctly identify at least 65% of actual advice requests, even when their wording includes other discussion types.

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
