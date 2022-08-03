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

Back when I was learning Portuguese, I often found myself wondering something that most language-learners probably wonder at some point: why the hell are there so many irregular verb conjugations in this language? And why are they so weird? It's like someone purposefully designed this language to be hard to learn!  

As it turns out, somebody didn't design it to be that way. Rather, natural processes designed it so; processes that have been playing out over thousands of years.

## A Day in the Life of a Language Learner

Let's learn some verb conjugations in Portuguese, yay! This will be fun!  
- andar (to walk): eu ando (I walk), ela anda (she walks), nós andamos (we walk), eles andam (they walk)... cool, that's easy enough to remember.  
- chegar (to arrive): eu chego (I arrive), ela chega (she arrives), nós chegamos (we arrive), eles chegam (they arrive)... cool, that's also easy to remember.  
- ser (to be): eu sou (I am), ela é (she is), nós somos (we are), eles são (they are)... what the word?!

Ugh! Why does "to be" have the most irregular conjugations? And why are they so weird? Oh, how I wish that the less common, less useful verbs had the irregular conjugations instead of "to be". That would make my life so much easier...

## Hey, Who you Callin' "Irregular"?

Before moving any further, let's define an "irregular" conjugation vs a "regular" conjugation. The regular rules for verb conjugation in Portuguese are demonstrated by "andar (to walk)" and "chegar (to arrive)" in the table below:

|                             | _regular<br>rules_ | _andar<br>(to walk)_       | _chegar<br>(to arrive)_        | _dar<br>(to give)_            | _ser<br>(to be)_             |
|-----------------------------|--------------------|----------------------------|--------------------------------|-------------------------------|------------------------------|
| _1st person present_        | -o                 | eu ando<br>_(I walk)_      | eu chego<br>_(I arrive)_       | **eu dou<br>_(I give)_**      | **eu sou<br>_(I am)_**       |
| _3rd person present_        | -a                 | ela anda<br>_(she walks)_  | ela chega<br>_(she arrives)_   | **ela dá<br>_(she gives)_**   | **ela é<br>_(she is)_**      |
| _1st person plural present_ | -amos              | nós andamos<br>_(we walk)_ | nós chegamos<br>_(we arrive)_  | nós damos<br>_(we give)_      | **nós somos<br>_(we are)_**  |
| _3rd person plural present_ | -am                | eles andam<br>_(I walk)_   | eles chegam<br>_(they arrive)_ | **eles dão<br>_(they give)_** | **eles são<br>_(they are)_** |

Any conjugation that adheres to the "regular rules" (in unbolded text in the table above) is a regular conjugation; in contrast, any conjugation that deviates from these rules (in bold text in the table above) is an irregular conjugation.

## Where do Irregular Verbs come from?

You might notice in the table above that the verbs are ordered from left to right, by both increasing frequency-of-usage and number of irregular conjugations (in Portuguese, like in English, "to give" and "to be" are two of the most commonly used, most irregular verbs). This correlation between frequency-of-usage and number of irregular conjugations is generally the rule, regardless of the language. So, why is this the case?

I started researching this question, and came across an interesting hypothesis by a linguist [/u/bohnicz](https://www.reddit.com/user/bohnicz/) in a [thread on the linguistics subreddit](https://www.reddit.com/r/linguistics/comments/97wfeq/why_are_the_most_common_verbs_always_treated_as/):

> At least for \[Indo-European\] languages, the verbs meaning _to be_ (usually) are the most irregular verbs in the entire language, with a so-called suppletive paradigm consisting of three or more different roots.
> 
> Just take a look at the Old High German forms of _sīn_ 'to be': _bim ~ bin : bist : ist ; birum ~ birun : birut : sind_ (Indicative) _sī : sīst : sī ; sīn : sīt : sīn_ (Subjunctive) This paradigm already contains words formed from three different roots, and we havn't looked at the past tense and conditional mood yet...
> 
> **Irregular verbs tend to be VERY old and highly frequent in use - being highly frequent is in fact what keeps them from becoming "regular" verbs.**

## Use it or Lose it

So, it's less about where the _irregular_ verbs come from, and more about where the _regular_ verbs come from. If we think about verbs that have been created in the past 20 years (e.g. "to email", "to text", or "to google") all of them follow the regular verb conjugation patterns of adding an '-ed' to form the past tense (e.g. "I emailed you yesterday" or "I just googled it"). With this, the oldest, most irregular, most frequent verbs (like "to give" and "to be") have been around in our language since a time when conjugation rules were different. All the other old verbs that were birthed alongside "to give" or "to be" died out due to irrelevance, and new verbs were created to define the new concepts we discovered as our world unfolded.[^1]

## Show me the Data

To understand the numerical relationship between verb frequency-of-usage and verb irregularity, we need to measure the two quantities from data.  

To measure verb frequency-of-usage, I use [a database of movie subtitles in Brazilian Portuguese](https://www.kevintang.org/Tools.html), built by [linguist Kevin Tang](https://www.linkedin.com/in/kevintangcantab/).[^2]  

To measure verb irregularity, I scrape data on verb-conjugations and their irregularity from [a popular Portuguese verb-conjugation website](https://www.conjugacao.com.br/). I then measure verb irregularity as "what fraction of this verb's conjugations are irregular"[^3].

Including these measurements in our table from above, we get the following frequencies and irregularities, noted in the bottom two rows:

|                             | _regular<br>rules_ | _andar<br>(to walk)_       | _chegar<br>(to arrive)_        | _dar<br>(to give)_            | _ser<br>(to be)_             |
|-----------------------------|--------------------|----------------------------|--------------------------------|-------------------------------|------------------------------|
| _1st person present_        | -o                 | eu ando<br>_(I walk)_      | eu chego<br>_(I arrive)_       | **eu dou<br>_(I give)_**      | **eu sou<br>_(I am)_**       |
| _3rd person present_        | -a                 | ela anda<br>_(she walks)_  | ela chega<br>_(she arrives)_   | **ela dá<br>_(she gives)_**   | **ela é<br>_(she is)_**      |
| _1st person plural present_ | -amos              | nós andamos<br>_(we walk)_ | nós chegamos<br>_(we arrive)_  | nós damos<br>_(we give)_      | **nós somos<br>_(we are)_**  |
| _3rd person plural present_ | -am                | eles andam<br>_(I walk)_   | eles chegam<br>_(they arrive)_ | **eles dão<br>_(they give)_** | **eles são<br>_(they are)_** |
| **_frequency_**             |                    | **0.004**                  | **0.007**                      | **0.015**                     | **0.154**                    |
| **_irregularity[^4]_**      |                    | **0**                      | **0**                          | **0.75**                      | **1.0**                      |

As mentioned earlier, this table increases from left to right by both frequency-of-usage and irregularity; and now we have the numbers to show it! With this intuition, let's take a look at this phenomenon for the top 50 most common verbs:

{{< plotly json="/plotly_charts/verb_frequency_vs_irregularity.json" height="500px" >}}  

It's nice when the numbers match the intuition! There's a correlation between verb irregularity and frequency-of-usage; hover over the plot to see the individual verbs ;)

## Zipf up your Boots

Thinking about verb frequency-of-usage gets me further thinking about Zipf's Law. Zipf's Law, as applied to word frequency in language, states that ([from Wikipedia](https://en.wikipedia.org/wiki/Zipf%27s_law)):

> "...given some corpus of natural language utterances, the frequency of any word is inversely proportional to its rank in the frequency table. Thus the most frequent word will occur approximately twice as often as the second most frequent word, three times as often as the third most frequent word, etc..."

{{< figure src="https://chumley.barstoolsports.com/union/2021/08/27/Screen-Shot-2021-08-27-at-12.57.59-PM.59cc8116.png" >}}  

[Power laws are found in all sorts of emergent systems; not just in language](https://en.wikipedia.org/wiki/Preferential_attachment). Could it be that verb frequency-of-usage also follows power law like behavior? Let's have a look:

{{< plotly json="/plotly_charts/verb_rank_vs_frequency.json" height="500px" >}}  

Zipfy-doo-da, can somebody say "jackpot"? This looks positively Zipfian, promisingly power(law)ful. 

## Final Thoughts

It can be a bit frustrating for language-learners that the most common verbs have the most irregular conjugations; myself included. However, this phenomenon is also a window into the beauty of language. Insofar as a language is a naturally evolving system, it adheres to certain self-organizing behaviors that other emergent systems do. Languages are living, breathing systems that evolve over millenia, and the verbs "to go" and "to be" have been a part of them since the time of my most distant linguistic ancestors. As such, I don't mind giving these verbs the most attention; on the contrary, it feels even honorable and respectful to do so.  

### Denkmit

One of the joys of learning a new language is discovering yourself in the language. Each new language is like a new color palette or musical instrument for self-expression. Everybody wields their language in a unique way; everybody has a unique voice. As I've begun to learn my next language (Hebrew), it's time to retool. There are a lot of language-learning tools out there, but I was seeking something that would cater to my specific voice, and help me to pay closer attention where I needed to. I couldn't find the right tool, so I decided to create it.  

[Denkmit](https://example.com/) creates language learning flashcards from your own WhatsApp chats. It prioritizes learning words and phrases that you and your conversation partners use most often, and that you have the most difficulty remembering. All language-learning requires some small amount of memorization, and Denkmit helps you to do that memorization as effectively as possible. It does so by teaching you your own words, while reminding you of the context in which you learned them. Denkmit helps you to learn your language, your way. Drop your email in the website to get early access to the tool ;)  

[^1]: There exist many complex processes involved in the creation, regularization, and irregularization of verbs. Famous linguist [Steven Pinker](https://en.wikipedia.org/wiki/Steven_Pinker) gives two examples of such processes from as recent as the past 100 years, with the verbs "to sneak" and "to dive". [In the past 100 years, the past tense of the verb "to sneak" recently irregularized from "I sneaked" to "I snuck", while the past tense of "to dive" recently irregularized from "I dived" to "I dove"](https://superpowerspeech.com/2016/09/irregular-verbs-where-do-they-come-from-and-how-can-we-help.html). Verbs can be created, become regular, and become irregular (for example, check out the [complex suppletive etymology of the English verb "to be"](https://en.wikipedia.org/wiki/Suppletion#English)). Verb evolution and irregularity is until this day a contentious linguistics research topic, and the reasoning provided in this post is simplified for the sake of clarity and brevity. For a deeper understanding, consider the book ["Words and Rules", by Pinker](https://en.wikipedia.org/wiki/Words_and_Rules), which explains more about where regular and irregular verbs come from.
[^2]: There's a considerable difference between written language and spoken language, and any linguistics study of natural language data is better off using spoken language. This is because written language is premeditated, edited, and curated, while only spoken language is truly spontaneous and generative. The only problem is that spoken language isn't usually recorded ([unless if you have an Alexa in your home](https://www.washingtonpost.com/technology/2019/05/06/alexa-has-been-eavesdropping-you-this-whole-time/)). So, movie subtitle data is a pretty decent approximation at spoken language, as [Dr. Tang's paper about the movie-subtitle dataset demonstrates](https://www.researchgate.net/publication/238885981_A_61_Million_Word_Corpus_of_Brazilian_Portuguese_Film_Subtitles_as_a_Resource_for_Linguistic_Research).  
As a curious side-note on this point, everything that we know about Latin actually comes from [Classical Latin](https://en.wikipedia.org/wiki/Classical_Latin), or Literary Latin, i.e. the Latin that was written down. It's often said that Latin is the parent of all modern Romance Languages. However, that's not entirely true. All of today's modern romance languages actually descend from the Latin of the commoners, [Vulgar Latin](https://en.wikipedia.org/wiki/Vulgar_Latin); not from Classical Latin.
[^3]: As an example, see the conjugations for [the verb "ir" (to go)](https://www.conjugacao.com.br/verbo-ir/); irregular conjugations are denoted as such with an asterisk "\*").
[^4]: This is an abridged conjugation table. It ignores all the other verb tenses, e.g. future, past participle, subjunctive, etc. The irregularity is measured only on the abridged table, to be as clearly demonstrative as possible.

<!--
    Notes from Brian:
        - connection between irregularity, oldness, and frequency of usage is unclear. make it clearer.
        - change title of section "Hey, Who you Callin' "Irregular"?" to something that doesn't match the title of the article.
        - change language of table to portuguese, or language of plots to english.
        - https://superpowerspeech.com/2016/09/irregular-verbs-where-do-they-come-from-and-how-can-we-help.html
        - https://www.reddit.com/r/linguistics/comments/15k4kz/why_do_irregular_verbs_exist/
-->