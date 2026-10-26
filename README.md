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

TakeMeter is a text classification project that analyzes public
Reddit posts from r/Sephora, a community focused on beauty products,
shopping experiences, and recommendations.

The goal is to train a machine learning model that categorizes posts
based on the type of discussion they contain. This project explores
how fine-tuning a pretrained language model can help identify
different patterns in online beauty discussions.


---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `advice_request`

**Definition:** A post primarily asking other community members for beauty product recommendations, comparisons, or guidance.

**Example 1:**
>"Foundation recommendations please!!"
https://www.reddit.com/r/Sephora/comments/1wnlkah/
The author asks for foundation recommendations that work
with combination skin and do not emphasize fine lines.

**Example 2:**
>"Foundation recommends"
https://www.reddit.com/r/Sephora/comments/1r2a6vy/
The author asks for a full-coverage foundation that
can cover freckles while matching their light skin tone.

### `product_experience`

**Definition:** A post primarily sharing firsthand observations or evaluations of beauty products the author has personally tried.

**Example 1:**
>"POV Beauty in depth review"
https://www.reddit.com/r/Sephora/comments/1wb2q99/

The author shares detailed impressions of beauty
products they personally swatched at Sephora.

**Example 2:**
>"Honestly Reviewing Sephora lippies"
https://www.reddit.com/r/Sephora/comments/1gogh94/

The author shares reviews and opinions about
multiple lip products available at Sephora.


### `opinion_complaint`

**Definition:** A post primarily expressing a personal judgment,
frustration, or criticism about beauty brands, retailers, or
shopping experiences rather than reviewing product performance.

**Example 1:**
"Disappointing birthday rewards"
https://www.reddit.com/r/Sephora/comments/1t5xvqo/

The author expresses dissatisfaction with Sephora's
birthday reward options.

**Example 2:**
"Is Sephora declining or is it just me?"
https://www.reddit.com/r/Sephora/comments/1gp4ec9/

The author criticizes changes in Sephora's customer
service, shipping, and loyalty program.


### The hardest boundary

**Which two labels:** `advice_request` and `product_experience`

**The decision rule I used every time:**
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->

If a post describes a personal experience with a product but
primarily asks for recommendations or help choosing another
product, I classify it as `advice_request`.

If the post primarily describes how a product performed for
the author without making advice its main purpose, I classify
it as `product_experience`.

For posts that overlap with `opinion_complaint`, I focus on
the primary purpose. Firsthand product performance reviews
belong to `product_experience`, while general criticisms of
Sephora's customer service, rewards, or shopping experience
belong to `opinion_complaint`.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**

Public text posts from r/Sephora, supplied in chat by the project author.
Image-only content was not transcribed.

**How I labelled them:** AI-assisted draft labels were suggested for the
pasted posts using the taxonomy above. The author confirmed reviewing and
approving the first 24 labels. Later-added rows remain marked
`needs user review` in `labels.csv`; those labels are not presented as
author-reviewed or cold-labeled. No cold-labeling has been completed.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `advice_request` | 54 | 75.0% |
| `product_experience` | 11 | 15.3% |
| `opinion_complaint` | 7 | 9.7% |
| **Total** | **72** | **100%** |

This is a partial dataset, not the required 200 examples. `advice_request`
exceeds the required 70% cap. I proceeded with the available data because of
time constraints; results should be treated as exploratory, not as evidence
that the assignment dataset requirements were met.

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

These are AI-identified borderline examples; their current labels are
provisional and await author review.

**1.** “Anyone else missing this gem??” describes a favorite setting spray
from firsthand use but asks whether it will come back in stock.
> *Could have been:* `product_experience` or `advice_request`
>
> *Current draft label:* `advice_request`, because the post asks whether the
> product will return.

**2.** “Kosas soulgazer mascara vs. Ilia limitless lash mascara” describes
firsthand experience with one mascara and asks which of two options is better.
> *Could have been:* `product_experience` or `advice_request`
>
> *Current draft label:* `advice_request`, because its main purpose appears
> to be choosing between products.

**3.** “Heads up if you were planning to buy Haus Labs concealer during the
sale” reports a broken applicator, asks how to get product out, and later
describes a positive customer-service resolution.
> *Could have been:* `product_experience`, `advice_request`, or
> `opinion_complaint`
>
> *Current draft label:* `opinion_complaint`, because the main point appears
> to be reporting a product packaging failure and its effect on the purchase.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate `2e-5`, batch size 16, seed 42,
maximum length 128. Device: CPU (`torch 2.14.1+cpu`).

**Anything I changed from the defaults, and why:** No hyperparameters changed.
The notebook label map was configured for this project's three labels.

**Split sizes:** 50 train / 11 validation / 11 test. Test labels: 8
`advice_request`, 2 `product_experience`, 1 `opinion_complaint`. These latter
two supports are too small for stable per-label metrics.

**Run result:** Accuracy 0.727; macro F1 0.281. Per-label F1: 0.842
(`advice_request`), 0.000 (`product_experience`), and 0.000
(`opinion_complaint`). The model predicted every test post as `advice_request`,
so the accuracy is misleading under this imbalanced dataset. The run was
exploratory and does not establish that the acceptance criteria are met.
`results.json` and `test_split.csv` contain the saved metrics and held-out
examples. The notebook's data check flagged the 75% advice-request share
above the assignment limit, but the run was continued at the author's request.


---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* I pasted r/Sephora posts and asked for help putting
  them into the CSV.
- *What came back:* AI-suggested draft labels and notes identifying borderline
  cases or omitted image content.
- *What I changed:* The first 24 labels were reviewed and approved by me.
  Later-added suggestions remain marked for review in the CSV.

**Moment 2**

- *What I asked for:* I asked whether the current CSV was labeled and formatted
  correctly.
- *What came back:* A check of its CSV structure, fields, labels, and obvious
  duplicates, plus a report of missing project requirements.
- *What I changed:* I continued supplying posts and approved continuing with
  the dataset I had available; I did not claim the requirements were met.

The assistant reported that the pre-improvement model predicted every
held-out post as `advice_request`. I chose class-weighted loss as one
improvement. The assistant configured the notebook label map and training
change, then ran the training/evaluation cells with the project's `.venv`.
Metrics and held-out splits are saved as project files; live cell outputs and
execution counts are not embedded in the notebook.

**Pre-labelling disclosure:**

AI suggested labels for posts I pasted; I reviewed and approved the first 24.
The remaining later-added rows are still flagged as requiring my review.
No rows are marked cold because I did not complete the cold-labeling task.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models were scored on the same 11 rows in test_split.csv. -->

| Measure | Baseline | Trained (seed 42) | Difference |
|---|---:|---:|---:|
| Overall accuracy | 0.727 | 0.727 | +0.000 |
| Macro F1 | 0.733 | 0.281 | -0.453 |
| F1 — `advice_request` | 0.800 | 0.842 | +0.042 |
| F1 — `product_experience` | 0.400 | 0.000 | -0.400 |
| F1 — `opinion_complaint` | 1.000 | 0.000 | -1.000 |

The baseline used `facebook/bart-large-mnli` and the definitions in
`label_definitions.txt`; `baseline_results.json` confirms the held-out set
was used.

**What I predicted before I looked:** I did not record a prediction before
seeing the results, so I cannot honestly reconstruct one after the fact.

**What the gap actually means:** The models tied on accuracy for these 11
posts, but the zero-shot baseline had much higher macro F1. The trained model
predicted every test post as `advice_request`; the tied accuracy therefore
hides its failure to identify the two minority labels.

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---:|---:|---:|---|
| 1. Overall accuracy | ≥ 0.65 | 0.727 | 0.727 | 0.727 | MET |
| 2. Every label F1 | ≥ 0.55 | min 0.000 | min 0.000 | min 0.000 | MISSED |
| 3. Dataset balance | max share ≤ 50% | 75.0% | 75.0% | 75.0% | MISSED |
| 4. Labeling consistency | agreement ≥ 80% on 30 | NOT TESTED | NOT TESTED | NOT TESTED | NOT TESTED |
| 5. `advice_request` recall | ≥ 0.65 | 1.000 | 1.000 | 1.000 | MET |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| True \ Predicted | `advice_request` | `product_experience` | `opinion_complaint` |
|---|---:|---:|---:|
| `advice_request` | 8 | 0 | 0 |
| `product_experience` | 2 | 0 | 0 |
| `opinion_complaint` | 1 | 0 | 0 |

**My biggest off-diagonal number, and what it means:** Two real
`product_experience` posts were called `advice_request`, and the one
`opinion_complaint` post was also called `advice_request`. This is consistent
with the model defaulting to the majority label rather than learning the
minority-category boundaries.

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.65 on all three seeds | MET | All three pre-improvement runs scored 0.727. |
| 2 | Every label F1 | ≥ 0.55 on all labels | MISSED | All three runs had F1 0.000 for `product_experience` and `opinion_complaint`. |
| 3 | Dataset balance | No label over 50% | MISSED | 54 of 72 rows are `advice_request` (75.0%). |
| 4 | Labeling consistency | ≥ 80% agreement on 30 posts | NOT TESTED | My independent labels and the staff answer file were not available; the assignment's personal labeling step remains incomplete. |
| 5 | `advice_request` recall | ≥ 0.65 on all three seeds | MET | All three pre-improvement runs had recall 1.000 for this label. |

**Diagnoses**

- **Criterion 2:** The strongest supported cause is too few minority-label
  examples combined with class imbalance: only 11 posts are
  `product_experience` and 7 are `opinion_complaint`; the held-out seed-42 set
  contains only 2 and 1, respectively. The confusion matrix shows all three
  were predicted as `advice_request`. The agreement check was not completed,
  so I cannot claim whether inconsistent annotation also contributed.
- **Criterion 3:** This is a direct count failure, not a model metric:
  `advice_request` is 75% of 72 rows, above both the criterion's 50% target
  and the assignment's 70% cap. I did not have time to collect and review more
  minority-label posts.
- **Criterion 4:** No verdict is possible without 30 independent labels and
  the staff key. I did not complete that personal labeling step.

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** NOT TESTED. The assignment requires me to label all 30
staff posts independently before seeing the staff labels. I chose not to
complete this step, and `data/staff_labels.csv` was not present in the repo.
`my_staff_labels.csv` is only an empty-label template, not a completed
annotation file.

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements:** Not available; no comparison was run. No disagreements or
adjudications are claimed.



---

## The Improvement

**What I changed:** I enabled class-weighted cross-entropy training. The
training split's class weights are inverse to each label's frequency
(`n_train / (number_of_labels × label_count)`). This was the one change; the
data, splits, model, seed list, and other settings were unchanged.

**Which diagnosis pointed at it:** Before the change, the model predicted all
11 seed-42 test posts as `advice_request`; the training split had few examples
of the minority labels. Weighting was chosen to counter the observed
majority-class collapse, not to claim the dataset imbalance had been fixed.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.65 | 0.818 | 0.182 | 0.455 | MISSED |
| 2. Every label F1 | ≥ 0.55 | min 0.000 | min 0.000 | min 0.000 | MISSED |
| 3. Dataset balance | max share ≤ 50% | 75.0% | 75.0% | 75.0% | MISSED |
| 4. Labeling consistency | agreement ≥ 80% on 30 | NOT TESTED | NOT TESTED | NOT TESTED | NOT TESTED |
| 5. `advice_request` recall | ≥ 0.65 | 1.000 | 0.000 | 0.500 | MISSED |

**Did it help, and how do I know:** It helped macro F1 slightly on average
(0.281 before, 0.346 after), but hurt average accuracy (0.727 before, 0.485
after) and made results highly seed-sensitive. It did not solve the issue:
the three test sets are tiny, and the training data still contains only seven
`opinion_complaint` examples.

---

## What's Still Broken

1. **Overall accuracy after the improvement:** Seed 7 and seed 2024 missed
   0.65. I would gather and review more examples before more training; I
   stopped because of time.
2. **Per-label F1:** Every seed had at least one label with F1 0.000. I would
   add genuine examples for the thin labels, review their annotations, and
   repeat trials; I stopped because I did not have time to collect and review
   that data.
3. **Dataset balance:** `advice_request` is 75%, above the 50% criterion and
   70% assignment cap. I would collect more real posts from the other labels
   and personally review them; I stopped at 72 posts because of time.
4. **Labeling consistency:** Not tested. I would label the staff set myself,
   then obtain the staff key and adjudicate disagreements; I stopped before
   that required independent labeling step.
5. **Advice-request recall after the improvement:** Seeds 7 and 2024 were
   below 0.65. I would investigate the effect of weighting on minority and
   majority predictions using a larger, balanced dataset; I stopped after the
   single measured improvement.

**The gap between what I meant my labels to capture and what the model
learned:**
I intended the model to distinguish advice-seeking, firsthand product
experiences, and opinions or complaints, but before the improvement it
classified every held-out post as `advice_request`. After class weighting its
predictions varied sharply by seed, which suggests that the very small
minority-label samples are not enough for stable boundaries.

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
