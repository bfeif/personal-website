---
title: "Feature Engineering With Latitude and Longitude"
date: 2024-03-16T09:47:45+02:00
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

***Leveraging the power in your geospatial data -- with code!***

Many of today's most competitive tech markets involve points moving around on a map: ride-hailing services (Uber, Lyft, Grab), micromobility services (Lime, Bird), food delivery services (Delivery Hero, Postsmates, Doordash), and more. Moreover, many services that don't place customers' locations at the center of their product use-cases still want to know their customers' locations so that they can better personalize their experiences based on where they are and what's going on around them.

What this all means for data scientists is that there's a lot of latitudes and longitudes floating around our data lakes (pun intended); and buried deep inside just these two variables is a wealth of information!

Creatively and effectively utilizing latitude and longitude can bring immense predictive power to our machine learning applications and added dimensionality to our analytics efforts, helping us data scientists to bring more value to our companies and our customers.

The goal of this article is to give a demontration of a few feature engineering techniques that use just latitude and longitude, comparing their predictive power on a Miami Home Sale Price Prediction problem. The structure is as follows:

1. Miami home sale price prediction problem setup
2. Discussion of feature-engineering techniques
    1. Raw latitude and longitude
    2. Spatial density
    4. Geohash target encoding
    5. Combination of all features
3. Extensions, Next Steps

Since the focus of this post is on feature-engineering, the model evaluation will be quite straightforward for the sake of brevity and clarity (i.e. no cross validation and no hyperparameter optimization).

Furthermore, this post will use Polars as a data manipulation library, as opposed to Pandas; if you, dear reader, are unfamiliar with Polars or otherwise find yourself still stuck in Panda-land, feel free to first check out my earlier post, ["The 3 Reasons Why I Have Permanently Switched From Pandas To Polars"](https://benfeifke.com/posts/the-3-reasons-why-i-switched-from-pandas-to-polars-20230328/).

And now, let's go 🚀

## 1. Miami Home Sale Price Prediction: Problem Setup

The problem that these different feature engineering techniques will be tested on throughout this post is Home Sale Price Prediction in Miami, coming from a publicly available dataset "Miami Housing 2016" \[1\]. The only raw features used are `"latitude"` and `"longitude"`, and they are used to predict the target `"price"`. The feature engineering techniques are compared to one another by way of two different machine learning models: Ridge Regression and XGBoost; here, I use one regression model and one tree-based model to demonstrate the way that these two models respond to the different tecniques considered.

```python
import polars as pl
from xgboost import XGBRegressor
from sklearn.linear_model import Ridge


# Data taken from https://www.openml.org/search?type=data&id=43093
df = (
    pl.scan_csv("../data/miami-housing.csv")
    .with_columns([
        pl.col("SALE_PRC").alias("price"),
        pl.col(["LATITUDE", "LONGITUDE"]).name.to_lowercase()
    ])
    .select(pl.col(["latitude", "longitude", "price"]))
)
```

Before getting started with any ML prediction, though, it's necessary to inspect the target variable; after all, monetary variables like "price" and "income" are often log-normally distributed or at least heavily right-skewed, and so it might behoove us to first transform the target variable to log-space:

```python
# Normally, it'd be necessary to take the log of price plus one to account for a log(0)
# error, but there's no way that a house sold for $0 :P
df = df.with_columns(pl.col("price").log10().suffix("_log10"))
```

<figure class="image" align="center">
    <img src="/images/distribution-of-home-sale-price.png" alt="drawing"/>
    <figcaption style="font-style: italic">
        <a href="https://benfeifke.com/posts/ecdf-the-only-plotting-tool-a-data-scientist-needs/">ECDF</a> and histogram of home sale prices. The most expensive sold for over $2,000,000! | Image by Author
    </figcaption>
</figure>

Even after a `log` transformation, the target is still slightly right-skewed. Nonetheless, it looks sufficient for our use-case.

<figure class="image" align="center">
    <img src="/images/miami-home-sale-price.png" alt="drawing"/>
    <figcaption style="font-style: italic">
        Home sale prices in Miami; houses close to the beach generally sold for more money, with the wealthy neighborhood of Pinecrest also being a hot spot. | Image by Author
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

And with that, everything is prepared! So without further ado, it's time to engineer some features 🧑‍💻

## 2. Discussion of Feature Engineering Techniques

### 2.1. Raw Latitude and Longitude
The first feature engineering technique is... you guessed it -- no feature engineering! Latitude and longitude can be quite powerful features on their own, though their behavior as such depends highly on the model being used. In particular, you wouldn't usually expect latitude or longitude to have a linear relationship with your target variable, unless your target is something earthly in nature, like "temperature" or "humidity"; with this, raw latitude and longitude won't play so well with linear models like `RidgeRegression`; they can however already be quite powerful with models based on decision trees like `XGBoost`:

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
#T_a1b4a_row0_col0 {
  background-color: #056aa6;
  color: #f1f1f1;
}
#T_a1b4a_row0_col1 {
  background-color: #b1c2de;
  color: #000000;
}
</style>
<table id="T_a1b4a">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_a1b4a_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_a1b4a_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_a1b4a_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_a1b4a_row0_col0" class="data row0 col0" >0.17848</td>
      <td id="T_a1b4a_row0_col1" class="data row0 col1" >0.13420</td>
    </tr>
  </tbody>
</table>

The intuition was correct: `XGBoost` has a lower `root_mean_squared_error` than `RidgeRegression`.


### 2.2. Spatial Density
Do homes in urban areas sell for higher prices than those in rural areas?

Population density is correlated with many demographic processes, and this is certainly true for rental prices and incomes (e.g. people earn higher salaries in cities than in the countryside). And even though this problem focuses on Miami, where there's only urban areas and slightly less urban areas, a spatial density feature might still be useful.

One could use many methods for measuring the spatial density around a home: counting the number of other home sales within some radius of each home sale; or computing and sampling from a Kernel Density Estimate over home sale locations; or even pulling third party census data about population density. For this case, I measure spatial density as the number of home sales within some radius of each home, using `scipy`'s [cKDtree](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.cKDTree.html):

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
    <img src="/images/miami-home-density.png" alt="drawing"/>
    <figcaption style="font-style: italic">
        Spatial density of home sales in Miami reflects the urbanness of the larger Miami metropolitan area; the highest density of home sales is surprisingly south of the city, around Homestead. | Image by Author
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
#T_01114_row0_col0 {
  background-color: #056aa6;
  color: #f1f1f1;
}
#T_01114_row0_col1 {
  background-color: #b1c2de;
  color: #000000;
}
#T_01114_row1_col0 {
  background-color: #d7d6e9;
  color: #000000;
}
#T_01114_row1_col1 {
  background-color: #bfc9e1;
  color: #000000;
}
</style>
<table id="T_01114">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_01114_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_01114_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_01114_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_01114_row0_col0" class="data row0 col0" >0.17848</td>
      <td id="T_01114_row0_col1" class="data row0 col1" >0.13420</td>
    </tr>
    <tr>
      <th id="T_01114_level0_row1" class="row_heading level0 row1" >spatial_density</th>
      <td id="T_01114_row1_col0" class="data row1 col0" >0.12211</td>
      <td id="T_01114_row1_col1" class="data row1 col1" >0.13018</td>
    </tr>
  </tbody>
</table>

Spatial density outperforms raw latitude and longitude for both the regression model and XGBoost; interestingly, the regression model slightly outperforms XGBoost for this feature.

### 2.3. Geohash Target Encoding

It's a known fact -- some neighborhoods are more expensive than others. So, it's possible that giving information to the model about each home's neighborhood (and the sale price that can be expected in that neighborhood) can add predictive power.

But how to do this? Or more immediately, what's a neighborhood?

A neighborhood can be anything -- a zip-code, a street, or in our case, a [Geohash](https://towardsdatascience.com/geospatial-indexing-explained-a-comparison-of-geohash-s2-and-h3-68d4ed7e366d). (I recently wrote an article about Geohash and other geospatial indexing tools — how they work, how to use them, and a comparison of Geohash to the two other most popular geospatial indexing tools. Feel free to check it out!)

-> ["Geospatial Indexing Explained: A Comparison of Geohash, S2, and H3"](https://benfeifke.com/posts/geospatial-indexing-explained/)

In short, Geohash allows us to convert a latitude-longitude point to a fixed neighborhood, thus giving us homes' neighborhoods as a categorical variable. And now, equipped with this categorical variable that represents the homes' neighborhoods, I use target encoding to communicate to the model about the expected price in each of those neighborhoods.

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
    <img src="/images/miami-geohash-median-home-sale-price.png" alt="drawing"/>
    <figcaption style="font-style: italic">
        Residing-geohash median prices; the outlines of the geohash squares can clearly be seen, and Miami beach unsurprisingly has some of the highest prices. | Image by Author
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
#T_1c975_row0_col0 {
  background-color: #056aa6;
  color: #f1f1f1;
}
#T_1c975_row0_col1 {
  background-color: #b1c2de;
  color: #000000;
}
#T_1c975_row1_col0 {
  background-color: #d7d6e9;
  color: #000000;
}
#T_1c975_row1_col1 {
  background-color: #bfc9e1;
  color: #000000;
}
#T_1c975_row2_col0 {
  background-color: #d4d4e8;
  color: #000000;
}
#T_1c975_row2_col1 {
  background-color: #d6d6e9;
  color: #000000;
}
</style>
<table id="T_1c975">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_1c975_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_1c975_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_1c975_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_1c975_row0_col0" class="data row0 col0" >0.17848</td>
      <td id="T_1c975_row0_col1" class="data row0 col1" >0.13420</td>
    </tr>
    <tr>
      <th id="T_1c975_level0_row1" class="row_heading level0 row1" >spatial_density</th>
      <td id="T_1c975_row1_col0" class="data row1 col0" >0.12211</td>
      <td id="T_1c975_row1_col1" class="data row1 col1" >0.13018</td>
    </tr>
    <tr>
      <th id="T_1c975_level0_row2" class="row_heading level0 row2" >geohash_target_encoding</th>
      <td id="T_1c975_row2_col0" class="data row2 col0" >0.12307</td>
      <td id="T_1c975_row2_col1" class="data row2 col1" >0.12256</td>
    </tr>
  </tbody>
</table>

Geohash target encoding performs slightly better than spatial density!

### 2.4: Putting It All Together

Finally, would I really be a data scientist if I didn't throw all the features together just to see what happens? Let's do it:

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

And the winner is... XGBoost trained on the combination of all features!

<style type="text/css">
#T_d69cb_row0_col0 {
  background-color: #056aa6;
  color: #f1f1f1;
}
#T_d69cb_row0_col1 {
  background-color: #b1c2de;
  color: #000000;
}
#T_d69cb_row1_col0 {
  background-color: #d7d6e9;
  color: #000000;
}
#T_d69cb_row1_col1 {
  background-color: #bfc9e1;
  color: #000000;
}
#T_d69cb_row2_col0, #T_d69cb_row3_col0 {
  background-color: #d4d4e8;
  color: #000000;
}
#T_d69cb_row2_col1 {
  background-color: #d6d6e9;
  color: #000000;
}
#T_d69cb_row3_col1 {
  background-color: #dad9ea;
  color: #000000;
}
</style>
<table id="T_d69cb">
  <thead>
    <tr>
      <th class="blank level0" >&nbsp;</th>
      <th id="T_d69cb_level0_col0" class="col_heading level0 col0" >ridge regression</th>
      <th id="T_d69cb_level0_col1" class="col_heading level0 col1" >xgboost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th id="T_d69cb_level0_row0" class="row_heading level0 row0" >raw_lat_lon</th>
      <td id="T_d69cb_row0_col0" class="data row0 col0" >0.17848</td>
      <td id="T_d69cb_row0_col1" class="data row0 col1" >0.13420</td>
    </tr>
    <tr>
      <th id="T_d69cb_level0_row1" class="row_heading level0 row1" >spatial_density</th>
      <td id="T_d69cb_row1_col0" class="data row1 col0" >0.12211</td>
      <td id="T_d69cb_row1_col1" class="data row1 col1" >0.13018</td>
    </tr>
    <tr>
      <th id="T_d69cb_level0_row2" class="row_heading level0 row2" >geohash_target_encoding</th>
      <td id="T_d69cb_row2_col0" class="data row2 col0" >0.12307</td>
      <td id="T_d69cb_row2_col1" class="data row2 col1" >0.12256</td>
    </tr>
    <tr>
      <th id="T_d69cb_level0_row3" class="row_heading level0 row3" >all_features</th>
      <td id="T_d69cb_row3_col0" class="data row3 col0" >0.12343</td>
      <td id="T_d69cb_row3_col1" class="data row3 col1" >0.12037</td>
    </tr>
  </tbody>
</table>

## 3. Discussion

Of course, as with any data science problem, the fun is only just beginning; there are already many possibilities for improvement:
- **Hyperparameter tuning**: Is there a more performant choice for search-radius for the spatial density computation? Is there a more performant geohash precision for generating the geohash target-encoding?
- **Target encoding**: Herein presented was a very basic approach to target-encoding, but can a different approach be taken that improves model generalization, such as [Bayesian Target Encoding](https://towardsdatascience.com/target-encoding-and-bayesian-target-encoding-5c6a6c58ae8c)? Furthermore, could an aggregation function other than just `mean` be used in the target encoding, such as `median`, `max`, or `min`? Even `count` would be interesting as another proxy for spatial density.
- **Model ensembling**: Why just XGBoost and RidgeRegression, and why not a combination of the two? Or, is there even enough data to try training a different model for every distinct Geohash?

And of course, the whole ML approach could be improved to include k-fold cross-validation, or a model or cost function that more richly appreciates the fat-tailed nature of the target variable.

Furthermore, the dataset herein studied was a record of home sales, which is notably distinct from home prices: in the former, one record represents a transaction, whereas in the latter, one record represents an actual home. As such, the spatial density feature herein computed may represent more of a "neighborhood purchasing popularity" than it does urbanness. As such, as with any data science problem, your mileage with these techniques may vary depending on the nature of your data.

## 4. Conclusion
These are just a few ideas of what can be done when working on machine learning problems with data that contains latitude and longitude; hopefully it gives you some starting points. You can check out the code on my github ([link](https://github.com/bfeif/personal-website/blob/geospatial-feature-engineering-post/code/notebooks/feature-engineering-with-just-latitude-and-longitude.ipynb)). As always, thank you for reading 🙂 Until next time!

## References

\[1\]: https://www.openml.org/search?type=data&id=43093

---

{{< contact message="Liked what you read? Feel free to reach out on " >}}