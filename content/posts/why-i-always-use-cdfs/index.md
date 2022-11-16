---
title: "The Only Plotting Tool a Data Scientist Needs"
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
***After reading this article, you'll never use a histogram again.***

The modern data scientist must swim in an ocean of plotting techniques. From matplotlib to ggplot2; from plotly to d3.js; from histograms, to box and whisker plots, to scatter plots, to whatever crazy chart seaborn comes up with next; it can be hard for novice data scientists to make sense of it all, drowning under all these different plotting tools! 

--> insert image of raining plot types.

But worry no longer: let [Empirical Cumulative Distribution Functions (ECDFs)](https://en.wikipedia.org/wiki/Empirical_distribution_function) be your trusty speedboat to take you where you need to go; they are the one Exploratory Data Analysis (EDA) companion you truly need; the one that cuts through all the noise; the one that will always be there to reliably illuminate your dark statistical world of not-yet understood data.

# What's an Empirical Cumulative Distribution Function?

## PDFs and CDFs
I've been working in commercial data science applications for 4+ years now, and I can tell you--I rarely see [Cumulative Distribution Functions (CDFs)](https://en.wikipedia.org/wiki/Cumulative_distribution_function), except on my own laptop. So, in case you're not familiar, let's take a moment to explain:  

While a [Probability Distribution Function (PDF)](https://en.wikipedia.org/wiki/Probability_density_function) measures `P(x=X)` for a random draw `x` from some probability function `P` over some support `X`, a CDF measures `P(x<=X)` for that same probability function `P` over the same support `X`.  

![PDF and CDF of Normal Distribution](/images/normal-pdf-and-cdf.png "PDF and CDF of Normal Distribution")

For the calculus fans in the audience, a CDF is the integral of the PDF.  
<iframe src="https://math.embed.fun/embed/jekWHsynLThYAB8hiQ3jJu" frameborder="0" width="200" height="155"></iframe>

## Histograms and ECDFs
Those curves above look nice and smooth, asthey are theoretical curves, displayed by plotting the known equation for the [Normal Distribution](https://en.wikipedia.org/wiki/Normal_distribution).


When we do EDA, we don't know what equation is underlying our data, and so we must estimate the distributions underlying our data. This is most typically done by plotting a histogram. And, just as a histogram provides an empirical approximation of the latent PDF underyling the data, an ECDF provides an empirical approximation of the latent CDF underyling the data:  

![PDF and Histogram of Normal Distribution](/images/normal-pdf-histogram-and-ecdf-cdf.png "PDF and Histogram of Normal Distribution")

Histograms can be nice in their immediate visual interpretability, but the list of reasons ends there. It pays to learn to love ECDFs, and I can convince you in 7 reasons:  

# 7 Reasons Why ECDFs Render Histograms Obsolete

**Disclaimer**: of course, there are other plotting tools out there; ECDFs and Histograms have many Brother and Sistograms. For just one example, scatter plots serve quite a different function to PDFs and CDFs, in that they are mostly used for getting a first look at the relationship between two variables.  

That said, if your goal is to gain a full understanding of one column's distribution, then there's simply no match for the ECDF. So, without further ado:

## 1. Key Quartile Values Become Immediately Obvious

What do I mean when I say "Key Quartile Values"? I mean:
- 0th percentile value (i.e. minimum) of the data;
- 25th percentile value of the data;
- 50th percentile value (i.e. median) of the data;
- 75th percentile value of the data;
- 100th percentile value (i.e. maximum) of the data

This information is readily available in both box and whisker plots and CDFs, but it is quite ambiguous in histograms:

![PDF and Histogram of Normal Distribution](/images/normal-histogram-boxplot-ecdf.png "PDF and Histogram of Normal Distribution")

And there it is. We can see:
- from the box and whisker plot, that the 25th percentile is about `-0.6`, the median `0`, and the 75th percentile about `0.6`;
- from the ECDF, the 25th percentile is about `-0.6`, the median `0`, and the 75th percentile about `0.6`;
- from the histogram... well, we can't really see much. We can guess that the median is at about `0`, but that's about it, and it's only really because of the symmetry of the data.

That's right! With all this talk of histograms, I forgot to mention: the box and whisker plot is decidedly redundant! Though it's true that it can do something that the histogram cannot, it is but a shadow of the CDF.

## 2. ECDFs Remain Relatively High-Integrity Under Low Data Volume.
So far in this article, we've only considered data of `10,000` draws from a normal distribution. How do things start to look when we decrease the amount of data? Let's have a look:
![Histogram and CDF by n_draws](/images/normal-histogram-ecdf-by-n-draws.png "Histogram and CDF by n_draws")

Histograms do a pretty good job of estimating the latent PDF for `10000` draws, and even for `1000` draws; but at `100` draws, it's starting to look a bit dubious. In fact, I can't with much confidence say anymore that it's a normal distribution. The ECDFs, however, remain far more robustly adherent to the underlying CDF, even still at `100` draws!  

You might be wondering to yourself now, "But Ben, why don't we just increase the bin size? Wouldn't that smoothen the histogram, and then it would look like our latent normal distribution again?" Yes, it would, but that introduces a myriad of other issues:

## 3. With Histograms, You Never Know How Many Bins to Use
Well, let's indulge ourselves for a moment: what would the histogram from above look like if we decreased the bin size?
![Histogram and CDF by n_bins](/images/normal-histogram-ecdf-by-n-bins.png "Histogram and CDF by n_bins")
While the 7-bin histogram is still not quite convincingly the normal distribution that we know it to be, it's nonetheless more convincing than the 25-bin histogram. But here's the thing: regardless of how good the histogram _can_ look (so long as you get the number of bins right) an ECDF doesn't require this guessing game of number of bins at all!  
![How Many Bins?](/images/how-many-bins.png "How Many Bins?")  

## 4. The Perils of Binning Bias, or "Where'd My Outlier Go?"
Let's say you still want to use a histogram. So, you just decrease the bin size to accommodate whatever your data volume is, until you get it looking perfect.

--> insert chef's kiss meme

If so, then be careful: if your data has outliers, then your histogram might just lie to you.

In the following charts, I've taken the same Gaussian sampled data from above, but manually added an outlier. Let's see how the outlier looks, depending on the number of bins:
![Histogram and CDF by n_bins](/images/normal-histogram-ecdf-by-n-bins-w-outlier.png "Histogram and CDF by n_bins, with Outlier")  
Depending on the number of bins, the perceived nature of the outlier and the latent distribution underlying the data completely changes:
- When we use `25` bins, we see an outlier out at `X = 11`. But, the bins are a bit noisy; perhaps it's not truly an outlier, so we decrease to `7` bins.
- When we use `7` bins, the hypothesized PDF is now smooth, but now the outlier is out at `X = 10`. What happened? Where exactly in the bin is the outlier? Is it even an outlier?
- When we decrease to `3` bins, the outlier skews the entire distribution, because the bins have to make up for all the space between the distribution mean of `0` and the outlier location of `11`. Looking at the `3` bins distribution, we are no longer sure--is it even a normal distribution?

With the histograms, we saw the outlier creep closer and closer to the main body of the distribution as the number of bins decreases. However, if we look to the corresponding ECDFs, there is no ambiguity whatsoever: by `X = 2.5`, we've seen 99% of the data; then, the CDF traces out a long line to get to the singular outlier at a value of `X = 11`. Voila!

## 5. Visualization Becomes an Artistically Trivial Endeavour.
It's often said that "data scientists are storytellers", and the longer I work in data science, the more I believe this to be true.

We as a data scientists must not only clean data, define metrics, train and deploy models, build data pipelines, and so much more. We also must communicate to our less-technical stakeholders the truth, utility, and business value of our results. To accomplish this endeavour, we weave our insights into compelling stories, ingestible to audiences of all technical levels.  

When all's said and done, these stories turn out more like picture-books than stories, filled with color-coded charts, graphs, and tables. And, a piece of information we often try to communicate is the distribution of some data. And how do we do this? With histograms that end up looking something like this:

How does

## 6. Postulating about Your Data's Underlying Statistical Distribution Becomes Much More Productive.

Even the Kolmogorov-Smirnov Test agrees!

## 7. The Data Tells You All its Secrets.
When you remove 

# The Singular Pitfall of ECDFs
I believe there's a simple reason that ECDFs aren't so popular in commercial data science: communication.  

The first time I showed a CDF to my product manager, I dove into an explanation of the distribution of the data, my hypotheses for the observed phenomena, everything. She paused for five seconds, and said to me "um... what am I looking at?".  

I certainly learned my lesson on that one.  

And, while I'd like to say I never make the same mistake twice, it's definitely taken me some years to become effective at what I believe to be the most difficult job we must do as data scientists: communicating technical material to non-technical stakeholders. After learning my lesson, now, when I communicate CDFs to non-technical stakeholders, I take twenty seconds to carefully guide them through how the ECDF works. And then we can have a more fruitful conversation. 

https://www.andata.at/en/software-blog-reader/why-we-love-the-cdf-and-do-not-like-histograms-that-much.html
