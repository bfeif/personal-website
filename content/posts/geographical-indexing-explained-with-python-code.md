---
title: "Geographical Indexing Explained, With Python Code"
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

What's a zip-code?

A zip-code is the result of a government's attempt to index latitude-longitude pairs to smaller, more easily governable subdivisions.

We data scientists also want to index latitude-longitude pairs to smaller subdivisions, but for all sorts of purposes other than governing -- analytics, feature-engineering, granular AB testing by geographic subdivision, and more.

Geographical indexing is a richly studied topic, and the tools that do it can bring a lot of power and richness to our models and analyses. What makes geographical indexing techniques further exciting, is that a look under their proverbial hoods reveals eclectic amalgams of other mathematical tools, such as space-filling curves, map projections, tesselations, and more!

This post will explore three of today's most popular geographical indexing techniques -- where they come from, how they work, what makes them different from one another, and some basic Python code to use them:
1. Geohash
2. S2
3. H3

## Three Geographical Indexing Techniques
### 1. Geohash
Geohash, invented in 2008 by Gustavo Niemeyer, is the earliest created geographical indexing technique (apparently a similar technique to Niemeyer's was created in 1966 by Guy Macdonald Morton, but Niemeyer claims to have not known about it until after developing Geohash). It enables its users to map latitude longitude pairs to squares of arbitrarily user-defined resolution. In Geohash, these squares are uniquely identified by a signature string, such as `"___"` (this is the level-6 geohash in which I currently live!).

But how are these strings generated?

To map a latitude-longitude pair to a geohash is an elegantly simple algorithm:

1. Choose a `geohash-level`, or resolution. For our example, we'll choose `1`.
2. Create an empty binary array `S` of length `geohash-level * 5` (here, length `5`).
3. For each geohash level, ask the question `5` times...
    1. Is our point in the left half of the map? If so, append `0` to `S` and reset the map to be just the left half of the map; if our point is in the right half of the map, append `1` to `S` and reset the map to be just the right-half of the map.
    2. Is our point in the bottom half of the map? If so, append `0` to `S` and reset the map to be just the bottom half of the map; if it's in the top half of the map, append `1` to `S` and reset the map to be just the top half of the map.
4. Convert every 5 bits from `S` into a Geohash 32-bit alphanumeric character, and return.

<img src="/images/geohash-algorithm-explained.png" alt="drawing"/>
<!-- source: https://map-projections.net/img/flat-ocean/mercator-84.jpg?ft=59de1425 -->

This algorithm can be repeated iteratively arbitrarily many times, all the way down to geohashes that are less than a meter on each side!

<img src="/images/geohash-level-1-level-2.png" alt="drawing"/>
<!-- source: https://www.geospatialworld.net/blogs/polygeohasher-an-optimized-way-to-create-geohashes/ -->

What's particularly elegant about this algorithm is that, by following this pattern of "left is `0`, right is `1`; bottom is `0`, top is `1`", the alphabetically ordered geohashes trace out a Z-order curve:

<img src="/images/geohash-z-order-curve.jpeg" alt="drawing"/>
<!-- source: https://ceur-ws.org/Vol-1671/paper4.pdf -->

[Z-order curves](https://en.wikipedia.org/wiki/Z-order_curve) are a type of space-filling curves, which are designed just for this purpose of mapping multidimensional values (such as latitude-longitude pairs) to one dimensional representations (such as a string).

Geohash is quite powerful: it's simple, fast, and importantly, the geohash strings preserve spatial hierarchy (i.e. if your house is in the level 3 geohash `"t1a"`, then it is also in the level 2 geohash `"t1"`, and in the level 1 geohash `"t"`). However, you might have noticed a few issues with it by now...

First, while the Z-order curve is convenient, it does not preserve guaranteed proximity between latitude-longitude pairs. Due to edge effects, two locations that are close in physical distance are not guaranteed to be close in their computed geohash strings; furthermore, due to the nature of the Z-order curve, two locations that are close in their geohash string might not be close in physical distance.

Second, while the [Mercator projection](https://en.wikipedia.org/wiki/Mercator_projection) of the map that is used by Geohash is convenient in its simplicity, it leads to high variability in the size of the geohash squares; furthermore, the Mercator projection has a discontinuity at both the North and South Poles (i.e. if you have a house in Antarctica at (-90°, 0°), it will not have a geohash -- sorry to disappoint!).

The geographical indexing techniques that follow came after Geohash, and seek to rectify these two issues.

### 2. S2


- public geographical indexing technique which maps latitude longitude pairs to squares with arbitrarily user-defined resolution.
- two critical updates on the Geohash algorithm: uses Hilbert curve instead of Z-order curve, and projects a cube onto the earth rather than a rectangle onto the earth.
- by projecting a cube onto the earth, some of the pain of variable sized squares is relieved.
- with the Hilbert curve, we have that points that are close in space aren't necessarily close in their string, but points that are close in their string are necessarily close in space.
- https://s2geometry.io/about/overview
- https://s2geometry.io/devguide/s2cell_hierarchy

### H3
- public geographical indexing technique which maps latitude longitude pairs to squares with arbitrarily user-defined resolution.
- Created by Uber
- Builds hexagonal grids on the icosahedron (with touches of pentagons, in the water)
- One of main benefits is that every cell is equidistant from its neighbors.
- Compared to the square techniques, it loses the strict spatial hierarchy, due to edge-effects when subdividing the hexagon into 7 smaller hexagons
- supports 16 levels of resolution.
- no space filling curve; rather, hierarchical subdivision: https://github.com/uber/h3/discussions/416#discussioncomment-1509642
- hexagons are equidistant, making it easy to compute gradients

Refs:
https://www.uber.com/en-DE/blog/h3/
https://docs.google.com/spreadsheets/d/1YQGOqNeI0zItS4MZYY_OASLfFMIJUOFer2OwQhVapX8/edit#gid=0
https://www.youtube.com/watch?v=vGKs-c1nQYU