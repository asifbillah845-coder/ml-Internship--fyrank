# FlyRank Refresh Opportunity Scoring — Capstone Research Paper

## Abstract
This study asks which existing content pages should be reviewed first when editorial resources are limited. We use the bundled anonymized FlyRank content-refresh snapshot of 30,000 pages and define observed decline as `trend_direction == down`. A transparent refresh score is compared with Logistic Regression, Decision Tree and Random Forest models under a client-holdout evaluation with leakage checks. Random Forest achieved Precision@50 of 0.740 versus 0.240 for the baseline and ROC-AUC of 0.750 on the held-out test set. The output is a ranked, human-review decision-support queue rather than a causal claim about search-engine behavior.

## 1. Problem statement
The operational decision is which existing pages should receive scarce editorial/SEO review capacity first. The unit is an anonymized content item. A wrong recommendation can waste review time or cause a high-opportunity page to be missed.

## 2. Data
The bundled release contains 30,000 anonymized rows and 44 columns. The preparation contract requires positive 90-day impressions and content age of at least 90 days, removes duplicate content IDs, and defines the target as `trend_direction == down`. `trend_direction` and `trend_pct` are excluded from model features because they define/encode the target. `content_id` and `client_id` are identifiers only.

## 3. Baseline
The transparent baseline combines visibility (40%), freshness risk (30%), position opportunity (25%) and depth gap (5%). On the same held-out client test set it achieved ROC-AUC 0.627 and Precision@50 0.240.

## 4. Model and analysis
We compared Logistic Regression, Decision Tree and Random Forest. Random Forest was selected by Precision@50 because the business decision is a small ranked review queue. The model used numeric and categorical descriptors of demand, performance, content size, age/freshness, position and engagement.

## 5. Evaluation
The evaluation uses a client-holdout split: 27,675 training rows and 2,325 test rows. No client is represented in both sets. The positive-class base rate is 0.542.

| Method | ROC-AUC | Avg precision | Precision@50 | Recall | F1 |
|---|---:|---:|---:|---:|---:|
| Baseline rules | 0.627 | 0.468 | 0.240 | — | — |
| Logistic Regression | 0.700 | 0.522 | 0.400 | 0.567 | 0.566 |
| Decision Tree | 0.742 | 0.575 | 0.660 | 0.716 | 0.634 |
| Random Forest | **0.750** | **0.618** | **0.740** | 0.744 | **0.640** |

## 6. Interpretation
The strongest Random Forest feature importances were days with impressions, log impressions, average position and content age. These should be interpreted as model-useful signals, not causal drivers. The signal audit also found that the simple staleness assumption was not confirmed: pages at least 180 days since update had a lower decline rate than the non-stale group in this snapshot.

## 7. Ranked recommendations
1. Review high-confidence candidates first.
2. For visible pages with low CTR, manually inspect search-intent alignment and snippet/title opportunities.
3. For low-engagement candidates, manually inspect usefulness, clarity and page experience.
4. Treat age/staleness as supporting evidence rather than a standalone refresh trigger.
5. Use `monitor` for low-confidence cases unless editorial context indicates otherwise.

## 8. Reproducibility
Run `pip install -r requirements.txt` followed by `python scripts/run_all.py` from the repository root. The random seed is 42. The generated queue is `outputs/refresh_queue.csv`; metrics are in `outputs/model_results.json`.

## 9. Limitations and honest framing
This is a snapshot-based observational classification/scoring study. It does not provide a genuine future-window outcome, so it is not a production temporal forecast. It cannot prove that refreshing content causes traffic or ranking gains and cannot be used to claim prediction of Google's algorithm.

## Acknowledgments & data credit
Built on the FlyRank ML Internship dataset — https://flyrank.ai
