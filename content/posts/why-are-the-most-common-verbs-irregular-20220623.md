---
title: "Why are the Most Common Verbs Irregular?"
date: 2022-07-12T09:47:45+02:00
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
plotly: true
# comments: true
---

Recently, I've started learning Hebrew, and I found myself wondering something that most language-learners probably wonder at some point: why the hell are there so many irregular verb conjugations in this language? And why are they so weird? It's like someone purposely designed this language to be hard to learn!  

As it turns out, somebody didn't design it to be that way; but rather, a natural process designed it so, one that has been playing out over thousands of years...

## A Day in the Life of a Language Learner

Let's learn some verb conjugations in English, yay! This will be fun!  
- To walk: I walk, you walk, she walks; I walked, you walked, she walked... cool, that's easy to remember.  
- To play: I play, you play, she plays; I played, you played, she played... cool, that's also easy to remember.  
- To be: I am, you are, she is; I was, you were, she was... what the word?!

Ugh! Why does "to be" have the most irregular conjugations? And why are they so weird? Oh, how I wish that the less common, less useful verbs had the irregular conjugations instead of "to be". That would make my life so much easier...

## Hey, Who you Callin' "Irregular"?

Before moving any further, let's define an "irregular" conjugation vs a "regular" conjugation. In English, the regular rules for verb conjugation are demonstrated by "to walk" and "to play" in the table below:

|                      | _to walk_  | _to play_  | _to come_     | _to be_      |
|----------------------|------------|------------|--------------|--------------|
| _1st person present_ | I walk     | I play     | I come       | **I am**     |
| _2nd person present_ | you walk   | you play   | you come     | **you are**  |
| _3rd person present_ | she walks  | she plays  | she comes    | **she is**   |
| _1st person past_    | I walked   | I played   | **I came**   | **I was**    |
| _2nd person past_    | you walked | you played | **you came** | **you were** |
| _3rd person past_    | she walked | she played | **she came** | **she was**  |

To conjugate the 3rd person present, an "-s" is added to the infinitive verb, and to conjugate all past tense conjugations, an "-ed" is added to the infinitive verb. Any conjugations that deviate from these rules, e.g. the past tense conjugations of "to come" and all conjugations of "to be", are called irregular.

## Why are the Most Common Verbs Irregular? 

You might notice in the table above that the verbs are ordered from left to right, by both increasing frequency-of-usage and number of irregular conjugations. This correlation between frequency-of-usage is generally the rule, not an exception, regardless of language. So, why is this the case?

> "Hey, I was thinking, let's make the most common verbs really hard for students to learn. What do you think?"  
> "That's a good one! Let's also give them 6 books for summer-reading, yeah?"  
> "Damn, you're good. Here's a raise."  

Though compelling, it's unlikely that prehistoric language teachers got together in a cave back when language was forming and decided to make the most common verbs irregular. Is there any other possible explanation?

I started researching this question, and came across an interesting hypothesis by a linguist [/u/bohnicz](https://www.reddit.com/user/bohnicz/) in a [thread on the linguistics subreddit](https://www.reddit.com/r/linguistics/comments/97wfeq/why_are_the_most_common_verbs_always_treated_as/):

> At least for \[Indo-European\] languages, the verbs meaning _to be_ (usually) are the most irregular verbs in the entire language, with a so-called suppletive paradigm consisting of three or more different roots.
> 
> Just take a look at the Old High German forms of _sīn_ 'to be': _bim ~ bin : bist : ist ; birum ~ birun : birut : sind_ (Indicative) _sī : sīst : sī ; sīn : sīt : sīn_ (Subjunctive) This paradigm already contains words formed from three different roots, and we havn't looked at the past tense and conditional mood yet...
> 
> **Irregular verbs tend to be VERY old and highly frequent in use - being highly frequent is in fact what keeps them from becoming "regular" verbs.**

## Use it or Lose it

The more frequently a word is used, the more slowly it evolves over time. And that makes sense. Just think for a second--the word "the" is pronounced correctly by every native English speaker, while almost everyone I know has an answer to the question "what's a word you had only ever read and never heard out loud, and then said it for the first time when you were decently old, and your pronunciation was completely wrong?"[^1].

This idea is so cool. I especially like /u/bohnicz's thought that "irregular verbs tend to be VERY old". It makes me feel like, when I'm saying a common phrase like "this is good", I'm reaching back into ancient civilizations and living for a brief second in history books.

## Zipf up your Boots

Thinking about verb frequency-of-usage and how it might have some special relationship with irregularity gets me thinking about Zipf's Law. [Zipf's Law](https://en.wikipedia.org/wiki/Zipf%27s_law), as applied to word frequency in language, states that ([from Wikipedia](https://en.wikipedia.org/wiki/Zipf%27s_law)):

> "...given some corpus of natural language utterances, the frequency of any word is inversely proportional to its rank in the frequency table. Thus the most frequent word will occur approximately twice as often as the second most frequent word, three times as often as the third most frequent word, etc..."

{{< figure src="https://chumley.barstoolsports.com/union/2021/08/27/Screen-Shot-2021-08-27-at-12.57.59-PM.59cc8116.png" >}}  
[Power laws are observed in all sorts of emergent systems; not just in language](https://en.wikipedia.org/wiki/Preferential_attachment). Could it be that verb-frequency-of-usage also follows power law like behavior, and that this reflects in how irregular the verb's conjugations are?

## Show me the Data

Is verb-frequency by rank Zipfian? To what degree is verb irregularity correlated with verb-frequency? To measure these things, I use [a database of movie subtitles in Brazilian Portuguese](https://www.kevintang.org/Tools.html), built by [Dr. Kevin Tang](https://www.linkedin.com/in/kevintangcantab/).[^2]

### Verb Frequency by Rank
{{< plotly json="/plotly_charts/verb_rank_vs_frequency.json" height="500px" >}}  

Zipfy-doo-da, can somebody say "jackpot"? This looks positively Zipfian, even promisingly power(law)ful. Let's take a look at irregularity versus frequency--


### Verb Irregularity by Verb Frequency of Usage

I measure a verb's irregularity as "what fraction of this verb's conjugations are irregular". So, noting this in the table from above, we'd have:

|                      | _to walk_  | _to play_  | _to come_    | _to be_      |
|----------------------|------------|------------|--------------|--------------|
| _1st person present_ | I walk     | I play     | I come       | **I am**     |
| _2nd person present_ | you walk   | you play   | you come     | **you are**  |
| _3rd person present_ | she walks  | she plays  | she comes    | **she is**   |
| _1st person past_    | I walked   | I played   | **I came**   | **I was**    |
| _2nd person past_    | you walked | you played | **you came** | **you were** |
| _3rd person past_    | she walked | she played | **she came** | **she was**  |
| _irregularity_ [^3]  | 0          | 0          | 0.5          | 1.0          |

So, this table roughly increases in both frequency-of-usage and irregularity, from left to right. Let's take a look at this in the SUBTLEX dataset:

{{< plotly json="/plotly_charts/verb_frequency_vs_irregularity.json" height="500px" >}}  

And this matches our intuition! There's a nice correlation between 

## Final Thoughts

It can be a bit frustrating for language-learners that the most common verbs have the most irregular conjugations.  
There can be a lot of pressure in language education, as in all education, to measure students by their deviation from a perfect correctness. Next time you hear someone mispronouncing something, take a pause before you correct them--perhaps they're onto something. Perhaps it might just be the way that the word is pronounced a few thousand years from now.  
I've recently begun learning my fifth language (Hebrew), and, as with every new one that I learn, it's time to retool! I was looking for something to help me learn the specific natural language that I myself use (i.e. I don't need to learn beach-volleyball vocabulary, but boy do I need chess vocabulary), but I couldn't find anything. So, I began working on my new language learning app (as a successor to the one I built for learning German)


[^1]: Mispronunciations of words can be quite humorous, if we allow them to be. For me, "epilogue" was pronounced "eh-pill-oh-gew" instead of "eh-pill-og"; for my mom, "bicarbonate" was pronounced "bick-er-bone-it" instead of "by-car-bun-it"; for my little brother, "decadence" was pronounced "deck-a-denk-ee" instead of "deck-a-dense"; and for my high school math teacher, "infrared" was pronounced "in-fraired" instead of "in-fra-red". What was it for you? Drop it in the comments ;)
[^2]: There's a considerable difference between written language and spoken language, and any linguistics study of natural language data is better off using spoken language. This is because written language is premeditated, edited, and curated, while only spoken language is truly spontaneous and generative. The only problem is that spoken language isn't usually recorded ([unless if you have an Alexa in your home](https://www.washingtonpost.com/technology/2019/05/06/alexa-has-been-eavesdropping-you-this-whole-time/)). So, movie subtitle data is a pretty decent approximation at spoken language, as [Dr. Tang's paper about the SUBTLEX dataset demonstrates](https://www.researchgate.net/publication/238885981_A_61_Million_Word_Corpus_of_Brazilian_Portuguese_Film_Subtitles_as_a_Resource_for_Linguistic_Research).  
Further on this point, everything we know about Latin actually comes from [Classical Latin](https://en.wikipedia.org/wiki/Classical_Latin), or Literary Latin, i.e. the Latin that was written down. All of today's modern romance languages actually descend from [Vulgar Latin](https://en.wikipedia.org/wiki/Vulgar_Latin), the tongue spoken by the Roman Empire commoners.
[^3]: This is an abridged conjugation table. It ignores all the other verb tenses, e.g. future, past participle, subjunctive, etc. The irregularity is measured only on the abridged table, to be as clearly demonstrative as possible.