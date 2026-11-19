# Credit card fraud detection



Fraud detection model on the Kaggle credit card dataset, built with a fraud operations mindset: the goal isn't just a good score, it's choosing an alert threshold that a review team could actually work with and showing how much money it stops.

I work in fraud prevention, so most of the effort went into the parts that matter in practice: handling a 0.17% fraud rate, not leaking information from the test set, and translating the result into money and alert volume.

## What's in the notebook

The analysis is in [`fraud\_detection.ipynb`](fraud_detection.ipynb).

1. Data cleaning (the dataset has 1,081 duplicate rows that would otherwise leak between train and test).
2. Train / validation / test split (60/20/20, stratified).
3. Comparison of logistic regression, random forest and XGBoost using PR-AUC on validation.
4. Threshold selection on validation.
5. A single final evaluation on test, plus fraud amount stopped vs missed and number of alerts.

## Results

Test set, evaluated once with the threshold chosen on validation.

|Metric|Value|
|-|-|
|Best model|Random forest (tied with XGBoost on validation)|
|Threshold|0.35|
|PR-AUC|0.857|
|Recall (fraud caught)|77% (73 of 95)|
|Precision (alerts that are real fraud)|95%|
|False alarms|4 out of 56,651 legitimate transactions|
|Fraud amount stopped|$9,638 of $12,973 (74%)|

In practice: 77 alerts for the review team, 73 of them real fraud.

## Run it

The dataset is too big for GitHub, so download `creditcard.csv` from [Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud) and put it next to the notebook.

```
pip install -r requirements.txt
jupyter notebook fraud\_detection.ipynb
```

It also runs on Google Colab: upload the notebook and the CSV and run all cells.

## Limitations

* Only 95 fraud cases in the test set, so the metrics move several points with just a few transactions.
* Random split instead of a time-based one, which is more optimistic than real production use.
* V1 to V28 are anonymized PCA components, so there's no way to engineer features or design rules based on business logic. A rules vs. model comparison on a dataset with raw transaction fields is what I want to do next.

