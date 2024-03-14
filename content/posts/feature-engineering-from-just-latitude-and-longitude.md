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

***Leveraging the power in your geospatial data--with code!***

Many of today's most competitive tech markets involve points moving around on a map: ride-hailing services (Uber, Lyft, Grab), micromobility services (Lime, Bird), food delivery services (Delivery Hero, Postsmates, Doordash), and more. Moreover, many services that don't place customers' locations at the center of their product use-cases still want to know their customers' locations so that they can better personalize their experiences based on where they are and what's going on around them.

What this all means for data scientists is that there's a lot of latitudes and longitudes floating around our data lakes (pun intended); and buried deep inside just these two variables is a wealth of information!

Creatively and effectively utilizing latitude and longitude can bring immense predictive power to our machine learning applications and added dimensionality to our analytics efforts, helping us data scientists to bring more value to our companies and our customers.

The goal of this article is to give a demontration of a few feature engineering techniques that use just latitude and longitude, comparing their predictive power on an Airbnb Price Prediction problem from Kaggle. The structure is as follows:

1. Airbnb price prediction problem setup
2. Discussion of feature-engineering techniques
    1. Raw latitude and longitude
    2. Spatial density
    4. Geohash target encoding
    5. Combination of all features
3. Extensions, Next Steps

Since the focus of this post is on feature-engineering, the model evaluation will be quite straightforward for the sake of brevity and clarity (i.e. no cross validation and no hyperparameter optimization).

Furthermore, this post will use Polars as a data manipulation library, as opposed to Pandas; if you, dear reader, are unfamiliar with Polars or otherwise find yourself still stuck in Panda-land, feel free to first check out my earlier post, ["The 3 Reasons Why I Have Permanently Switched From Pandas To Polars"](https://benfeifke.com/posts/the-3-reasons-why-i-switched-from-pandas-to-polars-20230328/).

And now, let's go 🚀

## 1. Airbnb Price Prediction: Problem Setup

The problem that these different feature engineering techniques will be tested on throughout this post is Airbnb price prediction, coming from a publicly available dataset "U.S. Airbnb Open Data" \[1\]. The only raw features used are `"latitude"` and `"longitude"`; the target is `"price"`; and the feature engineering techniques are compared to one another by way of two different machine learning models: Ridge Regression and XGBoost (one regression model and one tree-based model).

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

We'll furthermore focus on just data from New York City in order to limit the scope of the problem. In fact, doing so is already an operationally assimilated treatment of geospatial data problems: in industry, it's good practice when developing ML products to train and serve a different model for every region or city of operation, so long as your data volume allows it.

```python
df = df.filter(pl.col("city") == "New York City")
```

Before getting started with any ML prediction, though, it's necessary to inspect the target variable; after all, monetary variables like "price" and "income" are often log-normally distributed or at least heavily right-skewed, and so it might behoove us to first transform the target variable to log-space:

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

Now, I just need to add a column to distinguish `train` and `test` data, and everything should be good to go:

```python
TRAIN_TEST_SPLIT_FRACTION = 0.8

df = (
    df

    # Shuffle the data to avoid any issues from the data being pre-ordered...
    .sample(fraction=1, shuffle=True)
    
    # ...then use row numbers as an index for separating train and test.
    .with_row_count(name="row_number")
    .with_columns([
        (pl.col("row_number") < TRAIN_TEST_SPLIT_FRACTION * len(df)).alias("is_train")
    ])
)
```

And with that, everything is prepared! So without further ado, let's engineer some features 🧑‍💻

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
    model_performance = root_mean_squared_error(y_test, y_predicted)
```

<style type="text/css">
#T_62e3c_row0_col0 {
  background-color: #1b7eb7;
  color: #f1f1f1;
}
#T_62e3c_row0_col1 {
  background-color: #d0d1e6;
  color: #000000;
}
</style>
<table id="T_62e3c">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_62e3c_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_62e3c_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_62e3c_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_62e3c_row0_col0" class="data row0 col0" >0.3695</td>
      <td id="T_62e3c_row0_col1" class="data row0 col1" >0.3252</td>
    </tr>
  </tbody>
</table>

The intuition was correct: `XGBoost` has a lower `root_mean_squared_error` than `RidgeRegression`.


### 2.2. Spatial Density
Do Airbnbs in urban areas have higher prices than those in rural areas?

Population density is correlated with many demographic processes, and this is certainly true for rental prices and incomes (e.g. people earn higher salaries in cities than in the countryside). And even though this problem focuses on New York City where there's only urban areas and extremely urban areas, a spatial density feature might still be useful.

One could use many methods for measuring the spatial density around an Airbnb: counting the number of other Airbnbs within some radius for each Airbnb; or computing and sampling from a Kernel Density Estimate over Airbnb locations. For this case, I measure spatial density as the number of neighbors within some radius for each Airbnb, using `scipy`'s [cKDtree](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.cKDTree.html):

```python
def add_density_feature_columns_to_dataframe(geo_df: pl.DataFrame) -> pl.DataFrame:
    tree = spatial.cKDTree(df.select(["latitude", "longitude"]))
    result = geo_df.with_columns(
        pl.Series(
            "spatial_density",
            tree.query_ball_point(geo_df.select(["latitude", "longitude"]), .005, return_length=True)
        )
    )
    return result


df_w_density = add_density_feature_columns_to_dataframe(df)
```

<figure class="image" align="center">
    <img src="/images/nyc-airbnb-spatial-density.png" alt="drawing"/>
    <figcaption style="font-style: italic">
        Airbnb spatial density; the highest density of Airbnbs is around Times Square, the tourism center of the city. | Image by Author
    </figcaption>
</figure>

And now plugging this new feature into model training...
```python
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
    model_performance = root_mean_squared_error(y_test, y_predicted)
```

<style type="text/css">
#T_e567a_row0_col0 {
  background-color: #1b7eb7;
  color: #f1f1f1;
}
#T_e567a_row0_col1 {
  background-color: #d0d1e6;
  color: #000000;
}
#T_e567a_row1_col0 {
  background-color: #2a88bc;
  color: #f1f1f1;
}
#T_e567a_row1_col1 {
  background-color: #2484ba;
  color: #f1f1f1;
}
</style>
<table id="T_e567a">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_e567a_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_e567a_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_e567a_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_e567a_row0_col0" class="data row0 col0" >0.3695</td>
      <td id="T_e567a_row0_col1" class="data row0 col1" >0.3252</td>
    </tr>
    <tr>
      <th id="T_e567a_level0_row1" class="row_heading level0 row1" >spatial_density</th>
      <td id="T_e567a_row1_col0" class="data row1 col0" >0.3656</td>
      <td id="T_e567a_row1_col1" class="data row1 col1" >0.3671</td>
    </tr>
  </tbody>
</table>

And the results make sense: spatial density outperforms raw latitude and longitude for the regression model (the regression model really couldn't make sense of latitude and longitude), but it still underperforms raw latitude and longitude for XGBoost.

### 2.3. Geohash Target Encoding

It's a known fact -- some neighborhoods are more expensive than others. So, it's possible that giving information to the model about each Airbnb's neighborhood (and the price that can be expected in that neighborhood) can add predictive power.

But how to do this? Or more immediately, what's a neighborhood?

A neighborhood can be anything -- a zip-code, a street, or in our case, [Geohash](https://towardsdatascience.com/geospatial-indexing-explained-a-comparison-of-geohash-s2-and-h3-68d4ed7e366d). (I recently wrote an article about Geohash and other geospatial indexing tools — how they work, how to use them, and a comparison of Geohash to the two other most popular geospatial indexing tools. Feel free to check it out!)

-> ["Geospatial Indexing Explained: A Comparison of Geohash, S2, and H3"](https://benfeifke.com/posts/geospatial-indexing-explained/)

In short, Geohash allows us to convert a latitude-longitude point to a fixed neighborhood, thus giving us Airbnbs' neighborhoods as a categorical variable. And now, equipped with this categorical variable that represents the Airbnbs' neighborhoods, I use target encoding to communicate to the model about the expected price in each of those neighborhoods.

```python
def add_geohash_column_to_df(geo_df: pl.DataFrame) -> pl.DataFrame:
    result = (
        df
        .with_columns(
            df
            .select("latitude", "longitude")
            .map_rows(
                lambda x: geohash2.encode(x[0], x[1], precision=5),
                return_dtype=pl.Utf8
            )
            .rename({"map": "geohash"})
        )
    )
    return result


def add_target_encoding_to_df(
    dataframe: pl.DataFrame,
    categorical_column: str = "geohash"
) -> pl.DataFrame:
    category_target_means = (
        dataframe
        .filter(pl.col("is_train"))  # Only include train data to prevent test data leakage.
        .group_by(categorical_column)
        .agg(
            pl.col(MODEL_TARGET).mean().alias(f"{categorical_column}_{MODEL_TARGET}_mean")
        )
    )
    result = (
        dataframe
        .join(
            category_target_means,
            how="left",
            on=categorical_column
        )
    )
    return result


df_w_geohash = add_geohash_column_to_df(df)
df_w_geohash_target_encoded = add_target_encoding_to_df(df_w_geohash)
```

<figure class="image" align="center">
    <img src="/images/nyc-airbnb-geohash-price.png" alt="drawing"/>
    <figcaption style="font-style: italic">
        Airbnb geohash average prices; the outlines of the geohash squares can clearly be seen, and downtown Manhattan unsurprisingly has the highest prices. | Image by Author
    </figcaption>
</figure>

Let's see what the model thinks of my idea:

```python
MODEL_FEATURE_LIST_NAME = "geohash target encoding"
MODEL_FEATURE_LIST = [
    "geohash_price_log10_mean",
]

X_train = df_w_geohash_target_encoded.filter(pl.col("is_train")).select(MODEL_FEATURE_LIST)
y_train = df_w_geohash_target_encoded.filter(pl.col("is_train")).select(MODEL_TARGET)
X_test = df_w_geohash_target_encoded.filter(~pl.col("is_train")).select(MODEL_FEATURE_LIST)
y_test = df_w_geohash_target_encoded.filter(~pl.col("is_train")).select(MODEL_TARGET).to_numpy()

model_performance_list = []
for model_name, model_class in zip(
    ["xgboost", "ridge regression"],
    [XGBRegressor, Ridge]
):
    model = model_class().fit(X_train, y_train)
    y_predicted = model.predict(X_test)
    model_performance = root_mean_squared_error(y_test, y_predicted)
```

<style type="text/css">
#T_766d0_row0_col0 {
  background-color: #1b7eb7;
  color: #f1f1f1;
}
#T_766d0_row0_col1 {
  background-color: #d0d1e6;
  color: #000000;
}
#T_766d0_row1_col0 {
  background-color: #2a88bc;
  color: #f1f1f1;
}
#T_766d0_row1_col1 {
  background-color: #2484ba;
  color: #f1f1f1;
}
#T_766d0_row2_col0, #T_766d0_row2_col1 {
  background-color: #b7c5df;
  color: #000000;
}
</style>
<table id="T_766d0">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_766d0_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_766d0_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_766d0_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_766d0_row0_col0" class="data row0 col0" >0.3695</td>
      <td id="T_766d0_row0_col1" class="data row0 col1" >0.3252</td>
    </tr>
    <tr>
      <th id="T_766d0_level0_row1" class="row_heading level0 row1" >spatial_density</th>
      <td id="T_766d0_row1_col0" class="data row1 col0" >0.3656</td>
      <td id="T_766d0_row1_col1" class="data row1 col1" >0.3671</td>
    </tr>
    <tr>
      <th id="T_766d0_level0_row2" class="row_heading level0 row2" >geohash_target_encoding</th>
      <td id="T_766d0_row2_col0" class="data row2 col0" >0.3325</td>
      <td id="T_766d0_row2_col1" class="data row2 col1" >0.3325</td>
    </tr>
  </tbody>
</table>

As it turns out, geohash target encoding is more powerful than spatial density; though it still can't outperform raw latitude and longitude with XGBoost!

### 2.4: Putting It All Together

Of course, would I really be a data scientist if I didn't throw all the features together just to see what happens? Let's do it:

```python
df_w_all_features = add_density_feature_columns_to_dataframe(
    add_target_encoding_to_df(
        add_geohash_column_to_df(df)
    )
)

MODEL_FEATURE_LIST_NAME = "all_features"
MODEL_FEATURE_LIST = [
    "latitude",
    "longitude",
    "spatial_density",
    "geohash_price_log10_mean",
]

X_train = df_w_all_features.filter(pl.col("is_train")).select(MODEL_FEATURE_LIST)
y_train = df_w_all_features.filter(pl.col("is_train")).select(MODEL_TARGET)
X_test = df_w_all_features.filter(~pl.col("is_train")).select(MODEL_FEATURE_LIST)
y_test = df_w_all_features.filter(~pl.col("is_train")).select(MODEL_TARGET).to_numpy()

for model_name, model_class in zip(
    ["xgboost", "ridge regression"],
    [XGBRegressor, Ridge]
):
    model = model_class().fit(X_train, y_train)
    y_predicted = model.predict(X_test)
    model_performance = root_mean_squared_error(y_test, y_predicted)
```

And the winner is... XGBoost trained on the combination of all features! With XGBoost trained on raw latitude and longitude coming in close second:

<style type="text/css">
#T_8ee67_row0_col0 {
  background-color: #1b7eb7;
  color: #f1f1f1;
}
#T_8ee67_row0_col1 {
  background-color: #d0d1e6;
  color: #000000;
}
#T_8ee67_row1_col0 {
  background-color: #2a88bc;
  color: #f1f1f1;
}
#T_8ee67_row1_col1 {
  background-color: #2484ba;
  color: #f1f1f1;
}
#T_8ee67_row2_col0, #T_8ee67_row2_col1, #T_8ee67_row3_col0 {
  background-color: #b7c5df;
  color: #000000;
}
#T_8ee67_row3_col1 {
  background-color: #d2d2e7;
  color: #000000;
}
</style>
<table id="T_8ee67">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_8ee67_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_8ee67_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_8ee67_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_8ee67_row0_col0" class="data row0 col0" >0.3695</td>
      <td id="T_8ee67_row0_col1" class="data row0 col1" >0.3252</td>
    </tr>
    <tr>
      <th id="T_8ee67_level0_row1" class="row_heading level0 row1" >spatial_density</th>
      <td id="T_8ee67_row1_col0" class="data row1 col0" >0.3656</td>
      <td id="T_8ee67_row1_col1" class="data row1 col1" >0.3671</td>
    </tr>
    <tr>
      <th id="T_8ee67_level0_row2" class="row_heading level0 row2" >geohash_target_encoding</th>
      <td id="T_8ee67_row2_col0" class="data row2 col0" >0.3325</td>
      <td id="T_8ee67_row2_col1" class="data row2 col1" >0.3325</td>
    </tr>
    <tr>
      <th id="T_8ee67_level0_row3" class="row_heading level0 row3" >all_features</th>
      <td id="T_8ee67_row3_col0" class="data row3 col0" >0.3328</td>
      <td id="T_8ee67_row3_col1" class="data row3 col1" >0.3245</td>
    </tr>
  </tbody>
</table>


## 3. Discussion

Even after all the feature engineering, the most performant model was still hardly better than XGBoost with raw latitude and longitude! This tells us two things: (1) XGBoost is as awesome as ever, and (2) don't discredit raw features; they may be more powerful than you realize!

Of course, as with any data science problem, the fun is only just beginning; there are already many possibilities for improvement:
- **Hyperparameter tuning**: Is there a more performant choice for search-radius for the spatial density computation? Is there a more performant geohash precision for generating the geohash target-encoding?
- **Target encoding**: Herein presented was a very basic approach to target-encoding, but can a different approach be taken that improves model generalization, such as [Bayesian Target Encoding](https://towardsdatascience.com/target-encoding-and-bayesian-target-encoding-5c6a6c58ae8c)? Furthermore, could an aggregation function other than just `mean` be used in the target encoding, such as `median`, `max`, or `min`? Even `count` would be interesting as another proxy for spatial density.
- **Model ensembling**: Why just XGBoost and RidgeRegression, and why not a combination of the two? Or, is there even enough data to try training a different model for every distinct Geohash?

And of course, the whole ML approach could be improved to include k-fold cross-validation, or a model or cost function that more richly appreciates the fat-tailed nature of the target variable.

## 4. Conclusion
These are just a few ideas of what can be done when working on machine learning problems with data that contains latitude and longitude; hopefully it gives you some starting points. As always, thank you for reading 🙂 Until next time!

## References

\[1\]: https://www.kaggle.com/datasets/kritikseth/us-airbnb-open-data

---

{{< contact message="Liked what you read? Feel free to reach out on " >}}