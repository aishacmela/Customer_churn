# Customer Churn Prediction Project

## Modeling Results & Conclusion

### Summary
This project set out to build a churn prediction model to help the marketing team identify at-risk customers for a targeted, low-cost re-engagement campaign (sending trailers/previews of upcoming content). After a thorough exploratory data analysis, feature engineering process, and testing across three model families, the conclusion is that the available features in this dataset carry little to no learnable signal for predicting churn. This finding is well-triangulated across independent methods and is documented in detail below.

### Modeling Approach
Three model families were tested, following a start-simple-then-escalate approach:

- **Logistic Regression** — a linear, fully interpretable baseline.
- **Decision Tree** — a single non-linear model capable of capturing simple interaction effects.
- **Random Forest (200 trees)** — an ensemble method capable of capturing non-linear patterns and feature interactions that neither correlation analysis nor a single tree can reliably detect.

All models were evaluated using a held-out 20% test set (stratified to preserve the original class balance), never on training data. Because Logistic Regression is scale-sensitive, it was trained on standardized numeric features; the tree-based models were trained on the unscaled data, since they do not require feature scaling.

### Baseline for Comparison
The target variable, Churn Status, is close to balanced: approximately 53.9% of customers churned ("Yes") and 46.1% did not ("No"). This means a naive model that always predicts the majority class ("churn") would achieve roughly 54% accuracy without learning anything from the data at all. This naive baseline is the true bar every model needs to clear to be considered useful.

### Results

| Model | Test Accuracy |
|---|---|
| Naive baseline (majority class) | ~54.0% |
| Logistic Regression | 48.0% |
| Decision Tree | 44.5% |
| Random Forest (200 trees) | 53.5% |

Logistic Regression and the Decision Tree both performed worse than simply guessing the majority class every time. Random Forest was essentially tied with the naive baseline, meaning it avoided performing worse than chance but did not demonstrate that it had learned a meaningfully predictive pattern.

### Feature Importance (Random Forest)
Examining which features the Random Forest relied on most heavily provided further confirmation of the weak-signal finding. Importance scores across the top 10 features ranged narrowly from about 0.040 to 0.097, with no feature standing out as a dominant driver of churn. In a dataset with strong predictive signal, one would typically expect one or two features to show a clear, substantially higher importance score with a steep drop-off to the rest — that pattern was not observed here.

Engineered features (watch time per profile, support queries per month of tenure, and promotional offers per month of tenure) performed comparably to the raw features they were derived from, without introducing a dominant new signal.

### Supporting Evidence from Earlier Analysis
This modeling result is consistent with, and reinforced by, findings from earlier stages of the project:

- **Correlation analysis:** no numeric feature showed a correlation with churn greater than ±0.06 in absolute value.
- **Categorical analysis:** churn rates were nearly identical across Subscription Plan tiers (Basic 54.9%, Standard 53.4%, Premium 53.5%), a spread of under 2 percentage points.
- **Domain-driven ratio features:** correlations with churn remained weak (±0.03 or less).
- **Interaction terms:** the strongest of three tested interaction flags reached only a 0.074 correlation with churn.

Three independent lines of evidence — linear correlation, multiple model families, and feature importance — all point to the same conclusion.

### Conclusion
The available features in this dataset do not contain a strong learnable signal for predicting customer churn, whether analyzed through linear correlation, domain-driven feature engineering, or a range of machine learning models from simple to ensemble-based. This is a legitimate and valuable finding: some datasets, particularly synthetic ones built for practice purposes, generate churn labels with little or no true dependency on the accompanying feature columns. Reaching this conclusion through a rigorous, staged process — business framing, data understanding, exploratory analysis, feature engineering, and multi-model evaluation against an honest naive baseline — is itself the core value of this project.

### Recommendations for Future Work
- If richer data becomes available (e.g., actual usage timestamps, content-level engagement, customer service transcript sentiment, or billing event history), revisit modeling with these additional features, since the current dataset's static, coarse-grained snapshot may be limiting predictive power.
- Try gradient boosting methods (XGBoost, LightGBM) as a final confirmatory test, though based on the Random Forest result, a dramatically different outcome is not expected.
- If deployed in a real business setting, pair any future model with qualitative research (customer surveys, support call analysis) to identify churn drivers that may not be captured in structured, numeric data.
