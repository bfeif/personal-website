---
title: "Why the Cumulative Distribution Function Is the Only Plotting Tool a Data Scientist Needs."
date: 2022-07-31T09:47:45+02:00
draft: true
tags:
    - math
    - language
    - chaos-theory
    - data-science
keywords:
    - math
    - language
    - chaos-theory
    - data-science
# comments: true
---

https://www.andata.at/en/software-blog-reader/why-we-love-the-cdf-and-do-not-like-histograms-that-much.html

The modern data scientist lives in an ocean of plotting techniques. From matplotlib to ggplot2; from plotly to d3.js; from histograms, to box and whisker plots, to scatter plots, to whatever crazy chart seaborn comes up with next; it can be hard for novice data scientists to make sense of it all, drowning under all these different plotting tools! 

--> insert image of raining plot types.

But not to worry--let Cumulative Distribution Functions (CDFs) be your life-jacket; they are the one EDA companion you truly need; the one that cuts through all the noise; the one that is there to reliably illuminate your dark statistical world of not-yet understood data.

# What's a Cumulative Distribution Function?

I've been working in commerical data science applications for 4+ years now, and I can tell you--I rarely see CDFs, except on my own laptop. So, in case you're not familiar, let's take a moment to explain:  

While a Probability Distribution Function (PDF) measures `P(x=0)` for some probability function `P` over some support variable `x`, a CDF measures `P(x<0)` for that same probability function `P` over the same support `x`.  

Or, for the calculus fans in the audience, a CDF is the integral of the PDF.  
<iframe src="https://math.embed.fun/embed/jekWHsynLThYAB8hiQ3jJu" frameborder="0" width="200" height="155"></iframe>

Most typically, we estimate the PDF of some empirical column or feature in our data by plotting histograms. Histograms can be nice in their immediate visual interpretability, but the list of reasons ends there. It pays to learn to love CDFs, and I can convince you in 7 reasons:  

# 7 Reasons Why CDFs Render Histograms Obsolete

Disclaimer: of course, there's more to the plotting world than PDFs and CDFs. For example, scatter plots serve quite a different function to PDFs and CDFs, in that they are mostly used for getting a first look at the relationship between two variables.  

That said, if your goal is to understand your data's distribution, then there's simply no match for the CDF. So, without further ado:

## 1. Key Values Become Immediately Obvious
With all this talk of histograms, I almost forgot to mention--yes, the box and whisker plot is utterly useless; it is but a shadow of the CDF.

## 2. The Perils of Binning Artifacts, and the Disappearance of Multi-Modality
...  
You might be wondering to yourself now, "but can't I just decrease the bin size, and then the binning artifacts are gone?" Yes, you can! But you can only reduce the bin size as much as your data volume allows you to, which brings me to my next point:

## 3. CDFs Remain Relatively High-Integrity Under Low Data Volume.
## 4. Pontificating on Your Data's Underlying Statistical Distribution Becomes Much More Productive.
## 5. Missing Values and Outliers are Self-Evident.
## 6. Distribution Comparisons are Simply Easy.
## 7. The Data Tells You All its Secrets.
e.g. time modulation

# The Singular Pitfall of CDFs
Communication. The first time I showed a CDF to my product manager, I dove into an explanation of the distribution of the data, my hypotheses for the observed phenomena, everything. She paused for five seconds, and said to me "what am I looking at?".  
I certainly learned my lesson there. And, while I'd like to say I never make the same mistake twice, it's definitely taken me some years to become effective at what I believe to be the most difficult job as a data scientist--communicating highly technical material to non-technical stakeholders.  
After learning my lesson, now, when I communicate CDFs to non-technical stakeholders, I take 20 seconds to carefully guide them through what a CDF is. And I enjoy it. 