# Refresh Opportunity Scoring

### FlyRank ML Internship — Capstone Project

An explainable machine learning system for prioritizing pages that may warrant content-refresh review.

## Live Research Paper

**Read the deployed capstone paper:**

https://asifbillah845-coder.github.io/ml-Internship--fyrank/

The paper presents the research question, methodology, model comparison, signal audit, recommendations, limitations, and reproducibility details.
 
---
## Project Overview

This project investigates whether available content and search-performance signals can be used to prioritize pages for **content-refresh review**.

The goal is not to claim that the model predicts Google's ranking algorithm or that refreshing a page will necessarily improve its traffic or rankings.

Instead, the system is designed as a **decision-support tool** that helps analysts identify pages that may deserve attention first.

The project follows the FlyRank ML Internship workflow:

**Problem framing → Data contract → Leakage check → Signal audit → Baseline → Machine learning → Validation → Action playbook**

## Research Question

> **Can an explainable machine learning model prioritize pages that show signals associated with declining search performance better than a simple rule-based baseline?**

The project evaluates this using an anonymized FlyRank dataset and a client-aware holdout evaluation.

## Dataset

The project uses the bundled anonymized FlyRank ML Internship dataset.

- **30,000 pages**
- **44 columns**
- Anonymized/pseudonymized content identifiers
- Search-demand signals
- Impression and click metrics
- Session and engagement metrics
- Content age and freshness signals
- CTR and average position
- Content-size features
- Traffic and behavioral indicators

The dataset does **not** contain publicly exposed client names, domains, URLs, titles, keywords, or credentials.

### Target Label

The target is:

```text
is_declining_label = (trend_direction == "down")

In the modeling pipeline:

trend_direction is used to construct the target.
trend_direction is not used as a model feature.
trend_pct is not used as a model feature.

This prevents direct target leakage.

Methodology
1. Data Preparation

The pipeline prepares the anonymized dataset by:

Removing unusable records
Keeping pages with search impressions
Applying the content-age eligibility rule
Removing duplicate content identifiers
Creating derived numeric features
Creating the declining-performance label
2. Rule-Based Baseline

A transparent baseline score was created before training machine-learning models.

The baseline combines:

Visibility
Freshness risk
Position opportunity
Content-depth gap

The purpose of the baseline is to provide a simple benchmark against which the learned models can be evaluated.

3. Machine Learning Models

Three classification models were evaluated:

Logistic Regression
Decision Tree
Random Forest

The models were evaluated using a client-aware holdout split rather than randomly splitting individual pages.

This helps reduce the possibility that pages from the same client appear in both training and testing data.

4. Evaluation Metrics

The project focuses particularly on ranking-oriented metrics because the practical goal is to prioritize a limited number of pages for review.

Key metrics include:

Precision@20
Precision@50
Precision@100
ROC-AUC
Average Precision
Precision
Recall
F1 score
Accuracy
Results

The final dataset contained:

30,000 pages
16,262 declining-label pages
54.2% declining-label rate

The client-aware split produced:

27,675 training rows
2,325 test rows
Model Comparison
Model	ROC-AUC	Average Precision	Precision@50
Random Forest	0.750	0.618	0.740
Decision Tree	0.742	0.575	0.660
Logistic Regression	0.700	0.522	0.400
Rule-Based Baseline	0.627	0.468	0.240
Main Result

The Random Forest achieved the strongest overall ranking performance.

At the top 50 predictions:

Random Forest Precision@50 = 0.740
Baseline Precision@50      = 0.240

This represents approximately a:

3.08× improvement over the baseline

for Precision@50 on this evaluation split.

The result suggests that the learned model can prioritize declining-label pages more effectively than the simple rule baseline in this dataset.

Important Feature Signals

The strongest Random Forest feature-importance signals included:

Feature	Approx. Importance
Days with impressions	0.158
Log impressions over 90 days	0.129
Average position	0.109
Content age	0.095
Character count	0.043
Word count	0.040
Log clicks over 90 days	0.035
CTR	0.033
Scroll rate	0.031
Days with sessions	0.028

Feature importance indicates which variables contributed strongly to the model's predictions.

It does not establish that any individual feature causes search-performance decline.

Signal Audit

Several signals were independently checked before being used for recommendations.

Volume Signal

Pages with different impression-volume levels showed meaningful differences in decline rates.

Verdict: CONFIRMED

This provides directional evidence that search visibility/volume is useful for prioritization.

CTR and Position

Among sufficiently visible pages, declining pages showed lower median CTR than non-declining pages at comparable visibility/position conditions.

Verdict: CONFIRMED

This supports using CTR and position together as a review signal.

Staleness

A simple assumption that older/unchanged pages are automatically more likely to decline was not supported by this snapshot.

The basic staleness flag produced the opposite directional result in this dataset.

Verdict: OPPOSITE

This is an important negative finding and prevents the project from turning an intuitive assumption into an unsupported recommendation.

Recommended Action Playbook

The model is used to create a ranked review queue.

Suggested actions include:

1. Monitor

Pages with weaker evidence should remain under observation rather than being immediately changed.

2. Refresh

Pages with stronger decline signals can be prioritized for content review and potential updating.

3. Refresh + Review CTR

Pages showing visibility and CTR opportunity should receive additional CTR-focused review.

4. Refresh + Review Engagement

Pages showing engagement-related signals should receive content-quality and user-engagement review.

5. Expand + Refresh

Pages with stronger demand/content-depth opportunities may warrant expansion alongside a refresh.

These actions are recommendations for human review, not automatic instructions to change content.

Why Random Forest?

Random Forest was selected as the final model because it achieved the strongest Precision@50 and overall ranking performance among the evaluated models.

Its advantage is particularly relevant to this project because the operational objective is:

Find the most useful pages to review first.

The model therefore provides a ranked decision-support queue rather than simply producing a binary prediction.
Reproducibility

Clone the repository:

git clone https://github.com/asifbillah845-coder/ml-Internship--fyrank.git
cd ml-Internship--fyrank

Install dependencies:

pip install -r requirements.txt

Run the complete pipeline:

python scripts/run_all.py

The pipeline performs:

01_prepare_features.py
        ↓
02_baseline_score.py
        ↓
03_train_model.py
        ↓
04_evaluate_and_export.py
        ↓
05_build_pdf_report.py

Generated results are written to the appropriate outputs/ locations.

Repository Structure
ml-Internship--fyrank/
│
├── data/
│   └── raw/
│       └── content_refresh_anonymized.csv
│
├── docs/
│   └── index.html
│
├── notebooks/
│
├── outputs/
│   ├── charts/
│   ├── model_report.md
│   └── ...
│
├── scripts/
│   ├── 01_prepare_features.py
│   ├── 02_baseline_score.py
│   ├── 03_train_model.py
│   ├── 04_evaluate_and_export.py
│   ├── 05_build_pdf_report.py
│   └── run_all.py
│
├── submission/
│   └── paper_url.txt
│
├── work/
│   ├── notebooks/
│   ├── capstone.ipynb
│   └── capstone_report.md
│
├── requirements.txt
├── GUIDE.md
├── DATA_USE.md
├── SETUP.md
└── README.md
Capstone Work

The main capstone work is contained in:

work/

This includes the assignment notebooks and final capstone analysis.

Important notebooks include:

w01_research_question.ipynb
w02_ml_task_framing.ipynb
w03_data_contract.ipynb
w03_feature_leakage_check.ipynb
w04_signal_audit.ipynb
w04_baseline_score.ipynb
w05_model.ipynb
w06_validation_audit.ipynb
w07_action_playbook.ipynb
capstone.ipynb

The detailed written report is:

work/capstone_report.md
Public-Safety and Data Rules

This repository follows the FlyRank ML Internship data-use requirements.

The project does not publish:

Client names
Private domains
Private URLs
Search keywords
Private queries
Credentials
Private exports
Other identifying client information

Only anonymized/pseudonymized data appropriate for the internship project is used.

Do not add private client data to this public repository.

Do not paste private client data into third-party AI tools.

Honest Framing

This project should be interpreted as an observational machine-learning analysis and decision-support system.

The results do not prove that:

Google uses these exact signals in its ranking algorithm.
A page will definitely lose rankings because of these signals.
Refreshing a page will definitely increase traffic.
The model predicts Google's ranking algorithm.
The model's feature importance represents causality.

The model identifies patterns associated with the declining label within the available dataset.

Further testing with genuinely future data and controlled interventions would be required to establish whether recommended refresh actions produce measurable improvements.

Limitations
Snapshot Data

The available dataset is a bundled snapshot rather than a true longitudinal experiment.

Therefore, the evaluation does not establish future-time performance under production conditions.

No Causal Evidence

The analysis identifies associations and ranking signals.

It does not prove that changing a page will cause traffic, CTR, position, or engagement to improve.

No Controlled Refresh Experiment

The project does not contain randomized treatment/control groups showing the effect of refreshing content.

Model Generalization

Performance may change on other datasets, clients, industries, search environments, or future periods.

Feature Importance

Random Forest feature importance describes predictive contribution, not causal importance.

Key Takeaway

The main finding is:

An explainable Random Forest model can prioritize pages associated with declining search performance substantially better than the simple rule-based baseline on this anonymized evaluation dataset.

The strongest measured result was:

Precision@50 = 0.740
ROC-AUC      = 0.750

compared with:

Baseline Precision@50 = 0.240
Baseline ROC-AUC      = 0.627

The practical value is therefore in prioritizing human review, not automatically deciding which pages should be changed.

Deliverables
Research Paper

https://asifbillah845-coder.github.io/ml-Internship--fyrank/

Paper URL Submission File
submission/paper_url.txt
Capstone Notebook
work/notebooks/capstone.ipynb
Capstone Report
work/capstone_report.md
Generated Model Report
outputs/model_report.md
Acknowledgment & Data Credit

Built on the FlyRank ML Internship dataset.

This project was completed as part of the FlyRank ML Internship and follows the internship's anonymized-data, reproducibility, and public-safety requirements.

License

The repository code follows the project's included license.

The dataset remains subject to the data-use rules described in:

DATA_USE.md

Please read and follow those rules before using or redistributing any project data.

Author

Asif Billah

FlyRank ML Internship — Capstone Project

Project: Refresh Opportunity Scoring

Focus: Machine Learning · Search Intelligence · Content Refresh Prioritization · Explainable Decision Support
