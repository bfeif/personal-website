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

## Look at All That Space!

Many of today's most competitive tech markets involve points moving around on a map: ride-hailing services (Uber, Lyft, Grab), micromobility services (Lime, Bird), food delivery services (Delivery Hero, Postsmates, Doordash), and more. Moreover, many services that don't place customers' locations at the center of their product use-cases still want to know their customers' locations so that they can better personalize their experiences based on where they are and what's going on around them.

What this all means for data scientists is that there's a lot of latitudes and longitudes floating around our data warehouses and databases; and buried deep inside these two variables alone is a wealth of information!

Creatively and effectively utilizing latitude and longitude can bring immense predictive power to our machine learning applications and added dimensionality to our analytics efforts, helping us data scientists to bring more value to our companies and our customers.

The goal of this article is to give a demontration of a few feature engineering techniques that use just latitude and longitude, comparing their predictive power on an Airbnb Price Prediction problem from Kaggle. The structure is as follows:

1. Airbnb price prediction problem setup
2. Discussion of feature-engineering techniques
    1. raw latitude and longitude (i.e. no feature engineering)
    2. spatial density (could use many things: nearest neighbors, KDE, etc)
    3. geohash category
    4. geohash target encoding
    5. Combination of all features
3. Extensions, Next Steps

As a final preliminary note, this post will use Polars as a data manipulation library, as opposed to Pandas; if you, dear reader, are unfamiliar with Polars or otherwise still stuck in Panda-land, feel free to first check out my earlier post, ["The 3 Reasons Why I Have Permanently Switched From Pandas To Polars"](https://benfeifke.com/posts/the-3-reasons-why-i-switched-from-pandas-to-polars-20230328/). 

And now, let's go 🚀

## 1. Airbnb Price Prediction; Problem Setup

The problem that these different feature engineering techniques will be tested on throughout this post is Airbnb price prediction, coming from a publicly available dataset "U.S. Airbnb Open Data" \[1\]. The only raw features used are `"latitude"` and `"longitude"`; the target is `"price"`; and the feature engineering techniques are compared to one another by way of two different machine learning models: Ridge Regression and XGBoost.

```python
import polars as pl
from xgboost import XGBRegressor
from sklearn.linear_model import Ridge


# Data taken from https://www.kaggle.com/datasets/kritikseth/us-airbnb-open-data
df = (
    pl.scan_csv("../data/AB_US_2023.csv")
    .select(pl.col(["latitude", "longitude", "price"]))
    .collect()
)
```

We'll furthermore focus on just data from New York City in order to limit the scope of the problem.

<!-- In fact, this is already an operationally assimilated treatment of geospatial data problems: for example, in Delivery Hero, our ML products typically train and serve a different model for each of 20+ countries in which we operate, and often even a different model for each city. -->

```python
df = df.filter(pl.col("city") == "New York City")
```

Before getting started with any ML prediction, though, we need to inspect the target variable; after all, monetary variables like "price" and "income" are often log-normally distributed or at least heavily right-skewed, and so it might behoove us to first transform the target variable to log-space:

```python
df = df.with_columns((pl.col("price") + 1).log10().suffix("_log10"))
```

<figure class="image" align="center">
    <img src="/images/distribution-of-airbnb-price.png" alt="drawing"/>
    <figcaption style="font-style: italic">
        <a href="https://benfeifke.com/posts/ecdf-the-only-plotting-tool-a-data-scientist-needs/">ECDF</a> and histogram of Airbnb prices. The most expensive is $100,000 a night! | Image by Author
    </figcaption>
</figure>

Even after a `log` transformation, the target is still slightly right-skewed. Nonetheless, it looks sufficient for our use-case.

<figure class="image" align="center">
    <img src="/images/nyc-airbnb-prices.png" alt="drawing"/>
    <figcaption style="font-style: italic">
        Airbnb prices in New York City; rentals near the south end of Manhattan appear to be the most expensive. PS: sorry, Staten Island. | Image by Author
    </figcaption>
</figure>

Now, we just need to add a column to distinguish `train` and `test` data, and everything should be good to go:

```python
TRAIN_TEST_SPLIT_FRACTION = 0.8

df = (
    df

    # Shuffle the data to avoid any issues from the data already being ordered...
    .sample(fraction=1, shuffle=True)
    
    # ...then use row numbers as an index for separating train and test.
    .with_row_count(name="row_number")
    .with_columns([
        (pl.col("row_number") < TRAIN_TEST_SPLIT_FRACTION * len(df)).alias("is_train")
    ])
)
```

We've prepared everything we need to, so without further ado, let's engineer some features 🚀

## 2. Discussion of Feature Engineering Techniques

### 2.1. Raw Latitude and Longitude
The first feature engineering technique is... you guessed it -- no feature engineering! Latitude and longitude can be quite powerful features on their own, though their behavior as such depends highly on the model being used. In particular, you wouldn't usually expect latitude or longitude to have a linear relationship with your target variable, unless your target is something earthly in nature, like "temperature" or "humidity"; with this, raw latitude and longitude won't play so well with linear models like `RidgeRegression`; they can however already be quite powerful with e.g. models based on decision trees like `XGBoost`:

```python
MODEL_FEATURE_LIST_NAME = "raw_lat_lon"
MODEL_FEATURE_LIST = ["latitude", "longitude"]

X_train = df.filter(pl.col("is_train")).select(MODEL_FEATURE_LIST)
y_train = df.filter(pl.col("is_train")).select(MODEL_TARGET)
X_test = df.filter(~pl.col("is_train")).select(MODEL_FEATURE_LIST)
y_test = df.filter(~pl.col("is_train")).select(MODEL_TARGET).to_numpy()

model_performance_list = []
for model_name, model_class in zip(
    ["xgboost", "ridge regression"],
    [XGBRegressor, Ridge]
):
    model = model_class().fit(X_train, y_train)
    y_predicted = model.predict(X_test)
    model_performance = mean_squared_error(y_test, y_predicted)
    model_performance_list.append({
        "feature_list_name": MODEL_FEATURE_LIST_NAME,
        "model_name": model_name,
        "model_performance": model_performance
    })
```
<style type="text/css">
#T_09dff_row0_col0, #T_09dff_row0_col1 {
  background-color: #023858;
  color: #f1f1f1;
}
</style>
<table id="T_09dff">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_09dff_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_09dff_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_09dff_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_09dff_row0_col0" class="data row0 col0" >$2.13</td>
      <td id="T_09dff_row0_col1" class="data row0 col1" >$1.98</td>
    </tr>
  </tbody>
</table>

The intuition was correct: `XGBoost` has a lower `mean_squared_error` than `RidgeRegression`.


### 2.2. Spatial Density
Do Airbnbs in urban areas have higher prices than those in rural areas?

Population density is correlated with many demographic processes, and this is certainly true for prices and incomes (e.g. people earn higher salaries in cities than in the countryside). And even though this problem focuses on New York City where there's only urban areas and extremely urban areas, a spatial density feature might still be useful.

We could use many methods for measuring the spatial density of an Airbnb's neighborhood: counting the number of neighbors within some radius for each Airbnb; computing and sampling from a Kernel Density Estimate over Airbnb locations; or measuring the number of total Airbnbs in each Airbnb's residing geohash. For this case, we measure spatial density as the number of neighbors within some radius for each Airbnb, using `scipy`'s [cKDtree](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.cKDTree.html):

```python
def add_density_feature_columns_to_dataframe(geo_df: pl.DataFrame) -> pl.DataFrame:
    tree = spatial.cKDTree(df.select(["latitude", "longitude"]))
    result = geo_df.with_columns(
        pl.Series(
            "density",
            tree.query_ball_point(geo_df.select(["latitude", "longitude"]), .02, return_length=True)
        )
    )
    return result


df_w_density = add_density_feature_columns_to_dataframe(df)

MODEL_FEATURE_LIST_NAME = "spatial_density"
MODEL_FEATURE_LIST = ["spatial_density"]

X_train = df_w_density.filter(pl.col("is_train")).select(MODEL_FEATURE_LIST)
y_train = df_w_density.filter(pl.col("is_train")).select(MODEL_TARGET)
X_test = df_w_density.filter(~pl.col("is_train")).select(MODEL_FEATURE_LIST)
y_test = df_w_density.filter(~pl.col("is_train")).select(MODEL_TARGET).to_numpy()

model_performance_list = []
for model_name, model_class in zip(
    ["xgboost", "ridge regression"],
    [XGBRegressor, Ridge]
):
    model = model_class().fit(X_train, y_train)
    y_predicted = model.predict(X_test)
    model_performance = mean_squared_error(y_test, y_predicted)
    model_performance_list.append({
        "feature_list_name": MODEL_FEATURE_LIST_NAME,
        "model_name": model_name,
        "model_performance": model_performance
    })

spatial_density_results_df = (
    pd.DataFrame
    .from_records(model_performance_list)
    .pivot_table(
        index="feature_list_name",
        columns="model_name",
        values="model_performance",
        aggfunc="first"
    )
)
```

<style type="text/css">
#T_55b6c_row0_col0, #T_55b6c_row0_col1, #T_55b6c_row1_col0, #T_55b6c_row1_col1 {
  background-color: #023858;
  color: #f1f1f1;
}
</style>
<table id="T_55b6c">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_55b6c_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_55b6c_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_55b6c_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_55b6c_row0_col0" class="data row0 col0" >2.130</td>
      <td id="T_55b6c_row0_col1" class="data row0 col1" >1.977</td>
    </tr>
    <tr>
      <th id="T_55b6c_level0_row1" class="row_heading level0 row1" >spatial_density</th>
      <td id="T_55b6c_row1_col0" class="data row1 col0" >1.280</td>
      <td id="T_55b6c_row1_col1" class="data row1 col1" >1.273</td>
    </tr>
  </tbody>
</table>

And the results make sense: spatial density outperforms raw latitude and longitude for the regression model, but underperforms raw latitude and longitude for XGBoost.

### 2.3. Geohash Categorical

Different neighborhoods are more expensive than others, so some sort of neighborhood-related categorical feature could be a powerful predictor.

There are many ways of creating such a feature -- with spatial clustering (e.g. k-means, hierarchical), with spatial segmentation (e.g. geohash, quad-trees, s2-cells), or even using government-created zipcodes.

Here, we'll use geohash, due to its simplicity

## References

\[1\]: https://www.kaggle.com/datasets/kritikseth/us-airbnb-open-data

---

{{< contact message="Liked what you read? Feel free to reach out on " >}}