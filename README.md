# Patient Experience & Drug Review Sentiment Analysis (NLP)

> **Kaggle Notebook:** https://www.kaggle.com/code/aritrapal3196/patient-experience-nlp-analysis

## 📌 Executive Summary
Understanding patient experience is critical for pharmaceutical companies and healthcare providers, but numerical ratings often fail to capture the severe side-effect burdens of specific treatments. 

This project adapts a Natural Language Processing (NLP) sentiment pipeline to analyze over 160,000 patient drug reviews. By cleaning clinical text and extracting VADER sentiment polarity scores, I quantified "patient experience" across hundreds of medical conditions to identify which treatment categories suffer from the most negative side-effect sentiment.

---

## 📊 Key Analytical Insights
1. **Validation of NLP against Clinical Ratings:** The analysis proves a strong positive correlation between patient-submitted numerical ratings (1-10) and VADER text polarity, validating the NLP pipeline's accuracy.
2. **Detection of Hidden Side-Effect Burdens:** Grouping sentiment by medical condition revealed that treatments for chronic pain, severe weight management, and psychiatric conditions consistently generate the lowest text sentiment (averaging negative polarity), highlighting severe side-effect profiles that numerical ratings alone often obscure.

---

## 🛠️ Data & Methodology

### 1. Data Source
The dataset utilizes over 160,000 consumer-generated reviews of specific medications, including drug names, conditions, star ratings, and raw text reviews.
* **Source:** [UCI ML Drug Review Dataset](https://archive.ics.uci.edu/ml/datasets/Drug+Review+Dataset+%28Drugs.com%29)

### 2. Clinical Text Preprocessing
Raw review text often contains HTML artifacts and irregular characters. I built a vectorized cleaning function to:
* Decode HTML entities (e.g., converting `&#039;` to standard apostrophes).
* Strip non-alphabetical characters and normalize spacing.
* Standardize text to lowercase for consistent lexicon matching.

### 3. Sentiment Intensity Scoring
* **Algorithm:** VADER (Valence Aware Dictionary and sEntiment Reasoner).
* **Rationale:** I utilized VADER because its rule-based approach is highly optimized for social media and consumer review text, efficiently handling capitalization and punctuation to determine polarity without requiring a massive, labeled training set.
* **Aggregation:** Compound sentiment scores (-1.0 to +1.0) were aggregated at the `condition` level to rank patient experiences.

---

## 💻 Tech Stack
* **Language:** Python
* **Data Manipulation:** Pandas, NumPy
* **NLP & Text Processing:** NLTK (VADER SentimentIntensityAnalyzer), Regex
* **Visualization:** Matplotlib, Seaborn

---

**Author:** Aritra Pal  
**Connect:** [Insert your LinkedIn URL]
