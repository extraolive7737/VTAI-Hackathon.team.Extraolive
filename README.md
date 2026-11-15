# Brain-Region Scam Text Filter

> Working title. Veritas AI International High School Hackathon 2026.
> **Status: draft write-up. Sections marked TODO are not filled in yet and must be completed with our real results before the final submission.**

**Track:** Finance
**Team:** Jinwon Song, Sangjun Ha

This project is a prototype and is not financial advice.

## The problem

TODO (2-3 sentences): who gets these messages, and why it matters.
Draft: People receive text messages that pretend to be a bank, a delivery service or a prize draw, and one tap on a link can cost them money. A plain "spam / not spam" label does not teach anyone what to look for next time.

## What we built

TODO: confirm after the demo runs end to end.
Draft: A filter that takes one text message and gives back (1) a scam warning or a "looks fine" result and (2) the top three reasons, written as fixed template sentences filled in with checked numbers.

## What we added

We started from suggested project 4.2.1 (Scam text filter) in the Project Briefs. Our additions:

1. **Our own question.** Does splitting the features into "brain regions" (one small model per feature group, combined by a meta-model) do better or worse than one single model on the same features? We report the answer either way.
2. **Our own data.** TODO: a small set of Korean scam-style messages that we wrote ourselves (no real personal information), used only for testing, to see where a model trained on old English messages fails.
3. **Explanations.** The filter says why it flagged a message, using the region scores and the strongest feature inside each region.

## How it works

**Data.** SMS Spam Collection: 5,574 English text messages labelled spam or ham. TODO: how we split it (stratified train/test) and anything we cleaned.

**Model.**

- Baseline: label every message "not spam".
- Reference model: TF-IDF + logistic regression (the brief's baseline recipe).
- Single model: one LightGBM on all hand-made features.
- Region model: a router sends each feature group to its own LightGBM, and a logistic regression meta-model combines the region scores. The meta-model is trained on out-of-fold predictions only.

Feature groups ("regions"), to be adjusted after looking at the data:

| Region | What it counts |
|---|---|
| Money and reward | words such as free, prize, win, cash, claim |
| Urgency | words such as urgent, now, today, expires |
| Call to action | words such as call, text, reply, click, links |
| Personal and account details | words such as pin, account, bank, verify |
| Message shape | length, digit count, share of capital letters |

## Results

All numbers below must come from test data the models did not see in training.

| Model | Accuracy | Spam precision | Spam recall |
|---|---|---|---|
| Baseline (always "not spam") | TODO | TODO | TODO |
| TF-IDF + logistic regression | TODO | TODO | TODO |
| Single LightGBM | TODO | TODO | TODO |
| Region LightGBMs + meta-model | TODO | TODO | TODO |

**Where it fails.** TODO: five messages the model gets wrong, and why we think so.

**Korean test messages.** TODO: how many we wrote, how many were caught, and what that tells us.

## What we'd do next

TODO: one or two concrete next steps.

## Sources

Datasets

- Almeida, T. & Hidalgo, J. (2011). SMS Spam Collection. UCI Machine Learning Repository. https://archive.ics.uci.edu/dataset/228/sms+spam+collection (DOI: https://doi.org/10.24432/C5CC84), licensed CC BY 4.0.
- TODO: our own Korean test messages (written by the team, no personal information).

Starting point

- Veritas AI International High School Hackathon 2026, Project Briefs, section 4.2.1 "Scam text filter" (baseline steps).

Libraries

- scikit-learn: https://scikit-learn.org
- LightGBM: https://lightgbm.readthedocs.io
- pandas: https://pandas.pydata.org

AI tools

- Claude (Anthropic), https://claude.ai : used for planning, code drafts and this write-up draft. TODO: list any other AI tool we use.

Pretrained models

- None so far. TODO: update if this changes.
