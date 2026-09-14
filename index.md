# Predicting Organic Impression Decay: A Machine Learning Approach to Content Refresh Optimization
**Author:** Mehroz Ali

## Abstract
Organic traffic decay is a critical issue for digital publishers, yet content refreshes are traditionally executed reactively after visibility has already plummeted. This research investigates whether historical search performance signals can proactively identify pages at high risk of impression decay. Using the FlyRank Search Intelligence warehouse via DuckDB, we extracted temporal features and trained a predictive Random Forest classifier. The resulting model significantly outperformed a heuristic baseline, achieving a precision of ~0.72 compared to the baseline's ~0.24. By scoring pages on their likelihood of impending decline, content teams can deploy targeted updates before severe visibility loss occurs.

## Introduction / Problem Statement
Content lifecycle management currently relies on lagging indicators, meaning teams only notice a page is dead when the traffic graph flatlines. The business decision this work supports is resource allocation: determining exactly which URLs a content team should rewrite or merge this week to maximize ROI. By shifting to a predictive opportunity scoring engine, organizations can protect their organic search moats and automate the triage of decaying assets.

## Data
The analysis utilizes the official FlyRank ML Internship warehouse dataset hosted on Hugging Face.
*   **Tables Queried:** `fact_content_daily_performance` and `dim_clients`.
*   **Date Windows:** A 60-day historical observation window (March - April 2026) was used to predict outcomes in a 30-day future target window (May 2026).
*   **Exclusions:** To ensure public safety and privacy compliance, all raw client domains, URL strings, and proprietary credentials were obfuscated. Queries with extremely low search volumes (under 50 impressions in the first period) were excluded to reduce noise.

## Methodology
Data extraction was executed using DuckDB over Hugging Face's `hf://` endpoints to efficiently aggregate rows into a working pandas DataFrame.
*   **Features:** Historical impressions, CTR variance, and 30-day impression momentum (the ratio of impressions in period 2 vs period 1).
*   **Label Definition:** A binary classification where `1` indicates a page experienced a >20% drop in impressions in the target future window, and `0` indicates stability or growth.
*   **Validation Design:** A standard time-aware split (80/20 train-test split) was utilized to prevent temporal data leakage, ensuring the model generalizes to unseen data.
*   **Baseline:** A hardcoded heuristic baseline was established for comparison, flagging pages where momentum was dropping (<0.9) and historical CTR was low (<3%).

## Results
The trained Random Forest model successfully identified at-risk pages with significantly higher precision than the baseline heuristic rules.
*   **Baseline Performance:** The heuristic rule achieved a precision of approximately 0.24, flagging many false positives due to normal seasonal variance. 
*   **Model Performance:** The ML model achieved a precision of approximately 0.72, effectively isolating structural decay from normal fluctuations and providing a ~3x lift in accuracy.

## Limitations & Honest Framing
This model provides **directional, decision-support scoring only**. It does not prove or reverse-engineer Google's algorithm, nor can it claim causal impact (i.e., we cannot guarantee that refreshing the page will restore the exact number of lost impressions). The scores represent historical correlations associated with visibility drops and should be used as a prioritization tool, not an absolute truth.

## Ranked Recommendations
Based on the model outputs, the following action playbook is recommended for content triage:
1.  **Critical Score (0.80 - 1.00):** Rewrite immediately. These pages are exhibiting severe, sustained position drift and CTR collapse.
2.  **Warning Score (0.50 - 0.79):** Improve/Tweak. The page is losing relevance. Recommend updating metadata, checking for broken links, or adding fresh data.
3.  **Stable Score (< 0.49):** Protect & Monitor. Do not touch the core content.

## Reproducibility & Acknowledgments
*   **Code:** [https://github.com/Mehroze-Ali/flyrank-ml-internship](https://github.com/Mehroze-Ali/flyrank-ml-internship)
*   **Acknowledgments:** Built on the FlyRank ML Internship dataset (https://flyrank.ai)
