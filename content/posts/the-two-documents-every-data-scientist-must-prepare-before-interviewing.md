---
title: "The 2 Documents Every Data Scientist Must Write Before Taking Interviews"
date: 2024-05-17T09:47:45+02:00
draft: false
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

***Spoiler alert: neither of them is a resume.***

Data science interviews can be tough.

As data scientists, we can be expected to serve a sort of swiss-army knife function between business intelligence, software engineering, machine learning, data analysis, and product. On top of that, all companies use data scientists differently, and their respective interviews reflect that by spanning a wide array of questions and processes. We data scientists must be prepared to answer questions on statistics, machine learning, ab testing, business intelligence, system design, leetcode style programming questions, ML Ops, and more.

<figure class="image" align="center">
    <img src="/images/data_scientist_many_hats.png" alt="drawing" width=280/>
    <figcaption style="font-style: italic">
        Did someone say something about many hats? | Image from <a href="https://publicdomainvectors.org/en/free-clipart/Six-Hats/69983.html">publicdomainvectors.org</a>, modified by Author.
    </figcaption>
</figure>

However, in this ocean of possible questions, there are just two questions that I have been asked in **every single data science interview that I have ever done**:
- Tell me about yourself.
- Tell me about a project you've worked on.

These questions are so common that you have no excuse not to crush them every time. And, the best way to ensure that you crush them every time is by preemptively writing responses to them.

Over the past 5+ years working as a data scientist, I have given this advice to a few of my friends and colleagues on their respective job search journeys, and they've all told me that this is the most actionable and effective interview advice they've ever received. That's why I'm sharing it with you as well, in the hopes that it's useful for you on your journey.

In this article, I'll teach you to write these two responses -- a separate document for each -- and I'll teach you how to use each of them in your interviews for maximum effect.

## "Tell Me About Yourself"
We've all heard this question before, usually delivered in any one of a myriad of ways:
- "Before jumping into the interview, why don't you take some time to tell me a little bit more about your background?"
- "I was delighted to see your resume come across my desk! Can you tell me about some of your work experiences leading up to this interview?"
- "So, what brings you here? Please take a few minutes to tell me a bit about your career leading up to your interview today with Metooglesoftmazon."

In response to this question, many of us monotonously, and often forgetfully, droll over the bullet points on our resume, not sure of what else to do. But you don't want to do this. This question is your opportunity to deliver a recency-biased narrative telling of your resume that compels the interviewer to believe that your incentives and their company's incentives are truly and uniquely aligned. It's also often the first question you get asked, so it gives you a chance to stand tall and proud, starting your interview off on confident, firm footing.

### How to Write the "Tell Me About Yourself" Document

Write a bullet for each role/experience on your resume, describing:
- How you got into the role/experience.
- What you did during the role/experience, describing any key technologies you used, domains or projects you worked on, and any measurable impacts that you had.
- Why you transitioned to the next role/experience.

Your answer to "Tell Me About Yourself" shouldn't be more than two minutes or so, and you want to use that time wisely. So, for stuff early in your career, you should write less; and as you get closer to your current position, you should write more (hence "receny-biased self-narrative"). People would rather hear about projects more recent than the model you deployed 10 years ago, even if it was really really cool and used Cron jobs to train.

<figure class="image" align="center">
    <img src="/images/tmnt_workflow_orchestration.png" alt="drawing" width=280/>
    <figcaption style="font-style: italic">
        Cron passing the torch to Airflow, Argo, Prefect, and (sorry you got left out, Luigi)! | Image by Author
    </figcaption>
</figure>


For your most recent bullet point (i.e. your current experience), the third question "Why you transitioned to the next role/experience" actually becomes "Why do you want to transition from your current role and join this company?". So, this is where you want to tailor it to the company you're interviewing for: why do you want to join them? How do they fit into your story, and how do you fit into theirs?

I usually write a different "Why do you want to leave your current role and join this company?" for every company I interview at, and sometimes even make small adjustments to the entire document for the interview if something in my background is particularly relevant to the company (e.g. experience with some technology or domain).

### How to Use it

I usually have my desktop split half and half: one side has the interview video call open, and the other side has this document open. When the interviewer asks me their version of "Tell Me About Yourself", I usually say something like "Sure, I'd love to! I'll tell you about my background chronologically, which is bottom to top on my resume". It might not be necessary to be so structured and formal about it, but I like guiding the interviewer to use my resume as a visual tool for orienting themselves in my narrative.

<figure class="image" align="center">
    <img src="/images/how_to_use_tell_me_about_yourself_doc.png" alt="drawing" width=600/>
    <figcaption style="font-style: italic">
        An interview with... myself! It was the hardest interview I've ever experienced 🥵 | Image by Author
    </figcaption>
</figure>

Furthermore, depending on the company, your interviewer, and the vibe of the interview, you can shorten or lengthen bullet points, and even altogether omit bullet points from early in your career.

## Tell Me About A Project You've Worked On
Whether you're working on an ab-test design, engineering new features, or experimenting with new models for your deployed ML system, data science is typically project-based work. As such, companies want to know that you can take ownership of projects, be innovative, and deliver measurable impact. This is your chance to let them know that you've done this before, and you can do it for them.

Before starting writing the "Tell Me About a Project" document, take some time to reflect: what project are you most proud of? Which of your projects had the most impact on a business? On which projects were you a primary owner? It can be a school project, a personal project, a work project (most preferably), or something else. You want something that is recent, is reasonably big, and that you've taken a lot of ownership of. Pick one, and then get started:

### How to Write the "Tell Me About a Project" Document

There should be three sections: (1) Problem, (2) Solution, and (3) Common Questions:

#### The "Problem" Section
This should be very high-level, and should guide the interviewer from not knowing anything about the company that you work for, all the way to understanding a business problem that required a data science solution. As such, this section should explain the company you work for, the team in the company that you work for, a problem that you and your team encountered or were given by an external team (e.g. Product), and then the business codification of that problem. You should mention the problem's greatest non-trivialities, and any ways in which the problem is novel or noteworthy on an industry-wide level. It should be as quantitative as possible, and can even include impact estimations with hard numbers. 

#### The "Solution" Section
Once you had the distilled problem, what was your solution? Did you validate your solution on preexisting data somehow? Explain your solution at a technical level, from preprocessing to cost function selection to training to performance measurement. Once your solution was executed and deployed, how did you monitor it? How did you measure impact, and what was the impact? How did you iterate after any AB-Tests or any unsuccessful deployments? Again, this should be as quantitative as possible.

#### The "Common Questions" Section
As the old adage instructs, "if it's on your resume, be prepared to answer questions about it". Likewise, "if you used a particular technology or tool in your work, be prepared for questions about it". You should know your project better than anybody else, at a high level and at a low level. Here are some examples of questions that I have in my own "Common Questions" section, some of which I preemptively thought of and answered, and some of which I've been directly asked in interviews:
- How did you execute the impact estimation? Why did you or your team prioritize this project over other options?
- What did you learn in the discovery phase of the project?
- How did you deploy your model? Why did you deploy it this way?
- Did you AB test? What were the results? What methods did you use for measuring statistical significance? Why did you use t-test? How did the results of AB testing guide the project's next steps?
- How did you choose the model?
- You used LightGBM for classification--how does LightGBM work? How is it different from XGBoost? Which one worked better and why do you think it was better?

<figure class="image" align="center">
    <img src="/images/xgboost_vs_lgbm.jpeg" alt="drawing" width=280/>
    <figcaption style="font-style: italic">
        Only the strong will survive. | Image by Author.
    </figcaption>
</figure>

Writing this "Common Questions" section ends up doubling as a great studying and interview-preparation tool for me, as it forces me to dig into the internals of all the different tools I used, e.g. feature engineering methods, dimensionality reduction methods, cost functions, model training methods, engineering tools, and so much more.

### How to Use it

Like with the "Tell Me About Yourself" document, I have this one open alongside the interview video call screen. When they invariably say "tell me about a project", I deliver my "problem", "solution", and "impact" sections; then, when they invariably begin asking me questions, I reference my "Common Questions" section:
- If it's a question I've got a prepared response for, I glance at it to jog my memory (I don't want to read directly from the page, as it ends up sounding unnatural; besides, it's usually not necessary to do so anyway);
- If they ask a question that I've never gotten before, then I do my best; after the interview, I make sure to include that question to the "Common Questions" section before my next interview, along with taking some time to write the answer that I gave during the interview and even try to make it better.

## Conclusion

Having answers to these questions prepared in clean documents simply gives you two less things to think about, and allows you to focus in your interviews on the hard questions and on building a relationship with your interviewer, the latter of which is often the most important thing. Moreover, if you're one of the people (like myself) for whom the process of writing commits things to memory, then you're in luck -- I often don't need to look at these documents during my interviews, as I've committed them mostly to memory; rather, they serve as helpful guides.

Having clear, confident answers to common questions helps you to more clearly present yourself and to have a better interviewer-candidate rapport; this in turn will help you to land your dream data science job faster and more effectively.

---

{{< contact message="Liked what you read? Feel free to reach out on " >}}