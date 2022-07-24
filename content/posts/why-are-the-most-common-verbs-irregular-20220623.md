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

Back when I was learning Portuguese, I found myself wondering something that most language-learners probably wonder at some point: why the hell are there so many irregular verb conjugations in this language? And why are they so weird? It's like someone purposefully designed this language to be hard to learn!  

As it turns out, somebody didn't design it to be that way. Rather, natural processes designed it so; processes that have been playing out over thousands of years.

## A Day in the Life of a Language Learner

Let's learn some verb conjugations in Portuguese, yay! This will be fun!  
- trabalhar (to work): eu trabalho (I work), ela trabalha (she works), nós trabalhamos (we work), eles trabalham (they work)... cool, that's easy enough to remember.  
- andar (to walk): eu ando (I walk), você anda (you walk), ela anda (she walks), nós andamos (we walk)... cool, that's also easy to remember.  
- ser (to be): eu sou (I am), ela é (she is), nós somos (we are), eles são (they are)... what the word?!

Ugh! Why does "to be" have the most irregular conjugations? And why are they so weird? Oh, how I wish that the less common, less useful verbs had the irregular conjugations instead of "to be". That would make my life so much easier...

## Hey, Who you Callin' "Irregular"?

Before moving any further, let's define an "irregular" conjugation vs a "regular" conjugation. The regular rules for verb conjugation in Portuguese are demonstrated by "trabalhar (to work)" and "andar (to walk)" in the table below:

|                             | _regular<br>rules_ | _trabalhar<br>(to work)_        | _andar<br>(to walk)_       | _vir<br>(to come)_            | _ser<br>(to be)_             |
|-----------------------------|--------------------|---------------------------------|----------------------------|-------------------------------|------------------------------|
| _1st person present_        | -o                 | eu trabalho<br>_(I work)_       | eu ando<br>_(I walk)_      | **eu venho<br>_(I come)_**    | **eu sou<br>_(I am)_**       |
| _3rd person present_        | -a                 | ela trabalha<br>_(she works)_   | ela anda<br>_(she walks)_  | **ela vem<br>_(she comes)_**  | **ela é<br>_(she is)_**      |
| _1st person plural present_ | -mos               | nós trabalhamos<br>_(we work)_  | nós andamos<br>_(we walk)_ | nós vimos<br>_(we come)_      | **nós somos<br>_(we are)_**  |
| _3rd person plural present_ | -m                 | eles trabalham<br>_(they work)_ | eles andam<br>_(I walk)_   | **eles vêm<br>_(they come)_** | **eles são<br>_(they are)_** |

Any conjugation that adheres to the "regular rules" is a regular conjugation; in contrast, any conjugation that deviates from these rules (in bold text in the table above) is an irregular conjugation.

## Where do Irregular Verbs come from?

You might notice in the table above that the verbs are ordered from left to right, by both increasing frequency-of-usage and number of irregular conjugations (in Portuguese, like in English, "to be" and "to come" are two of the most commonly used, most irregular verbs). This correlation between frequency-of-usage and number of irregular conjugations is generally the rule, not an exception, regardless of the language. So, why is this the case?

I started researching this question, and came across an interesting hypothesis by a linguist [/u/bohnicz](https://www.reddit.com/user/bohnicz/) in a [thread on the linguistics subreddit](https://www.reddit.com/r/linguistics/comments/97wfeq/why_are_the_most_common_verbs_always_treated_as/):

> At least for \[Indo-European\] languages, the verbs meaning _to be_ (usually) are the most irregular verbs in the entire language, with a so-called suppletive paradigm consisting of three or more different roots.
> 
> Just take a look at the Old High German forms of _sīn_ 'to be': _bim ~ bin : bist : ist ; birum ~ birun : birut : sind_ (Indicative) _sī : sīst : sī ; sīn : sīt : sīn_ (Subjunctive) This paradigm already contains words formed from three different roots, and we havn't looked at the past tense and conditional mood yet...
> 
> **Irregular verbs tend to be VERY old and highly frequent in use - being highly frequent is in fact what keeps them from becoming "regular" verbs.**

## Use it or Lose it

So, it's less about "where do irregular verbs come from", and more about "where do regular verbs come from". If we think about verbs that have been created in the past 20 years (e.g. "to email", "to text", or "to google") all of them follow the regular verb conjugation patterns of adding an '-ed' to form the past tense (e.g. "I emailed you yesterday" or "I just googled it"). So, the oldest and most irregular verbs, like "to come" and "to be" (with their respective "I came yesterday" and "I was just there"), have been around in our language since a time when conjugation rules were different.  

Furthermore, the more frequently a word is used, the more slowly it evolves over time. And that makes sense. Just think for a second--the word "the" is pronounced correctly by every native English speaker, while almost everyone I know has an answer to the question "what's a word you had only ever read and never heard out loud, and then said it for the first time when you were a teenager, and your pronunciation was completely wrong?"[^1]. Like this, the word "the" is much less susceptible to change (or even disappearance) than some word that numerous teenagers don't know how to pronounce.

## Zipf up your Boots

Thinking about verb frequency-of-usage and how it might have some special relationship with irregularity gets me thinking about Zipf's Law. [Zipf's Law](https://en.wikipedia.org/wiki/Zipf%27s_law), as applied to word frequency in language, states that ([from Wikipedia](https://en.wikipedia.org/wiki/Zipf%27s_law)):

> "...given some corpus of natural language utterances, the frequency of any word is inversely proportional to its rank in the frequency table. Thus the most frequent word will occur approximately twice as often as the second most frequent word, three times as often as the third most frequent word, etc..."

{{< figure src="https://chumley.barstoolsports.com/union/2021/08/27/Screen-Shot-2021-08-27-at-12.57.59-PM.59cc8116.png" >}}  
[Power laws are found in all sorts of emergent systems; not just in language](https://en.wikipedia.org/wiki/Preferential_attachment). Could it be that verb frequency-of-usage also follows power law like behavior, and that this also reflects in how irregular the verb's conjugations are?

## Show me the Data

Is verb-frequency versus rank Zipfian? To what degree is verb irregularity correlated with verb-frequency? To measure these things, I use [a database of movie subtitles in Brazilian Portuguese](https://www.kevintang.org/Tools.html), built by [Dr. Kevin Tang](https://www.linkedin.com/in/kevintangcantab/).[^2]

### Verb Frequency by Rank
{{< plotly json="/plotly_charts/verb_rank_vs_frequency.json" height="500px" >}}  

Zipfy-doo-da, can somebody say "jackpot"? This looks positively Zipfian, promisingly power(law)ful. Let's take a look at irregularity versus frequency--


### Verb Irregularity by Verb Frequency of Usage

I measure a verb's irregularity as "what fraction of this verb's conjugations are irregular"[^3]. Noting this in the table from above, we'd have the following irregularity scores:

|                             | _regular<br>rules_ | _trabalhar<br>(to work)_        | _andar<br>(to walk)_       | _vir<br>(to come)_            | _ser<br>(to be)_             |
|-----------------------------|--------------------|---------------------------------|----------------------------|-------------------------------|------------------------------|
| _1st person present_        | -o                 | eu trabalho<br>_(I work)_       | eu ando<br>_(I walk)_      | **eu venho<br>_(I come)_**    | **eu sou<br>_(I am)_**       |
| _3rd person present_        | -a                 | ela trabalha<br>_(she works)_   | ela anda<br>_(she walks)_  | **ela vem<br>_(she comes)_**  | **ela é<br>_(she is)_**      |
| _1st person plural present_ | -amos              | nós trabalhamos<br>_(we work)_  | nós andamos<br>_(we walk)_ | nós vimos<br>_(we come)_      | **nós somos<br>_(we are)_**  |
| _3rd person plural present_ | -am                | eles trabalham<br>_(they work)_ | eles andam<br>_(I walk)_   | **eles vêm<br>_(they come)_** | **eles são<br>_(they are)_** |
| _irregularity_[^4]          |                    | 0                               | 0                          | 0.75                          | 1.0                          |

So, this table roughly increases in both frequency-of-usage and irregularity, from left to right. With this intuition, let's now take a look at this phenomenon in the Portuguese movie subtitle dataset:

{{< plotly json="/plotly_charts/verb_frequency_vs_irregularity.json" height="500px" >}}  

It's nice when the numbers match the intuition! There's a nice correlation between verb irregularity and frequency-of-usage. Hover over the plot to see the individual verbs ;)

## Final Thoughts

It can be a bit frustrating for language-learners that the most common verbs have the most irregular conjugations; myself included. However, this phenomenon is also a window into the beauty of language. Insofar as a language is a naturally evolving system, it adheres to certain self-organizing behaviors that other emergent systems do. For example, the human brain makes up for about [3% of the weight of an average adult-male](https://en.wikipedia.org/wiki/Brain_size#:~:text=The%20adult%20human%20brain%20weighs,in%20women%20about%201200%20g.), but [consumes a disproportionate 15% of the heart's cardiac output](https://en.wikipedia.org/wiki/Cerebral_circulation). In a sense, the verbs "to be" and "to go" in Portuguese, like the human brain in the human body, is where most of the magic happens, and so they receive the most attention. Languages, like the human body, are living, breathing emergent systems.  

### Denkmit

One of the joys of learning a new language is discovering yourself in the language. Each new language is like a new color palette or musical instrument for self-expression. Everybody wields their language in a unique way; everybody has a unique voice. As I've begun to learn my next language (Hebrew), it's time to retool. There are a lot of language-learning tools out there, but I was seeking something that would cater to my specific voice, and help me to pay closer attention where I needed to. I couldn't find the right tool, so I decided to create it.  

[Denkmit](https://example.com/) creates language learning flashcards from your own WhatsApp chats. It prioritizes learning words and phrases that you and your conversation partners use most often, and that you have the most difficulty remembering. All language-learning requires some small amount of memorization, and Denkmit helps you to do it as effectively as possible by teaching you your own words, while reminding you of the context in which you learned them. Denkmit helps you to learn your language, your way. Drop your email in the website to get early access to the tool ;)  

[^1]: Mispronunciations of words can be quite humorous, if we allow them to be. For me, "epilogue" was pronounced "eh-pill-oh-gew" instead of "eh-pill-og"; for my mom, "bicarbonate" was pronounced "bick-er-bone-it" instead of "by-car-bun-it"; for my little brother, "decadence" was pronounced "deck-a-denk-ee" instead of "deck-a-dense"; and for my high school math teacher, "infrared" was pronounced "in-fraired" instead of "in-fra-red". What was it for you? Drop it in the comments ;)
[^2]: There's a considerable difference between written language and spoken language, and any linguistics study of natural language data is better off using spoken language. This is because written language is premeditated, edited, and curated, while only spoken language is truly spontaneous and generative. The only problem is that spoken language isn't usually recorded ([unless if you have an Alexa in your home](https://www.washingtonpost.com/technology/2019/05/06/alexa-has-been-eavesdropping-you-this-whole-time/)). So, movie subtitle data is a pretty decent approximation at spoken language, as [Dr. Tang's paper about the movie-subtitle dataset demonstrates](https://www.researchgate.net/publication/238885981_A_61_Million_Word_Corpus_of_Brazilian_Portuguese_Film_Subtitles_as_a_Resource_for_Linguistic_Research). As a curious side-note on this point, everything that we know about Latin actually comes from [Classical Latin](https://en.wikipedia.org/wiki/Classical_Latin), or Literary Latin, i.e. the Latin that was written down. However, all of today's modern romance languages actually descend from [Vulgar Latin](https://en.wikipedia.org/wiki/Vulgar_Latin), the tongue spoken by the Roman Empire commoners.
[^3]: I scraped data on verb-conjugations and their irregularity from a common Portuguese verb-conjugation website, https://www.conjugacao.com.br/. As an example, see the conjugations for [the verb "ir" (to go)](https://www.conjugacao.com.br/verbo-ir/) (irregular conjugations are denoted as such with an asterisk "\*") .
[^4]: This is an abridged conjugation table. It ignores all the other verb tenses, e.g. future, past participle, subjunctive, etc. The irregularity is measured only on the abridged table, to be as clearly demonstrative as possible.

<!--
    Notes from Brian:
        - connection between irregularity, oldness, and frequency of usage is unclear. make it clearer.
        - change title of section "Hey, Who you Callin' "Irregular"?" to something that doesn't match the title of the article.
        - change language of table to portuguese, or language of plots to english.
        - https://superpowerspeech.com/2016/09/irregular-verbs-where-do-they-come-from-and-how-can-we-help.html
        - https://www.reddit.com/r/linguistics/comments/15k4kz/why_do_irregular_verbs_exist/
-->