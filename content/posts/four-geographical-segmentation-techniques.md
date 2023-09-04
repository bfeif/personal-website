---
title: "Geographical Indexing Techniques Explained, With Python Code"
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
***"How long is the coastline of Britain? It turns out, it depends how long your ruler is."***
https://en.wikipedia.org/wiki/Coastline_paradox

What's a zip-code?

A zip-code is the result of a government's attempt to break up their land into smaller, more easily governable subdivisions.

We data scientists also want to break up land into smaller subdivisions, for all sorts of purposes other than governing -- analytics, feature-engineering, AB testing, and more.

Dividing one geographical space into smaller geographical spaces is a richly studied topic, and the tools that do this can bring a lot of power and richness to our models and analyses. What makes geographical segmentation techniques further exciting, is that a look under their proverbial hoods reveals eclectic amalgams of other mathematical and programming tools, such as fractals, space-filling curves, hashing functions, and more!

This post will explore three of today's most popular geographical segmentation techniques -- where they come from, how they work, and the Python code to use them:
- Geohashing
- S2
- H3

## Geohashing
- public geographical indexing technique which maps latitude longitude pairs to squares with arbitrarily user-defined resolution.
- https://geospatialmedia.s3.amazonaws.com/wp-content/uploads/2020/09/2.png
- The most important property of Geohash for humans is that spatial hierarchy is mapped to Geohash string hierarchy; for example, the level 3 geohash `t1a` is strictly contained inside the level 1 geohash `t1`, etc (see image). So, if your house is inside `t1a`, it is also inside `t1`, and also inside `t`, etc.
- It is projected onto a square version of the map, can lead to variable sized geohash squares
- Geohash uses a Z-order curve, which leads to a one negative point: points that are close in space are not necessarily close in their geohash string, and points that are close in their geohash string are not necessarily close in space

## S2
- public geographical indexing technique which maps latitude longitude pairs to squares with arbitrarily user-defined resolution.
- two critical updates on the Geohash algorithm: uses Hilbert curve instead of Z-order curve, and projects a cube onto the earth rather than a rectangle onto the earth.
