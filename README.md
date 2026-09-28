# Patient Experience & Drug Review NLP Analysis

Kaggle notebook: https://www.kaggle.com/code/aritrapal3196/patient-experience-nlp-analysis

## What this project is about

A star rating tells you if a patient liked a drug. It doesn't tell you why. Two drugs can both average 7/10 while one of them keeps giving people headaches or making them put on weight, and you'd never see that from the rating alone.

I went through 215K patient reviews from Drugs.com and used NLP to read what patients actually wrote. I wanted to find the side effects they complain about, see which of those go along with bad ratings or with people stopping the drug, and spot drugs that look fine on ratings but have a problem hiding in the text.

## Results

### The data after cleaning
I started with 215,063 reviews. 1,194 had no condition and 1,171 had website junk in the condition field, so those were dropped. Another 84,761 were the same review listed under both a brand and its generic name. That left 212,698 rows, of which 127,937 are unique reviews.

### Does the text agree with the rating?
Only moderately. The Spearman correlation between rating and sentiment is 0.33 with normal VADER and 0.35 with my adjusted version. VADER gets the direction right 68.4% of the time for clearly good (7 to 10) or clearly bad (1 to 4) ratings. That makes sense once you read the reviews. A lot of happy patients spend half the review describing how bad things were before the drug worked, and VADER picks up all of that as negative.

Taking illness words out of VADER made a big difference to how conditions rank. Pain went from the 18th most negative condition to the 97th. Chronic Pain moved from 10th to 75th, and Osteoarthritis from 48th to 122nd. Normal VADER was making pain patients look unhappy mostly because they write the word "pain" a lot.

### Which side effects come up
46.8% of reviews mention at least one side effect.

| Side effect | % of reviews |
|---|---|
| Nausea | 9.5 |
| Tiredness | 9.0 |
| Skin problems | 8.1 |
| Headache | 8.0 |
| Anxiety | 7.8 |

### Which side effects go with bad ratings
A bad rating here means 1 to 3. About 21% of reviews that don't mention a given side effect are bad ones.

| Side effect | Bad rating % when mentioned | Relative risk |
|---|---|---|
| Heart racing | 39.1 | 1.81 |
| Skin problems | 33.8 | 1.62 |
| Mood swings | 33.5 | 1.59 |
| Hair loss | 34.2 | 1.58 |
| Dizziness | 32.0 | 1.50 |

Dry mouth went the other way. Reviews that mention it average 7.65 compared with 6.99 for the rest, with a relative risk of 0.59. My guess is that people see dry mouth as an annoying but fair trade for a drug that works.

### Hidden side effects
I found 35 cases where a drug is rated at or above the median for its condition but one side effect shows up at least 1.5 times as often as it does for other drugs treating the same thing. A few examples:

| Drug | Condition | Avg rating | Side effect | Drug % | Condition avg % |
|---|---|---|---|---|---|
| Spironolactone | Acne | 7.81 | Dizziness | 8.8 | 1.2 |
| Spironolactone | Acne | 7.81 | Weight gain | 8.8 | 1.6 |
| Quetiapine | Insomnia | 7.95 | Weight gain | 9.9 | 2.3 |
| Mirtazapine | Depression | 7.34 | Weight gain | 15.4 | 5.7 |
| Mirtazapine | Depression | 7.34 | Insomnia | 16.8 | 7.5 |

Quetiapine and mirtazapine are both known to cause weight gain, so seeing them flagged was a good sign the method picks up real effects. Several birth control pills (TriNessa, Sprintec, Tri-Sprintec, Ortho Evra) also showed nausea at around twice the average for birth control.

### Stopping or switching
6.1% of reviews talk about stopping or switching the drug. That goes up to 7.8% when a side effect is mentioned and drops to 4.6% when none is. The side effects most tied to stopping were hair loss (1.74 times more likely), mood swings (1.66) and heart racing (1.61).

## How I did it

### The data
The Drugs.com review dataset (Gräßer et al., 2018), from the [UCI ML Repository](https://archive.ics.uci.edu/dataset/462/drug+review+dataset+drugs+com). I used the Kaggle copy. Each row has the drug name, the condition it was taken for, the review text, a rating from 1 to 10 and the date. I combined the train and test files because I'm not training a model.

### Cleaning
Rows with no condition or with junk like `3</span> users found this comment helpful.` in the condition column were dropped. Duplicate brand/generic listings are left out of the overall numbers so nothing is counted twice. I kept them for the drug-by-drug comparison, since a review of the brand is also a review of the generic.

For the review text itself I only fixed HTML codes (`&#039;` back to an apostrophe). My first version also stripped punctuation and lowercased everything, which turned out to be wrong. VADER reads "didn't", capital letters and "!!!" to judge how strong a sentence is. The notebook shows the difference on one sentence: -0.84 left as it is, -0.34 after the old cleaning.

### Sentiment
Every review gets a VADER score from -1 to +1. VADER treats words like "pain", "depression" and "anxiety" as negative, so "this finally fixed my depression" loses points just for naming the illness. I made a second version of VADER with 20 of those illness words taken out and kept both scores.

To check the scores, I compared them with the patient ratings using Spearman correlation (ratings go in steps from 1 to 10, so Spearman fits better than normal correlation). I also checked how often VADER agreed with clearly good or clearly bad ratings.

### Finding side effects
I made word lists for 15 side effects: nausea, headache, weight gain, tiredness, insomnia, dizziness, anxiety, mood swings, stomach issues, skin problems, hair loss, low sex drive, dry mouth, spotting and heart racing. A review counts as mentioning one if it uses any of its words.

Phrases like "no nausea" or "didn't have any headaches" don't count. And when the side effect is the illness being treated (someone taking an anxiety medicine will naturally write "anxiety"), that side effect is skipped for those reviews.

### Linking side effects to ratings
For each side effect I split the reviews into the ones that mention it and the ones that don't, then compared how many in each group gave a bad rating. Dividing the two gives the relative risk.

### Hidden side effects
Drugs are only compared with other drugs for the same condition. I took the 6 conditions with the most reviews (Birth Control, Depression, Pain, Anxiety, Acne and Insomnia) and kept drugs with at least 200 reviews. A drug gets flagged when its rating is at or above the median for its condition and one side effect appears in at least 5% of its reviews, at 1.5 times the condition average or more.

### Stopping or switching
I searched the reviews for phrases like "stopped taking", "switched to" and "came off", then compared how often those show up with and without each side effect. When patients stop a drug, the cost usually shows up as unused prescriptions and extra doctor visits, so this is the part most relevant to adherence.

## Who could use this

The most obvious user is a brand team. They could see which side effects set their drug apart from competitors treating the same condition. The stop/switch results would also help a patient support programme decide which side effects to prepare patients for, since those are the ones most tied to people quitting.

## Limitations

People who write reviews usually had a strong experience, good or bad, so these numbers describe reviewers rather than every patient. The word matching is basic. It misses side effects that patients describe in other ways, and it can match part of a longer word. A medical NLP model like scispaCy would do better. A side effect in a review is what the patient reported, not something a doctor confirmed, and none of this proves the drug caused it. Patients also switch drugs for reasons like cost or the drug not working, so the stop/switch link is an association and nothing more. Some condition names in the original dataset are cut off ("Panic Disorde", "ibromyalgia"), and I left them as they are.

## Tools
Python, Pandas, NumPy, NLTK (VADER), SciPy, Matplotlib, Seaborn

## Running it
Open the notebook on Kaggle, add the dataset `jessicali9530/kuc-hackathon-winter-2018` and run all cells. Scoring the reviews takes a few minutes.

---

Aritra Pal · [LinkedIn](https://www.linkedin.com/in/aritrapal20/)
