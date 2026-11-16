# Headline Mood Check: What News Sentiment Did and Didn't Tell Us About the Dow

**Track:** Finance
**Team:** Jinwon Song, Sangjun Ha
**Code:** [MVP notebook (Colab, with outputs)](https://colab.research.google.com/drive/1e1bwrv5ZGxxx1sQyUpnvW9UiBdnr5-yF?usp=sharing) · [experiment log notebook](https://colab.research.google.com/drive/1zQQknPNWd2NqP9bjZDAM8-0F7OqSaSTZ?usp=sharing) · [full results and details](docs/details.md)

> This project is a prototype and is not financial advice.

**Main takeaway.** In our test, the mood of the day's top headlines did not call the Dow's next move better than always guessing "up" (49.8% vs 50.8% over 378 days). A scary or cheerful news day is not, by itself, evidence for a trade.

## The problem

A beginner investor reads the morning headlines and feels pushed to buy or sell. Many people believe the news mood shows where the market goes next, but they cannot check that belief before they act, and trading on noise is how small investors lose money.

## What we built

A "mood check" card for the moment right before a trade.

- **In:** the headlines you just read, pasted one per line. For the demo you can also pick a 2015-2016 date, which loads that day's 25 real headlines.
- **Out:** how negative the news is compared with every day in 2008-2014, a model's estimate that the Dow Jones Industrial Average closes up on the next trading day, that model's track record next to two simple guesses, and a plain sentence saying the estimate is not a reason to trade.

Part of the card for 2015-06-18, an ordinary test day picked with a fixed random seed:

```
1. News mood: more negative than 28% of days in 2008-2014 (FinBERT: 14 negative, 7 neutral, 4 positive).
3. Model estimate that the Dow closes up on the next trading day: 66%
4. Track record on 2015-16 (378 days, about +/-5 points of chance):
   this model 49.8% | always guess 'up' 50.8% | same as previous day 45.8%
5. News mood alone did not beat simply guessing 'up' in 2015-16 (49.8% vs 50.8%), so this estimate is not a reason to trade on its own.
   (Looking back: on 2015-06-19 the Dow actually went down, -0.55%.)
```

## What we added

We started from the suggested project "News headlines and the stock market" (Project Briefs 4.2.4) and added:

- **Our own user:** a beginner investor about to trade on the news, and a card built for that moment.
- **Our own questions:** can headlines you could read *before* a trading day predict it, not only same-day headlines as in the brief? Does mood relate to the *size* of moves?
- **Our own data:** 40 headlines we labeled (Sangjun Ha, with help from ChatGPT) to check FinBERT on world news.
- **A tested and rejected part:** a small language model for the card's explanation.

## How it works

**Data.** `Combined_News_DJIA.csv` and `DJIA_table.csv` from Kaggle: 1,989 trading days (2008-08-08 to 2016-07-01), the top 25 world-news headlines per day (49,718 headlines), and a label that is 1 if the DJIA rose or stayed the same. We removed a `b'...'` wrapper from about 24% of headlines. We split by date, never at random: an ML set (2008-2013 to train, 2014 to check settings) and a test set (2015-01-02 to 2016-07-01). Model settings were fixed before the test set was scored.

**Model.** FinBERT (pretrained, unchanged) scores each headline from -1 to +1. Eight daily features summarize the scores, and a small LightGBM model turns them into the estimate. We chose LightGBM because it suits small tables and shows which input moved each estimate.

## Results

| Model | Accuracy on the test set (2015-16, 378 days) |
|---|---|
| LightGBM, previous-day headlines (used by the card) | 49.8% |
| LightGBM, same-day headlines (the brief's setup) | 46.8% |
| Baseline: always "up" | 50.8% |
| Baseline: same as previous day | 45.8% |

With 378 days, about +/-5 points can be chance, so neither model differs from always guessing "up". The same-day model beat "always up" on our 2014 check (56.6% vs 55.2%) and then fell to 46.8%: a pattern from one period did not carry over. For the size of moves, none of the four relationships we tested held in both 2008-14 and 2015-16.

**Where it fails, and why we think so**

- **The model.** On 2015-09-28 it gave a 72% chance of an up day and the Dow fell 1.92%. The previous trading day's most negative headlines were about a stampede near Mecca, refugees and FIFA. Top world-news headlines often do not contain the news that moves the market.
- **FinBERT.** It matched our 40 labels only 55% of the time (Cohen's kappa 0.15). In 14 of the 18 disagreements we said negative and FinBERT said neutral, mostly violent or political news without financial wording.
- **The small language model.** Qwen2.5-1.5B changed meanings: "57% chance of an up day" became "correct 57% of the time". After our checks, 0 of 20 sentences in the second round were usable, so the card uses a fixed sentence.

**What these results would and wouldn't justify.** They would justify treating a scary or cheerful news day with caution before trading. They would not justify buying or selling because of the news mood, trusting the estimate for today's news (the model learned from 2008-2014 world news), or any conclusion about individual companies: this is an index-level analysis.

## What we'd do next

- Test financial headlines matched to specific companies, where news is more likely to carry market information.
- Measure whether seeing the card changes what beginner investors decide, which we did not test.

## Sources

- Data: `Combined_News_DJIA.csv` (the "Daily News for Stock Market Prediction" data named in the Project Briefs) and `DJIA_table.csv`, from https://www.kaggle.com/datasets/goodenoughzhenlei/combined-news-djiacsv. Kaggle lists the license as "Unknown", so we use the data only for this non-commercial student project and do not redistribute the dataset.
- FinBERT, `ProsusAI/finbert`, https://huggingface.co/ProsusAI/finbert, CC BY-NC-SA 4.0, used unmodified. Araci, D. (2019), arXiv:1908.10063.
- Qwen2.5-1.5B-Instruct (tested, not used in the card), https://huggingface.co/Qwen/Qwen2.5-1.5B-Instruct, Apache License 2.0.
- Libraries: LightGBM, pandas, NumPy, SciPy, scikit-learn, Matplotlib, Hugging Face Transformers, PyTorch. Platform: Google Colab.
- AI tools: Claude (Anthropic) helped plan the project and draft the code and this write-up; the team ran every step and checked the results. ChatGPT (OpenAI) helped label the 40 headlines.
- Team contributions, full tables and license notes: [docs/details.md](docs/details.md).
