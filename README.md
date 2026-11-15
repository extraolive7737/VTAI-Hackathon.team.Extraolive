# Region by Region: A Scam Text Filter That Shows Its Reasons

> Veritas AI International High School Hackathon 2026.
> **Status: draft write-up. Sections marked TODO are not filled in yet and must be completed with our real results before the final submission.**

**Track:** Finance
**Team:** Jinwon Song, Sangjun Ha

This project is a prototype and is not financial advice.

## The problem

Older family members who are not used to smartphones get texts that pretend to be a delivery company, a bank or even their own child, and one tap on a link can cost them money. A plain "spam / not spam" label does not tell them what to look for next time.

TODO: tighten to 2-3 sentences once the demo is fixed.

## What we built

A filter that takes one text message and gives back:

1. a scam warning or a "looks fine" result, with a score, and
2. the top three reasons, written as fixed template sentences. A reason only names words that are really in the message, and the score is checked to be between 0 and 1 before it is shown.

TODO: paste one real input and output from the notebook.

## What we added

We started from suggested project 4.2.1 (Scam text filter) in the Project Briefs. Our additions:

1. **Our own question.** We split the features into "regions" (one small model per kind of warning sign, combined by a meta-model) so the filter can say which kind of sign it saw. What does that split cost in accuracy compared with one single model on the same features? We report the answer either way.
2. **Our own data.** The training messages are more than ten years old. We wrote 40 new test messages ourselves (20 in the style of scams common in Korea today, 20 normal), in English, with no real names, numbers or links, and tested whether the filter catches them. These messages are never used for training or for choosing keywords.
3. **Our own user.** The output is designed for an older family member: one clear warning and three short reasons.

## How it works

**Data.** SMS Spam Collection: 5,574 English text messages labelled spam or ham. TODO: confirm the counts we loaded and the split (stratified 80% train, 20% test).

**Models.**

- Baseline: label every message "not spam".
- Reference model: TF-IDF + logistic regression (the brief's recipe).
- Single model: one LightGBM on all hand-made features.
- Region model: a router sends each feature group to its own LightGBM, and a logistic regression meta-model combines the region scores. The meta-model is trained only on out-of-fold predictions from the training set.

**Regions.** Keyword lists are chosen from the training split only, before looking at any test data.

| Region | What it counts |
|---|---|
| Reward | words such as free, win, prize, cash, claim |
| Urgency | words such as urgent, now, today, expires |
| Action | words such as call, text, reply, click, plus links and phone-like numbers |
| Account | words such as account, bank, card, pin, verify |
| Shape | length, digit count, share of capital letters, exclamation marks |

The region idea is loosely inspired by how a brain routes signals to specialised areas. It is an engineering analogy, not a claim about neuroscience.

## Results

All numbers come from data the models did not see in training.

**Held-out test split of SMS Spam Collection**

| Model | Accuracy | Spam precision | Spam recall |
|---|---|---|---|
| Baseline (always "not spam") | TODO | TODO | TODO |
| TF-IDF + logistic regression | TODO | TODO | TODO |
| Single LightGBM | TODO | TODO | TODO |
| Region LightGBMs + meta-model | TODO | TODO | TODO |

**Our 40 new messages**

| Model | Scam-style caught (of 20) | False alarms (of 20 normal) |
|---|---|---|
| TF-IDF + logistic regression | TODO | TODO |
| Single LightGBM | TODO | TODO |
| Region LightGBMs + meta-model | TODO | TODO |

**Where it fails.** TODO: five messages the model gets wrong, and why we think so.

**Limits.** The filter only reads English, because the public labelled data is English. Our 40 new messages were written by us, so they are a small check and not a measurement of real-world performance.

## What we'd do next

TODO: one or two concrete next steps (for example, Korean training data).

## Sources

Datasets

- Almeida, T. & Hidalgo, J. (2011). SMS Spam Collection. UCI Machine Learning Repository. https://archive.ics.uci.edu/dataset/228/sms+spam+collection (DOI: https://doi.org/10.24432/C5CC84), licensed CC BY 4.0.
- `data/modern_scam_test.csv`: 40 test messages written by the team for this project. TODO: add once the file is in the repository.

Starting point

- Veritas AI International High School Hackathon 2026, Project Briefs, section 4.2.1 "Scam text filter" (baseline steps).

Libraries

- scikit-learn: https://scikit-learn.org
- LightGBM: https://lightgbm.readthedocs.io
- pandas: https://pandas.pydata.org

AI tools

- Claude (Anthropic), https://claude.ai : used for planning, code drafts and this write-up draft. TODO: list any other AI tool we use.

Pretrained models

- None.
