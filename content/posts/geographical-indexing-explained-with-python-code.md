---
title: "Geographical Indexing Explained"
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

What's a postal-code? A postal-code is nothing but a government's attempt to index latitude-longitude pairs to smaller, more easily governable subdivisions.

We data scientists also want to index latitude-longitude pairs to smaller subdivisions, but for all sorts of purposes other than governing -- analytics, feature-engineering, granular AB testing by geographic subdivision, and more.

Geographical indexing is a richly studied topic, and the tools that do it can bring a lot of power and richness to our models and analyses. What makes geographical indexing techniques further exciting, is that a look under their proverbial hoods reveals eclectic amalgams of other mathematical tools, such as space-filling curves, map projections, tesselations, and more!

This post will explore three of today's most popular geographical indexing techniques -- where they come from, how they work, and what makes them different from one another:
1. Geohash
2. S2
3. H3

This post will conclude by comparing these techniques, and recommending when you might want to use one over another.

Please note that these tools include much functionality beyond basic geographical indexing: polygon intersection, polygon containment checks, line containment checks, generating cell-coverings of geographical spaces, and more. This post, however, focuses strictly on geographical indexing.

## Three Geographical Indexing Techniques
### 1. Geohash
Geohash, invented in 2008 by Gustavo Niemeyer, is the earliest created geographical indexing technique. It enables its users to map latitude-longitude pairs to Geohash squares of arbitrarily user-defined resolution. In Geohash, these squares are uniquely identified by a signature string, such as `"drt3"` (the level-4 geohash in which I grew up!).

<img src="/images/my-home-geohash-drt3.png" alt="drawing" width=400/>
<!-- source: https://www.movable-type.co.uk/scripts/geohash.html -->

But how are these strings generated?

#### The Geohash Algorithm
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

What's particularly elegant about this algorithm is that, by following this pattern of "left is `0`, right is `1`; bottom is `0`, top is `1`", the alphabetically ordered geohashes trace out a Z-order curve.

#### What's a Z-order curve?

<img src="/images/geohash-z-order-curve.jpeg" alt="drawing"/>
<!-- source: https://ceur-ws.org/Vol-1671/paper4.pdf -->

[Z-order curves](https://en.wikipedia.org/wiki/Z-order_curve) are a type of space-filling curves, which are designed just for this purpose of mapping multidimensional values (such as latitude-longitude pairs) to one dimensional representations (such as a string).

Geohash is quite powerful: it's simple, fast, and importantly, the geohash strings preserve spatial hierarchy (i.e. if your house is in the level 3 geohash `"t1a"`, then it is also in the level 2 geohash `"t1"`, and in the level 1 geohash `"t"`). However, you might have noticed a few issues with it by now...

First, while the Z-order curve is convenient, it only weakly preserves latitude-longitude proximity in computed strings; particularly, due to edge effects, two locations that are close in physical distance are not guaranteed to be close in their computed geohash strings. Furthermore, due to the "zig-zag" nature of the Z-order curve, the opposite is also true -- two locations that are close in their geohash string might not be close in physical distance.

Second, while the flat projection of the map that is used by Geohash is convenient in its simplicity, it leads to high variability in the size of the geohash squares; furthermore, this projection has a discontinuity at both the North and South Poles (i.e. if you have a vacation house in Antarctica at (-90°, 0°), it will not have a geohash -- sorry to disappoint!).

The geographical indexing techniques that follow came after Geohash, and seek to rectify these two issues.

### 2. S2

First announced on [December 5, 2017](https://opensource.googleblog.com/2017/12/announcing-s2-library-geometry-on-sphere.html), S2 was created at Google primarily by [Eric Veach](https://en.wikipedia.org/wiki/Eric_Veach).

S2, among many other things, alleviates the two aforementioned issues with Geohash, and it does so by way of two innovations: (1) it uses a [Hilbert curve](https://en.wikipedia.org/wiki/Hilbert_curve) instead of a Z-order curve to alleviate the problem that string-distance is not representative of physical distance, and (2) it uses a cube projection instead of a flat projection, reducing size differences between squares.

<img src="/images/s2curve-globe.gif" alt="drawing" width=300/>

#### The Hilbert Curve

The [Hilbert curve](https://en.wikipedia.org/wiki/Hilbert_curve) is another type of space-filling curve that, rather than using a "zig-zag pattern" like the Z-order curve, uses a gentler "u-shaped pattern".

<img src="https://media.springernature.com/lw685/springer-static/image/art%3A10.1007%2Fs11042-017-4744-4/MediaObjects/11042_2017_4744_Fig4_HTML.gif" alt="drawing"/>

By using the Hilbert curve, S2 facilitates that latitude-longitude pairs that are close in their S2 Cell ID string distance are much more likely to be close in physical distance. That said, this is only an alleviation -- the Hilbert curve still possesses the same unfortunate edge effects as the Z-order curve, resulting in the fact that latitude-longitude pairs close in physical distance are not guaranteed to be close in their S2 Cell ID string distance.

#### The S2 Map Projection
The second key innovation from S2 is the use of an unfolded-cube projection of the earth rather than a flat projection.

<img src="https://s2geometry.io/devguide/img/s2cell_global.jpg" alt="drawing"/>

Using such a projection significantly reduces variation between cell sizes because, as you move away from the equator, the distance between two longitude lines increases sinusoidally as a function of latitude.

<img src="/images/longitude-distortion-of-geohash-s2.png" alt="drawing">

- https://s2geometry.io/about/overview
- https://s2geometry.io/devguide/s2cell_hierarchy
- eric@rainforesttrust.org

### H3

Last, and certainly not least, [Uber's H3](https://www.uber.com/en-DE/blog/h3/). The most recently published geographical indexing technique of these three, H3 has two further key innovations that have made it a very popular tool in data science: (1) the use of hexagons in place of squares, and (2) the use of an icosahedron projection onto Earth.

<img src="https://blog.uber-cdn.com/cdn-cgi/image/width=2160,quality=80,onerror=redirect,format=auto/wp-content/uploads/2018/06/Twitter-H3.png" alt="drawing">

#### Why Hexagons?

The hexagon is the [regular polygon](https://en.wikipedia.org/wiki/Regular_polygon) with the most sides that still tessalates with itself. And, if you take such a tessalation of only hexagons, the unique property arises that, for any given hexagon in the tessalation, all of its neighbors are equidistant from the element; this is critically not the same for triangles or squares (the only other two regular polygons that tessalate with themselves), for whom any element of the tesslation has three and two distinct possible distances from its neighbors, respectively.

<img src="/images/triangles-vs-squares-vs-hexagons.png" alt="drawing">
<!-- source: https://www.uber.com/en-DE/blog/h3/ -->

Having this property that all neighbors are equidistant greatly simplifies any calculus or gradient related operations that Uber or H3's other users might want to perform.

As a brief aside, hexagons are also mother nature's choice of shape--bees build their hives in hexagons, water crystallizes in hexagons that scale fractally up to beautiful snowflakes, and Saturn has a giant hexagon-shaped storm at its North pole. Put simply, [hexagons are the bestagons](https://www.youtube.com/watch?v=thOifuHs6eY)!

#### The Icosahedron Projection
H3's second innovation is the use of an icosahedron projection (as opposed to Geohash's Mercator-like projection and S2's unfolded cube).

<img src="/images/h3-icosahedron.png" alt="drawing">
<!-- source: https://www.uber.com/en-DE/blog/h3/ -->

H3 then covers each triangle face of the icosahedron with hexagons, and subdivides hexagons into smaller hexagons from there.

<img src="/images/h3-icosahedron-face.png" alt="drawing">

#### H3's Sacrifices
At this point, you might be wondering -- what about a space-filling curve? What about subdividing hexagons into smaller hexagons? Well, there is no such thing as the perfect architecture; only the right one. And in order to build this hexagonal elegance, Uber had to make a few sacrifices:
- h3 uses hierarchical subdivisions rather than a space-filling curve;
- h3's method of subdividing hexagons into smaller hexagons does not retain strict spatial hierarchy;
- h3's icosahedron mapping necessitates a few pentagons to be used in place of only hexagons.

- supports 16 levels of resolution.
- no space filling curve; rather, hierarchical subdivision: https://github.com/uber/h3/discussions/416#discussioncomment-1509642
- h3 makes some sacrifices to achieve their complexity: pentagons and no space-filling curve.
- Compared to the square techniques, it loses the strict spatial hierarchy, due to edge-effects when subdividing the hexagon into 7 smaller hexagons
- Builds hexagonal grids on the icosahedron (with touches of pentagons, in the water)

Refs:
https://www.uber.com/en-DE/blog/h3/
https://docs.google.com/spreadsheets/d/1YQGOqNeI0zItS4MZYY_OASLfFMIJUOFer2OwQhVapX8/edit#gid=0
https://www.youtube.com/watch?v=vGKs-c1nQYU