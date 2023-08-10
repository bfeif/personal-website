---
title: "Feature Engineering From Just Latitude and Longitude"
date: 2023-07-27T09:47:45+02:00
draft: false
tags:
    - refactoring
    - programming
    - data-science
    - software
    - python
keywords:
    - refactoring
    - programming
    - data-science
    - software
    - python
---
***Quote***

## Look at All That Space!

Insert Intro

The goal of this article is to give a demonstration of feature engineering from just latitude and longitude, including ML performance comparisons, hopefully sporing ideas for other techniques for feature-engineering from latitude and longitude.

Without further ado... this article demonstrates four feature-engineering techniques that use just latitude and longitude, and compare their predictive power on an Airbnb Price Prediction problem from Kaggle. The strucure is as follows:
1. Discussion of feature-engineering techniques
    1. raw latitude longitude (i.e. no feature engineering)
    2. spatial density (could use many things: nearest neighbors, KDE, etc).
    3. geohash category
    4. geohash target encoding
2. Comparison of different feature-engineering techniques on Airbnb price prediction task, using linear regression and XGBoost
3. Extensions and Next Steps

## Now Featuring... Latitude and Longitude!

The problem that these different feature engineering techniques will be tested on throughout this post is Airbnb price prediction, i.e. where the `X` is `["latitude", "longitude"]`, and the `y` is `"price"`.

```python
import polars as pl


# Data taken from https://www.kaggle.com/datasets/kritikseth/us-airbnb-open-data
df = (
    pl.scan_csv("../data/AB_US_2023.csv")
    .select(pl.col(["latitude", "longitude", "price"]))
    .collect()
)
```

Without further ado, let's engineer some features 🚀

### Raw Latitude and Longitude
The first feature engineering technique is... you guessed it -- no feature engineering! Latitude and longitude can be quite powerful features on their own, though their manifestation as such depends highly on the model being used. In most problems, you wouldn't expect that latitude or longitude to have a linear relationship with your target variable (unless of course if your target is something earthly, like rainfall or temperature); with this, raw latitude and longitude won't play so well with linear models like `LinearRegression`, but they can already be quite powerful with e.g. decision-tree-based models like `XGBoost`:

###

---

{{< contact message="Liked what you read? Feel free to reach out on " >}}