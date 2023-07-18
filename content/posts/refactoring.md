---
title: "Refactoring: A Data Science Survival Guide"
date: 2023-07-18T09:47:45+02:00
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
> _"When I talk to software developers who have been working on a system for a while, I often hear that they were able to make progress rapidly at first, but now it takes much longer to add new features. Every new feature requires more and more time to understand how to fit it into the existing code base, and once it's added, bugs often crop up that take even longer to fix. The code base starts looking like a series of patches covering patches, and it takes an exercise in archaeology to figure out how things work. This burden slows down adding new features, to the point that developers wish they could start again from a blank slate. <br><br>But some teams report a different experience. They find they can add new features **faster** because they can leverage the existing things by quickly building on what's already there."_

![Logarithmic vs Exponential Returns](/images/log_vs_exponential_returns.png)

The difference between these two scenarios is the quality of the software's internal design.
But what does this mean for data scientists? We're not software engineers, right? Right??

# Here's My Model, Thank You Very Much

A day in the life of a data scientist involves equations and models flying through our heads, pouring into our keyboards, leaving trails of messy code and messier whiteboards scattered in our wakes. Programming is a means to an end for us; there's no need to write code cleanly! The job will be short after all, we just need to run one experiment, or just one query! Once we have gotten our analysis or our model just right, we hand over our code to the engineers, say "voila", wash our hands; we whistle our way on to the next experiment, with full confidence that the engineers will take care of everything.

![Logarithmic vs Exponential Returns, Expectation](/images/log_vs_exponential_returns_expectation.png)

Things rarely work so smoothly, though--if the engineers can even read our poorly styled code (e.g. with variables like `df_original`, `df_final`, and `df_final_2`), they quickly find out that it doesn't scale, that they don't know how to conform the code to existing architecture, or that it's so brittle that it breaks when they so much as touch it. Sometimes, if there's enough value, the data scientist gets brought back in to the picture, and more time is budgeted for the project; other times, however, the project just fizzles and dies.

![Logarithmic vs Exponential Returns, Reality](/images/log_vs_exponential_returns_reality.png)

Don't be the data scientist that engineers dread working with. We must be empathetic to our engineering compatriots. We must write clean code. And to do so, we must refactor.

But how?

# How to Refactor

In "Refactoring", Martin Fowler describes the two hats that all programmers must wear: the "add features" hat, and the "refactor" hat.

When we are wearing the "add features" hat, we are writing new code; and when we are wearing the "refactoring" hat, we are seeking bad code (e.g. duplicated code, nested loops or conditionals, mysterious names) and snuffing it out.

As data scientists, we look really good in the "add features" hat, whipping up exploratory charts and rolling out models... but most of us don't even own the "refactor" hat!

So, how can you know what bad code looks like? And more importantly, how can you know how to refactor? These next few sections sequentially improve some data science code, showing precise examples of bad code and the refactoring motifs that alleviate them:

## 1. Fixing Duplicated Code by Pulling Up a Method
**Bad Code Smell**: Duplicated Code  
**Refactoring Motif**: Pull Up Method

Imagine you are trying to compare the performance of `XGBoost` vs `RandomForest` on some data:

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier, AdaBoostClassifier
from xgboost import XGBClassifier


def load_df():
    df = pd.read_csv('../data/titanic_dataset.csv')
    df["is_male"] = df["Sex"] == "male"
    return df


def evaluate_adaboost_model(data):
    X = data[["is_male", "SibSp", "Pclass", "Fare"]]
    y = data['Survived']
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
    
    model = AdaBoostClassifier()
    model.fit(X_train, y_train)
    
    accuracy = model.score(X_test, y_test)
    return accuracy


def evaluate_random_forest_model(data):
    X = data[["is_male", "SibSp", "Pclass", "Fare"]]
    y = data['Survived']
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
    
    model = RandomForestClassifier()
    model.fit(X_train, y_train)
    
    accuracy = model.score(X_test, y_test)
    return accuracy


df = load_df()

accuracy_xgboost = evaluate_adaboost_model(df)
print(f"XGBoost Accuracy: {accuracy_xgboost:.3f}")

accuracy_random_forest = evaluate_random_forest_model(df)
print(f"Random Forest Accuracy: {accuracy_random_forest:.3f}")
```

And now, you want to add an additional comparison for `XGBoost`, and you smell something is amiss. Are you really going to create a new function, `evaluate_xgboost_model`? `evaluate_adaboost_model` and `evaluate_random_forest_model` already appear to have a lot of repeated code. Let's rather pull up the function `evaluate_model`, which will take in the model of interest as an argument:

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier, AdaBoostClassifier
from xgboost import XGBClassifier


def load_df():
    df = pd.read_csv('../data/titanic_dataset.csv')
    df["is_male"] = df["Sex"] == "male"
    return df


def evaluate_model(data, model_constructor):
    X = data[["is_male", "SibSp", "Pclass", "Fare"]]
    y = data['Survived']
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
    
    model = model_constructor()
    model.fit(X_train, y_train)
    
    accuracy = model.score(X_test, y_test)
    return accuracy


df = load_df()

accuracy_xgboost = evaluate_model(df, AdaBoostClassifier)
print(f"XGBoost Accuracy: {accuracy_xgboost:.3f}")

accuracy_random_forest = evaluate_model(df, RandomForestClassifier)
print(f"Random Forest Accuracy: {accuracy_random_forest:.3f}")
```

Instead of immediately putting on the "add features" hat, we took a moment, and put on the "refactor hat". And look what we were able to do! Not only has our code gotten shorter, but, now adding an extra model-evaluation for `XGBoost` (and any other model) will now be trivial!

## 2. Fixing a Mysterious Name by Renaming a Variable
**Bad Code Smell**: Mysterious Name
**Refactoring Motif**: Rename Variable

Now, let's say you got good results (yay!), so you show it to your colleague, who immediately interrupts you by saying "What's actually in `df` though?".

Often, renaming a variable has the highest ratio of reward to effort of any refactoring motif. With a little bit of thought and just a few keystrokes, you can preemptively answer questions about the data that's getting passed around your code. In this case, it should be easy to come up with a clear name that communicates precisely what's inside of our dataframe; doing so will answer our colleague's question and any more questions like it:

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier, AdaBoostClassifier
from xgboost import XGBClassifier


def load_titanic_passengers_df():
    titanic_passengers_df = pd.read_csv('../data/titanic_dataset.csv')
    titanic_passengers_df["is_male"] = titanic_passengers_df["Sex"] == "male"
    return titanic_passengers_df


def evaluate_model(data, model_constructor):
    X = data[["is_male", "SibSp", "Pclass", "Fare"]]
    y = data['Survived']
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
    
    model = model_constructor()
    model.fit(X_train, y_train)
    
    accuracy = model.score(X_test, y_test)
    return accuracy


titanic_passengers_df = load_df()

accuracy_xgboost = evaluate_model(titanic_passengers_df, AdaBoostClassifier)
print(f"XGBoost Accuracy: {accuracy_xgboost:.3f}")

accuracy_random_forest = evaluate_model(titanic_passengers_df, RandomForestClassifier)
print(f"Random Forest Accuracy: {accuracy_random_forest:.3f}")
```

Now, there's no doubt at all--every row in the newly named `titanic_passengers_df` represents a distinct passenger from the Titanic. Nice!

## 3. Fix Magic Values by Extracting Variables
**Bad Code Smell**: Magic Values
**Refactoring Motif**: Extract Variable

So now you've shown it to your colleagues, and they all agree: you've got some great results! Now, it's time to share it with some engineers in your team; the only problem is that they are not so familiar with data science and machine learning, and they immediately ask "what are these string values at the top of the `evaluate_model` function?". Instead of saying "they're features and a target for our model, silly engineer!", we can answer all engineers' such questions permanently by extracting the requisite variables from our code:

```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier, AdaBoostClassifier
from xgboost import XGBClassifier


MODEL_FEATURES = ["is_male", "SibSp", "Pclass", "Fare"]
MODEL_TARGET = "Survived"
TRAIN_TEST_SPLIT_FRACTION = 0.2


def load_titanic_passengers_df():
    titanic_passengers_df = pd.read_csv('../data/titanic_dataset.csv')
    titanic_passengers_df["is_male"] = titanic_passengers_df["Sex"] == "male"
    return titanic_passengers_df


def evaluate_model(data, model_constructor):
    X = data[MODEL_FEATURES]
    y = data[MODEL_TARGET]
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=TRAIN_TEST_SPLIT_FRACTION)
    
    model = model_constructor()
    model.fit(X_train, y_train)
    
    accuracy = model.score(X_test, y_test)
    return accuracy


titanic_passengers_df = load_df()

accuracy_xgboost = evaluate_model(titanic_passengers_df, AdaBoostClassifier)
print(f"XGBoost Accuracy: {accuracy_xgboost:.3f}")

accuracy_random_forest = evaluate_model(titanic_passengers_df, RandomForestClassifier)
print(f"Random Forest Accuracy: {accuracy_random_forest:.3f}")
```

With the `MODEL_FEATURES` and `MODEL_TARGET` variables extracted, we significantly decrease ambiguity for future readers (and we extracted the `TRAIN_TEST_SPLIT_FRACTION` in case of future questions regarding that as well).

There's still plenty more refactoring that can be done to this code, but for now, it's enough. In summary, we:
- Fixed duplicated code by pulling up the `evaluate_model()` function;
- Fixed the mysterious `df` name by renaming it to `titanic_passengers_df`; and
- Fixed the magic values in our code by extracting the variables `MODEL_FEATURES`, `MODEL_TARGET`, and `TRAIN_TEST_SPLIT_FRACTION`.


# Conclusion

If you made it all the way to the end of this article, you've probably at least once looked at some code that you'd written and thought to yourself--"how did I get into this mess? Every change is taking longer and longer than the last! Maybe I should just start from scratch..."

To start over, or to heal and rebuild? This is a hard question to answer. That said, in writing software, as in nurturing human relationships, healing and rebuilding can often be more difficult than starting over, but it is usually more robust and is almost always far more rewarding. Refactoring enables us to discover higher levels of internal design in the things we have already created, rendering us simultaneously more respectful of the past and more robust to the future.

---

{{< contact message="Liked what you read? Feel free to reach out on " >}}