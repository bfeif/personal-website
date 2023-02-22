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

***And the 1 thing I still use Pandas for.***

At the time of writing this post, it's been six years since I graduated from my Data Science Masters Degree program. And, for those entire six years spent doing data-science, Pandas has been the foundation of all my work: impact-analyses, exploratory data analyses, data validations, model experimentation, you name it. My career was built on top of Pandas!

Needless to say, I had some serious Pandas [lock-in](https://en.wikipedia.org/wiki/Vendor_lock-in).

That is, until I discovered [Polars](https://github.com/pola-rs/polars), the new "blazingly fast DataFrame library" for Python.

In this article, I'll explain:
1. My biggest frustrations with Pandas.
2. What Polars is, and what makes it so fast.
3. The 3 reasons why I have permanently switched from Pandas to Polars.
4. As promised, the 1 thing that I still use Pandas for.

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

## Introducing Polars: The Fastest Python DataFrame Library That You've (Maybe) Never Heard Of.

Maybe you've heard of Polars, maybe you haven't! Either way it's starting to get some serious traction...
- [Leonie Monigatti](https://medium.com/@iamleonie) recently [wrote a comprehensive timing comparison of Pandas to Polars in Towards Data Science](https://towardsdatascience.com/pandas-vs-polars-a-syntax-and-speed-comparison-5aa54e27497e).
- [Wei-Meng Lee](https://weimenglee.medium.com/) already published a [Getting Started guide](https://towardsdatascience.com/getting-started-with-the-polars-dataframe-library-6f9e1c014c5c) last summer.
- [Carl M. Kadie](https://medium.com/@carlmkadie) wrote just last month on [one of the biggest surface-level differences between Pandas and Polars -- Polars's lack of an index](https://medium.com/towards-data-science/understand-polars-lack-of-indexes-526ea75e413).