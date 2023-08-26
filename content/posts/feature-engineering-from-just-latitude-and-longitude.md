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

Many of today's most competitive tech markets involve latitude and longitude, or points on a map: ride-hailing services (Uber, Lyft, Grab), micromobility services (Lime, Lyft, Bird), food delivery services (Delivery Hero, Postsmates, Doordash), and more. Moreover, many services that don't place customers' locations at the center of their product use-cases still want to know their customers' locations so that they can better personalize customers' experiences based on where they are and what's going on around them.

What this all means for data scientists is that there's a lot of latitudes and longitudes floating around our data warehouses and databases; and buried deep inside these two variables alone is a wealth of information!

Knowing how to creatively and effectively utilize the features latitude and longitude can bring immense predictive power to our machine learning applications and added dimensionality to our analytics efforts, helping us data scientists to bring more value to our companies and our customers.

The goal of this article is to give a demontration of a few feature engineering techniques that use just latitude and longitude, comparing their predictive power on an Airbnb Price Prediction problem from Kaggle. The structure is as follows:

1. Airbnb price prediction problem setup
2. Discussion of feature-engineering techniques
    1. raw latitude and longitude (i.e. no feature engineering)
    2. spatial density (could use many things: nearest neighbors, KDE, etc)
    3. geohash category
    4. geohash target encoding
    5. Combination of all features
3. Extensions, Next Steps

## 1. Airbnb Price Prediction; Problem Setup

The problem that these different feature engineering techniques will be tested on throughout this post is Airbnb price prediction. The only raw features used are `"latitude"` and `"longitude"`, and the target is `"price"`, and the feature engineering techniques are compared to one another by way of three different machine learning models: Linear Regression, Ridge Regression, and XGBoost.

```python
import polars as pl
from xgboost import XGBRegressor
from sklearn.linear_model import LinearRegression, Ridge


# Data taken from https://www.kaggle.com/datasets/kritikseth/us-airbnb-open-data
df = (
    pl.scan_csv("../data/AB_US_2023.csv")
    .select(pl.col(["latitude", "longitude", "price"]))
    .collect()
)
```

We'll furthermore limit to just data from New York City to limit the scope of the problem. This is already an operationally assimilated treatment of the problem: for example, in Delivery Hero, our ML products typically train and serve a different model for each of 20+ countries in which we operate, and often even a different model for each city.

```python
df = df.filter(pl.col("city") == "New York City")
```
--> Insert map of prices.

However, we need to inspect the target variable; after all, monetary variables like "price" or "income" are often log-normally distributed or at least heavily right-skewed, and so it might behoove us to first transform the target variable to log-space:

```python
df = df.with_columns(pl.col("price").log1p().suffix("_log1p"))
```

![Distribution of Airbnb Price](/images/distribution-of-airbnb-price.png)

Even after a [`log_1p` transformation](https://pola-rs.github.io/polars/py-polars/html/reference/expressions/api/polars.Expr.log1p.html#polars.Expr.log1p), the target is still slightly right-skewed. Nonetheless, it looks sufficient for our use-case.

Now, we just need to separate `train` and `test` data, and everything should be good to go:

```python
TRAIN_TEST_SPLIT_FRACTION = 0.8

df = (
    df
    .with_row_count(name="row_number")
    .with_columns([
        (pl.col("row_number") < TRAIN_TEST_SPLIT_FRACTION * len(df)).alias("is_train")
    ])
)
```

We've prepared everything we need to, so without further ado, let's engineer some features 🚀

## 2. Discussion of Feature Engineering Techniques

### 2.1. Raw Latitude and Longitude
The first feature engineering technique is... you guessed it -- no feature engineering! Latitude and longitude can be quite powerful features on their own, though their behavior as such depends highly on the model being used. In particular, you wouldn't usually expect latitude or longitude to have a linear relationship with your target variable, unless your target is something earthly in nature, like rainfall or temperature); with this, raw latitude and longitude won't play so well with linear models like `LinearRegression`; they can however already be quite powerful with e.g. models based on decision trees like `XGBoost`:

### 2.2. Spatial Density
Are Airbnb's in urban areas more expensive than those in rural areas? Population density is related to many demographic processes, especially those related to prices and incomes. And even though this problem focuses on New York City where there's no rural areas, but rather only urban areas and extremely urban places, it still might be useful.

We could use many methods for measuring the spatial density of an Airbnb's neighborhood: computing the number of nearest neighbors, 



---

{{< contact message="Liked what you read? Feel free to reach out on " >}}