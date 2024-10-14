---
title: "Bootstrapping for Offline Machine Learning Evaluation"
date: 2024-10-13T09:47:45+02:00
draft: false
tags:
    - geospatial
    - data-science
    - data
    - software
    - python
keywords:
    - geospatial
    - data-science
    - data
    - software
    - python
---

***After reading this, you'll never use k-fold cross-validation again.***

"Wow! This ML model is showing auspicious performance, but how can you guarantee its performance on production data?"

This is what a client recently asked me in response to an ML Proof of Concept analysis I did for them, and I struggled to answer:

"Well, based on hyperparameter tuning, this model is the most performant, and it achieves a 10-fold cross-validation accuracy-score of X, which is Y% better than the untuned model…"

I couldn't even convince myself! How could I convince the client? Was k-fold cross-validation not enough?

I needed something more.

See, In some technical organizations, the cost of online experimentation is low, and one successful offline experiment is enough to warrant an online one, especially when it's in alignment with a product vision; however, in organizations such as my client's, the cost of experimentation is very high. In general, this can be because of human resource costs, because of lengthy durations of live experiments, or something else; in my client's case, live experimentation was expensive because it was his organization's first foray into machine learning, and integrating ML into the product would take a lot of time and human resources to achieve.

When the cost of live experimentation is high, then we ought to turn back and add sophistication to our offline experimentation, to, if possible, make statistically rigorous statements like "In production, I expect the model to improve on the status quo performance by X-i to X+1%, with 95% confidence".

This is not something that k-fold cross-validation can offer us, but it's something that bootstrapping can.

This blog post describes such an approach for using bootstrapping to compare two models in offline evaluation, and make such statistically rigorous statements about their performance comparison, by executing hypothesis testing on the bootstrapped distributions of model performances. It will demonstrate this with code, on a problem of predicting Miami Home Sale Price using different geospatial feature engineering techniques.

The code and feature engineering techniques analyzed come from a prior blog post of mine, "Feature Engineering With Latitude and Longitude", though that is not required reading before this one!

This blog post will cover the following topics:

* Brief Primer on Bootstrapping
* Problem Setup: Miami Home Sale Price Prediction
* Bootstrapping ML Comparison
* Discussion

Let's get into it!


## Brief Primer on Bootstrapping

In short, bootstrapping is a statistical technique which can be used to estimate the sampling distribution of an estimator or statistic. It's particularly useful when the data for experimentation is limited, or when you want to estimate confidence intervals for your statistic in question (in our case, machine learning model performance).

It works by the following way:

1. Start with an original sample of data;
2. Repeatedly draw samples (with replacement) from this original sample;
3. Calculate the statistic of interest for each resampled dataset;
4. Use the distribution of these statistics across the different resamples to infer confidence intervals and more about the statistic.

<figure class="image" align="center">
    <img src="/images/bootstrapping_explained.png"/>
    <figcaption style="font-style: italic">Boostrapping explained. | Image from Wikipedia. To replace!</figcaption>
</figure>

In bootstrapping, the critical piece is with replacement. By sampling with replacement, bootstrapping simulates real-world sampling, in which an original sample of data is usually created by taking independent samples from a theoretically infinite population. If instead data was sampled without replacement, then the distribution of the original sample data changes after every individual sample, making the sampling not truly independent!

In this case, the statistic to be measured on each bootstrapped resample is machine learning model performance:

<figure class="image" align="center">
    <img src="/images/bootstrapping_explained.png"/>
    <figcaption style="font-style: italic">Modify the image for ML experimentation! | Image from Wikipedia. To replace!</figcaption>
</figure>

As with any bootstrapping experiment, once completed, we'll be able to statistically reason about the machine learning model's performance, and what it means for what to expect in a production experiment.

Enough discussion, let's get coding!

## Problem Setup: Miami Home Sale Price Prediction

The problem upon which this bootstrapping demo will be performed is Home Sale Price Prediction in Miami, coming from a publicly available dataset "Miami Housing 2016" [1].

<figure class="image" align="center">
    <img src="/images/miami-home-sale-price.png"/>
    <figcaption style="font-style: italic">Home sale prices in Miami; houses close to the beach generally sold for more money, with the wealthy neighborhood of Pinecrest also being a hot spot. | Image by Author</figcaption>
</figure>

My earlier post working with this dataset attempted to predict "log(price)" using only features engineered from "latitude" and "longitude". In the simplest case of using just "latitude" and "longitude" without any feature engineering, XGBoost turned out to be far more performant than RidgeRegression.

Let's begin the demo by reproducing that result.

We start by loading the data, transforming "price" to "log(price)", and adding a column to indicate train vs test data:


```python
import polars as pl
from xgboost import XGBRegressor
from sklearn.linear_model import Ridge

TRAIN_TEST_SPLIT_FRACTION = 0.8

# Data taken from https://www.openml.org/search?type=data&id=43093
df = (
    pl.read_csv("../data/miami-housing.csv")
    .with_columns([
        pl.col("SALE_PRC").alias("price"),
        pl.col(["LATITUDE", "LONGITUDE"]).name.to_lowercase()
    ])
    .with_columns([
        pl.col("price").log10().name.suffix("_log10")
    ])
    .with_row_index(name="index")
    .select([
        pl.col("index").mod(10).lt(TRAIN_TEST_SPLIT_FRACTION * 10).alias("is_train"),
        "latitude",
        "longitude",
        "price",
        "price_log10"
    ])
)
```

Then, we train and evaluate both an XGBoost model and a RidgeRegression model, using Root Mean Square Error (RMSE) as the evaluation function:


```python
MODEL_TARGET = "price_log10"
MODEL_FEATURE_LIST = ["latitude", "longitude"]

X_train = df.filter(pl.col("is_train"))
y_train = df.filter(pl.col("is_train"))
X_test = df.filter(~pl.col("is_train"))
y_test = df.filter(~pl.col("is_train"))

for model_name, model_class in zip(
    ["xgboost", "ridge regression"],
    [XGBRegressor, Ridge]
):
    model = model_class().fit(
        X_train.select(MODEL_FEATURE_LIST),
        y_train.select(MODEL_TARGET)
    )
    y_predicted = model.predict(X_test.select(MODEL_FEATURE_LIST))
    model_performance = root_mean_squared_error(y_test.select(MODEL_TARGET), y_predicted)
```

Survey says...

<style type="text/css">
#T_fbc5b_row0_col0 {
  background-color: #023858;
  color: #f1f1f1;
}
#T_fbc5b_row0_col1 {
  background-color: #eee9f3;
  color: #000000;
}
</style>
<table id="T_fbc5b">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_fbc5b_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_fbc5b_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_fbc5b_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_fbc5b_row0_col0" class="data row0 col0" >0.24179</td>
      <td id="T_fbc5b_row0_col1" class="data row0 col1" >0.11113</td>
    </tr>
  </tbody>
</table>

XGBoost has a lower RMSE than RidgeRegression for this problem! This is the same as the result in my earlier post, though notably, just by using a different function for setting train aside from test, already the exact results are slightly different.

And on that note, let's bring statistical rigor to this evaluation--using the bootstrap method 😎

## Bootstrapping ML Comparison

To begin, we create a function for executing one experiment run of a

## References

\[1\]: https://www.openml.org/search?type=data&id=43093

---

{{< contact message="Liked what you read? Feel free to reach out on " >}}