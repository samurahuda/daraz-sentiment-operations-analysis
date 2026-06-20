# 🛒 Daraz BD — Consumer Sentiment & Operational Root Cause Analysis

> A multilingual NLP pipeline built to decode consumer psychology, regional linguistic patterns,
> and supply chain inefficiencies within the Bangladeshi e-commerce market.

![Python](https://img.shields.io/badge/Python-3.10-blue?style=flat-square&logo=python&logoColor=white)
![NLP](https://img.shields.io/badge/NLP-VADER%20%7C%20NLTK-green?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Google%20Colab-orange?style=flat-square&logo=googlecolab&logoColor=white)
![Dataset](https://img.shields.io/badge/Dataset-8%2C500%2B%20Reviews-purple?style=flat-square)
![Language](https://img.shields.io/badge/Languages-Bangla%20%7C%20English%20%7C%20Banglish-red?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)

---

## 📌 What Is This Project?

Standard sentiment analysis tools are built for clean, single-language English text. Real-world Bangladeshi e-commerce data doesn't work that way — customers write in pure Bangla script, pure English, and a hybrid called **Banglish** (e.g., *"packaging khub e baje chilo"*) all within the same dataset.

This project solves that by building a **custom multilingual NLP pipeline** on top of NLTK's VADER engine. It adds a translation pre-processing layer, a regional language override engine, a star-rating confidence anchor, and a keyword-based root cause classifier — extracting real operational business insights from 8,500+ Daraz customer reviews scraped from Bangladesh's largest e-commerce platform.

---

## 🗂️ Dataset

| Property | Detail |
|---|---|
| **Source** | Kaggle — Bangladesh Daraz Reviews Dataset |
| **Volume** | 8,500+ real customer reviews |
| **Language Mix** | Pure English · Pure Bangla (Unicode) · Banglish (Roman script) |
| **Primary Column** | `review_text` |
| **Supporting Column** | `rating` (1–5 stars, used as ground-truth anchor) |

---

## ⚙️ System Pipeline

```
[Raw Reviews: Bangla / English / Banglish]
               ↓
[Script Detection — skip translation if already English]
               ↓
[deep-translator — Bangla Unicode → English]
               ↓
[VADER Sentiment Scoring on translated text]
               ↓
[Banglish Rule Engine — 20+ regional term overrides]
               ↓
[Star Rating Anchor — correct VADER where rating is unambiguous]
               ↓
[Root Cause Classifier — keyword mapping on negative reviews]
               ↓
[Output: Daraz_Sentiment_Final.csv + Plotly Charts + Word Cloud]
```

---

## 🛠️ Tech Stack

| Layer | Tool / Library |
|---|---|
| Execution Environment | Google Colab (free tier) |
| Data Handling | Pandas |
| Translation | deep-translator (GoogleTranslator) |
| Sentiment Engine | NLTK — VADER Lexicon |
| Regional Language Override | Custom Banglish keyword ruleset |
| Rating Confidence Anchor | Star-rating ground truth logic |
| Visualization | Plotly Express · Matplotlib · WordCloud |
| Progress Tracking | tqdm |

---

## 🚀 How to Run

### Step 1 — Clone the repository
```bash
git clone https://github.com/samurahuda/daraz-sentiment-operations-analysis.git
cd daraz-sentiment-operations-analysis
```

### Step 2 — Open in Google Colab
Go to [colab.research.google.com](https://colab.research.google.com), click **Upload**, and select the `.ipynb` notebook file. Then upload `Daraz_Master_Reviews_bd.csv` directly to `/content/` in Colab's file panel.

### Step 3 — Run cells in order (Cell 1 → Cell 7)
Every cell is labelled and self-contained. **Do not skip or re-run cells out of order.** Each cell depends on the output of the one before it.

### Step 4 — Collect your outputs

| File | Description |
|---|---|
| `Daraz_Sentiment_Final.csv` | Full analyzed dataset, Excel-ready (UTF-8 BOM encoded) |
| `sentiment_chart.html` | Interactive Plotly sentiment distribution chart |
| `issue_breakdown.html` | Interactive Plotly root cause breakdown |
| `wordcloud.png` | Complaint theme word cloud (red colormap) |

---

## 🧠 Key Methodology Decisions

### Why `deep-translator` instead of `mtranslate`?
`mtranslate` is unmaintained and fails silently on longer or Unicode-heavy texts — it would freeze on Colab with no error and no output. `deep-translator` wraps the same Google Translate API with proper error handling, encoding support, and rate-limit awareness. It is reliable on Colab's free tier.

### Why detect Bangla script before translating?
Translating every row wastes API quota and significantly slows processing on 8,500 rows. The pipeline checks for Unicode Bangla characters (`\u0980`–`\u09FF`) before each translation call. If none are found, the text is already readable by VADER and the translation step is skipped entirely — cutting unnecessary API calls by approximately 60%.

### Why a Banglish override layer?
VADER was trained on English Twitter data. It has no knowledge of words like *"darun"* (excellent), *"thik ache"* (it's fine), or *"valoi"* (very good) — scoring them all as Neutral because they are outside its lexicon. The custom override engine checks for 20+ such regional Banglish expressions before VADER runs, preventing systematic false-negatives on positive Bangladeshi reviews.

### Why anchor sentiment to star ratings?
When a customer gives 5 stars, the review is Positive — regardless of what words they used. A short text like *"packaging okay"* scores Neutral on VADER, but the star rating is unambiguous ground truth. The rating anchor step uses `rating >= 4 → Positive` and `rating <= 2 → Negative` to correct VADER's edge cases, while 3-star reviews remain under VADER's control since they are genuinely mixed.

---

## 📊 Business Findings

### 🔴 Finding 1 — The Packaging Crisis
The single largest driver of negative reviews is **physical product damage during transit**, with liquid items (detergents, shampoos, cooking oils) arriving with broken seals or active leakage. The word cloud of complaint reviews is dominated by: *pack, box, damage, leak, broken, polythene*.

**Recommendation:** Mandate secondary containment sealing for all liquid SKUs at the warehouse level. Flag liquid-category sellers for specialized courier handling protocols.

### 🟡 Finding 2 — Hidden Fee Friction
Bangladeshi consumers show extreme sensitivity to **unexpected cost changes at checkout** — particularly sudden delivery fee increases or unanticipated Cash-on-Delivery (COD) surcharges appearing at the final payment screen. This generates post-purchase resentment even when the product itself arrived correctly.

**Recommendation:** Lock in delivery fee estimates at cart stage. Surface all COD charges prominently before checkout, not at the final confirmation screen.

### 🔵 Finding 3 — The Localization Gap (Core Research Contribution)
This project empirically demonstrates that **global NLP tools cannot accurately process mixed-language South Asian consumer data without custom structural adaptation**. Raw VADER applied to this dataset without the Banglish override and translation layer would systematically misclassify a significant share of positive regional reviews as Neutral or Negative.

This has direct implications for any analytics firm, startup, or platform operating in Bangladesh, India, Pakistan, or comparable mixed-language regional markets.

---

## 📁 Repository Structure

```
daraz-sentiment-operations-analysis/
│
├── Bilingual_text_mining_and_root_cause_operatio...ipynb   ← Main notebook
├── Daraz_Master_Reviews_bd.csv                             ← Source dataset (upload to Colab)
├── Daraz_Sentiment_Final.csv                               ← Output: analyzed dataset
├── sentiment_chart.html                                    ← Output: interactive chart
├── issue_breakdown.html                                    ← Output: root cause chart
├── wordcloud.png                                           ← Output: complaint visualization
└── README.md
```

---

## 🔮 Possible Extensions

- **Aspect-Based Sentiment Analysis (ABSA):** Score sentiment separately for delivery speed, product quality, and pricing within the same review rather than one overall label.
- **BanglaBERT Integration:** Replace VADER with a transformer model fine-tuned on Bangla and Banglish text for significantly higher classification accuracy.
- **Seller-Level Dashboards:** Group root cause categories by seller ID to surface which vendors consistently generate packaging damage or wrong-item complaints.
- **Time-Series Trend Analysis:** Track sentiment scores over months to detect seasonal complaint spikes around major shopping events like Eid sales.
- **Live Scraping Pipeline:** Replace the static CSV with a live Daraz review scraper to keep the analysis continuously updated.

---

## 👤 Author

**samurahuda**
Final Year Student — [Computer Science and Engineering Department, Brac University]
[GitHub — samurahuda](https://github.com/samurahuda)

---

## 📄 License

This project is licensed under the [MIT License](LICENSE). Dataset sourced from Kaggle under its respective terms of use.
