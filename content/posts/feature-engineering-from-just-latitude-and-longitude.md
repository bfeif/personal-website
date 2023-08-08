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

Without further ado... this article gives a comparison of four feature-engineering techniques:
- raw latitude longitude (i.e. no feature engineering)
- spatial density
    - could use many things: nearest neighbors, KDE, etc.
- geohash category
- geohash target encoding
and compares against a few models

---

{{< contact message="Liked what you read? Feel free to reach out on " >}}