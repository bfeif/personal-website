---
title: "The 3 Reasons Why I Have Permanently Switched From Pandas To Polars"
date: 2023-02-04
draft: false
tags:
    - language
    - language-learning
    - data-science
    - portuguese
    - statistics
keywords:
    - language
    - language-learning
    - data-science
    - portuguese
    - statistics
plotly: true
# comments: true
---

***I came for the speed, but I stayed for the syntax.***

At the time of writing this post, it's been six years since I graduated from my Data Science Masters Degree program. And, for those entire six years spent doing data-science, Pandas has been the foundation of all my work: impact-analyses, exploratory data analyses, data validations, model experimentation, you name it. My career was built on top of Pandas!

Needless to say, I had some serious Pandas [lock-in](https://en.wikipedia.org/wiki/Vendor_lock-in).

That is, until I discovered [Polars](https://github.com/pola-rs/polars), the new "blazingly fast DataFrame library" for Python.

In this article, I'll explain:
1. What Polars is, and what makes it so fast.
2. The 3 reasons why I have permanently switched from Pandas to Polars.
    1. The `.arr` namespace
    2. `.scan_parquet()` and `.sink_parquet()`
    3. Data-oriented programming

## Introducing Polars: The Fastest Python DataFrame Library That You've (Maybe) Never Heard Of.

Maybe you've heard of Polars, maybe you haven't! Either way it's slowly taking over Python's data-processing landscape, starting right here on Medium...
- [Leonie Monigatti](https://medium.com/@iamleonie) recently [wrote a comprehensive timing comparison of Pandas to Polars in Towards Data Science](https://towardsdatascience.com/pandas-vs-polars-a-syntax-and-speed-comparison-5aa54e27497e).
- [Wei-Meng Lee](https://weimenglee.medium.com/) already published a [Getting Started guide](https://towardsdatascience.com/getting-started-with-the-polars-dataframe-library-6f9e1c014c5c) last summer.
- [Carl M. Kadie](https://medium.com/@carlmkadie) wrote just last month on [one of the biggest surface-level differences between Pandas and Polars -- Polars's lack of an index](https://medium.com/towards-data-science/understand-polars-lack-of-indexes-526ea75e413).

So what makes it so fast? From the [Polars User Guide](https://pola-rs.github.io/polars-book/user-guide/#introduction):
> _`Polars` is completely written in [`Rust`](https://www.rust-lang.org/) (no runtime overhead!) and uses [`Arrow`](https://arrow.apache.org/) -- the [native arrow2 `Rust` implementation](https://github.com/jorgecarleitao/arrow2) -- as its foundation..._  
> _`Polars` is written in Rust which gives it C/C++ performance and allows it to fully control performance critical parts in a query engine..._  
> _...Unlike tools such as dask -- which tries to parallelize existing single-threaded libraries like NumPy and Pandas --Polars is written from the ground up, designed for parallelization of queries on DataFrames..._

And there you have it. `Polars` is not just a framework for dataframe parallelization like `dask` or `ray`; it is a full makeover of the Python DataFrame, including the highly optimal Apache Arrow columnar memory format as its foundation, and its own query optimization engine to boot.

This speed alone is enough to get anyone interested. But as you'll see in the rest of this article, the speed is what got me interested, but it's really the syntax that made me fall in love.

## The 3 Reasons Why I Have Permanently Switched from Pandas to Polars.

### 1. The `.arr` Namespace
Imagine the following scenario in `Pandas`: you have a dataset of families and some information about them, including a list of all the members of the family:

```python
import pandas as pd
df = pd.DataFrame({
    "last_name": ["Johnson", "Jackson", "Smithson"],
    "members": [["John", "Ron", "Con"], ["Jack", "Rack"], ["Smith", "Pith", "With", "Lith"]],
    "city_of_residence": ["Boston", "New York City", "Dallas"]
})
print(df)
>>>>   last_name                    members city_of_residence
     0   Johnson           [John, Ron, Con]            Boston
     1   Jackson               [Jack, Rack]     New York City
     2  Smithson  [Smith, Pith, With, Lith]            Dallas
```

For your analysis, you want to create a new column from the last element of the `members` list. How do you do this? A search of the `pandas` API will leave you lost, but a brief stackoverflow search will show you the answer! The prevailing method to extract an element of a list in a pandas column is to use the `.str` namespace ([stackoverflow ref1](https://stackoverflow.com/questions/69409763/pandas-index-the-first-element-of-each-list-in-a-dataframe-column-of-lists), [stackoverflow ref2](https://stackoverflow.com/questions/45983017/extracting-an-element-of-a-list-in-a-pandas-column)), like this:

```python
import pandas as pd
df = pd.DataFrame({
    "last_name": ["Johnson", "Jackson", "Smithson"],
    "members": [["John", "Ron", "Con"], ["Jack", "Rack"], ["Smith", "Pith", "With", "Lith"]],
    "city_of_residence": ["Boston", "New York City", "Dallas"]
})
df["family_leader"] = df["members"].str[0]
print(df)
>>>>   last_name                    members city_of_residence family_leader
     0   Johnson           [John, Ron, Con]            Boston          John
     1   Jackson               [Jack, Rack]     New York City          Jack
     2  Smithson  [Smith, Pith, With, Lith]            Dallas         Smith
```

If you're like me, you're probably wondering, "why do I have to use the `.str` namespace to handle a `list` data-type?".

In Polars, this is not a problem. By conforming to Apache Arrow's columnar data format, Polars has all standard data-types, and appropriate namespaces for handling all of them, including `list`s:

```python
import polars as pl
df = pl.DataFrame({
    "last_name": ["Johnson", "Jackson", "Smithson"],
    "members": [["John", "Ron", "Con"], ["Jack", "Rack"], ["Smith", "Pith", "With", "Lith"]],
    "city_of_residence": ["Boston", "New York City", "Dallas"]
})
df = df.with_columns([pl.col("members").arr.get(0).alias("family_leader")])
print(df)
>>>> ┌───────────┬─────────────────────────────┬───────────────────┬───────────────┐
     │ last_name ┆ members                     ┆ city_of_residence ┆ family_leader │
     │ ---       ┆ ---                         ┆ ---               ┆ ---           │
     │ str       ┆ list[str]                   ┆ str               ┆ str           │
     ╞═══════════╪═════════════════════════════╪═══════════════════╪═══════════════╡
     │ Johnson   ┆ ["John", "Ron", "Con"]      ┆ Boston            ┆ John          │
     │ Jackson   ┆ ["Jack", "Rack"]            ┆ New York City     ┆ Jack          │
     │ Smithson  ┆ ["Smith", "Pith", … "Lith"] ┆ Dallas            ┆ Smith         │
     └───────────┴─────────────────────────────┴───────────────────┴───────────────┘
```

That's right: Polars is so explicit about data-types, that it even tells you the type of each column in your dataframe every time you print!

It doesn't stop here though. Not only does the Pandas API require use of one data-type's namespace for handling of another data-type, but the API has become so bloated that there are often many ways to do the same thing. Consider the following code snippet:

```python
import pandas as pd

df = pd.DataFrame({
    "a": [1, 1, 1],
    "b": [4, 5, 6]
})

column_name_indexer = ["a"]
boolean_mask_indexer = df["b"]==5
slice_indexer = slice(1, 3)
for o in [column_name_indexer, boolean_mask_indexer, slice_indexer]:
    print(df[o])
```

In this code snippet, the same pandas syntax `df[o]` can do three distinct operations: retrieving a column of the dataframe, performing a row-based boolean mask on the dataframe, and retrieving a slice of the dataframe by index.

In Pandas, you can't do everything (for example, `Polars` has a `struct` data-type for handling `dict` columns, unlike `Pandas`), and for the things that you can do, there's sometimes multiple ways to do them! Compare this with `Polars`, where you can do everything, the data-types are clear, and there's usually only one way to do the same thing.

### 2. `.scan_parquet()` and `.sink_parquet()`

One of the best things about `Polars` is the fact that it offers two API's: an eager API and a lazy API.

The eager API does everything in-memory. Like `Pandas`

## So What's Wrong With Pandas?

Like I said, I had some serious Pandas lock-in, despite some glaring issues with Pandas that I've experienced over the years. Two such issues stand out among all others, though:
### Memory-Usage Runs Rampant in Pandas
Let's face it--pandas is slow! If you want to speed things up, you either have to sample down the data or do some chunking or filtering technique. A large part of this is because Pandas is single-threaded. Of course, to alleviate this, you can crawl through a web of parallelization tools like Dask, Ray, and Modin, but they all have less trivial configuration and set-up than they tend to advertise.
### Pandas Syntax is Bloated and Complicated
It's no secret that Pandas has a steep learning curve, and its bloated API is largely the reason. To this end, there's many ways to do the same thing in Pandas, and it makes things a little messy. Consider the following example:
```python
import pandas as pd

df = pd.DataFrame({
    "a": [1, 1, 1],
    "b": [4, 5, 6]
})

for index in [["a"], df["b"]==5, slice(1, 3)]:
    print(df[index])
```
Each of these instances of `df[...]` does a different operation, rendering a completely different result: a column selection, a boolean masking on rows, and then a slicing over rows.

Added to this, Pandas treats data as an Object, (as in "Object" from an Object-Oriented Programming perspective). This often means that, while doing EDA, getting to a particular result can require multiple assignment operations, belying the true nature of what we most often want to do with Pandas--query! Added to this, 

Are these problems familiar to you? Polars fixes all of this, and more...