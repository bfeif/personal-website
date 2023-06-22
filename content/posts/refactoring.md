---
title: "Refactoring: A Data Science Survival Guide"
date: 2023-6-25T09:47:45+02:00
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
---
***"Healing takes courage, and we all have courage, even if we have to dig a little to find it." - Tori Amos***

# A Tale of Two Programmers

In "Refactoring", Martin Fowler cites the common example:
"Many engineers cite software rot... Some people say something different, that as they add code, the next feature gets easier and easier to add"

<!-- Insert image -->

But what does this mean for data scientists? We're not software engineers, right? Right??

# Here's My Model, Thank You Very Much

A day in the life of a data scientist involves equations and models flying through our heads, pouring into our keyboards, leaving trails of messy code and messy whiteboards in our wakes. Programming is a means to an end for us; there's no need to write code cleanly! The job will be short after all, we just need to run one experiment, or just one query! Once we have gotten our analysis or our model just right, we hand over our code to the engineers, say "voila", wash our hands, and whistle our way on to the next experiment, with full confidence that the engineers will take care of everything.

<!-- Insert image -->

Things rarely work so smoothly, though--if the engineers can even read our poorly styled code (e.g. with variables like `df_original`, `df_final`, and `df_final_2`), they quickly find out that it doesn't scale, that they don't know how to conform the code to existing architecture, or that it's so brittle that it breaks when they so much as touch it.

<!-- Expectation vs reality, expectation is iron man shooting a model at a tank -->

Don't be the data scientist that engineers dread working with. We must be empathetic to our engineering compatriots. We must write clean code. And to do so, we must refactor.

But how?

# How to Refactor

In "Refactoring", Martin Fowler describes the two hats that all programmers must wear: the "add features" hat, and the "refactor" hat. When we are wearing the "add features" hat, we are writing code, adding new functionality; and when we are wearing the "refactoring" hat, we are seeking "bad code smells" and snuffing them out.

Bad code smells include things like duplicated code, nested loops or conditionals, or mysterious names, and they all serve to obfuscate the readability (smellability?) of our code to the next reader.

As data scientists, we look really good in the "add features" hat, whipping up exploratory charts and rolling out models... but most of us don't even own the "refactor" hat! These next few sections give examples of bad code smells and the refactoring motifs that alleviate them:

## Fixing Duplicated Code by ____

## Fixing

# Conclusion

If you're reading this article, you've probably at least once looked at some code that you'd written and thought to yourself--"how did I get into this mess? Every change is taking longer and longer than the last! Maybe I should just start from scratch..." 

To start over, or to heal and rebuild? In writing software, as in nurturing human relationships, healing and rebuilding can often be more difficult than starting over, but it is usually more robust and far more rewarding.

This process of healing and rebuilding software is referred to as "refactoring", and it is most aptly defined as "the process of improving code's internal structure while maintaining its external behavior".

For a happy, collaborative, productive data science career, learning how to refactor code is an integral step along the journey.

---

{{< contact message="Liked what you read? Feel free to reach out on " >}}