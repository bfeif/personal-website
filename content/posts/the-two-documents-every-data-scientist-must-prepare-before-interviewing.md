---
title: "The Two Documents You just Have to Write before Doing Any Data Science Interviews"
date: 2022-11-23T09:47:45+02:00
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

***... or "how to get a 100% conversion rate on getting to the first technical data science interview."***

Data science interviews can be notoriously tough.

As data scientists, we can be expected to serve a sort of swiss-army knife function between business intelligence, software engineering, machine learning, data analysis, and product. On top of that, all companies use data scientists differently, and their respective interviews reflect that. As such, we data scientists must be prepared to answer questions on statistics, machine learning, ab testing, business intelligence, system design, leetcode style programming questions, ML Ops, and more... 

...but in this ocean of possible questions, there are just two questions that I have been asked in **every single data science interview that I have ever done**:
- Tell me about yourself.
- Tell me about a project you've worked on.

These questions are so common that you have no excuse not to crush them every time. And, the best way to ensure that you crush them every time is by preemptively writing responses to them.

--> meme

In this article, I'll teach you to preemptively write these two responses -- a separate document for each, and I'll teach you how to use each of them in your interviews for maximum effect.

# Tell Me About Yourself
We've all heard this question before, usually delivered in any one of a myriad of ways:
- "Before jumping into the interview, why don't you take some time to tell me a little bit more about your background?"
- "I was delighted to see your resume come across my desk! Can you tell me about some of your work experiences leading up to this interview?"
- "So, what brings you here? Tell me more about your career leading up to you interview today with Metooglesoftmazon!"

In response to this question, many of us monotonously droll over the bullet points on our resume, not sure of what else to do. But you don't want to do this. This question is your opportunity to deliver a recency-biased self-narrative that compels the interviewer to believe that your incentives and their company's incentives are truly and uniquely aligned.

## How to Write It

There should be two sections: (1) Resume Read-Through, and (2) Common Questions.

### The "Resume Read-Through" Section
Write a bullet for each step on your resume, describing:
- How you got into the role or experience.
- What you did during the position, describing any key technologies you used, domains or projects you worked on, and any measurable impact you had.
- Why you left.

Your answer to "Tell Me About Yourself" shouldn't be more than two minutes or so, and you want to use that time wisely. So, for stuff early in your career, you should write less; and as you get closer to your current position, you should write more (hence "receny-biased"). After all, nobody wants to hear about the model you deployed 4 years ago, even if it was really really cool and used chron jobs to train.

--> insert TMNT meme of Airflow, Luigi, and Argo walking with Cron jobs.

For your most recent bullet point (i.e. your current experience), the question "Why you left?" is actually "Why do you want to leave your current role and join this company?". So, this is where you want to tailor it to the company you're interviewing for: why do you want to join them? How do they fit into your story, and how do you fit into theirs? Tech companies' favorite employees are the ones who are passionate (i.e. are open to being overworked) and who will stay for a long time. This is your chance to make the companies that you interview at think that you satisfy both. I usually write a different "Why do you want to leave your current role and join this company?" for every company I interview at, to make them all feel special.

### The "Common Questions" Section

This should be left blank, to begin. During your interviews, interviewers will ask you questions about the things that you say (naturally). You can jot them down here, so that you're prepared for any questions that repeat themselves in a later interview.

## How to Use it

I usually have my desktop split half and half: one side has the interview video call open, and the other side has this document open. When the interviewer asks me their own special version of "Tell Me About Yourself", I usually say something like "Sure, I'd love to! I'll tell you about my background chronologically, which is bottom to top on my resume". That way, the interviewer can use my resume as a visual tool for orienting themselves in my narrative.

Furthermore, depending on the company, your interviewer, and the vibe of the interview, you can shorten or lengthen bullet points, and even altogether omit bullet points from early in your career.

As mentioned above, if the interviewer asks you any questions, you add them to the "Questions" section of the document. After the interview, you can go back and write answers to those questions, so that you're more ready for whenever you get asked that question again.

# Tell Me About A Project You've Worked On
Whether you're working on an ab-test design, engineering new features, or experimenting with new models for your deployed ML system, data science is typically project-based work. As such, companies want to know that you can take ownership of projects, be innovative, and deliver measurable impact. This is your chance to show them that you are (or that you can be). So, before starting writing the "Tell Me About a Project" document, take some time to reflect: which was your biggest project? Which was your most impactful? On which projects were you a primary owner? It can be a school project, a personal project, a work project (most preferably), or something else. You want something that is recent and that you've taken a lot of ownership of. Pick one, and then get started:

## How to Write It

There should be three sections: (1) Problem, (2) Solution, and (3) Common Questions:

### The "Problem" Section
This should be very high-level, and should guide the interviewer from not knowing anything about the company that you work for, all the way to understanding a business problem that required a data science solution. As such, this section should explain the company you work for, the team in the company that you work for, a problem that you and your team encountered or were given by an external team (e.g. Product), and then the business codification of that problem. You should mention the problem's greatest non-trivialities, and any ways in which the problem is novel or noteworthy on an industry-wide level. It should be as quantitative as possible, and can even include impact estimations with hard numbers. 

### The "Solution" Section
Once you had the distilled problem, what was your solution? Did you validate your solution on preexisting data somehow? Explain your solution at a technical level, from preprocessing to cost function selection to training to performance measurement. Once your solution was executed and deployed, how did you monitor it? How did you measure impact, and what was the impact? How did you iterate after any AB-Tests or any unsuccessful deployments? Again, this should be as quantitative as possible.

### The "Common Questions" Section
As the old adage instructs, "if it's on your resume, be prepared to answer questions about it". Likewise, "if you used a particular technology or tool in your work, be prepared for questions about it". You should know your project better than anybody else, at a high level and at a low level. Here are some examples of questions that I have in my own "Common Questions" section, some of which I preemptively thought of and answered, and some of which I've been directly asked in interviews:
- How did you execute the impact estimation?
- What did you learn in the discovery phase of the project?
- How did you deploy your model? Why did you deploy it this way?
- Did you AB test? What were the results? What methods did you use for measuring statistical significance? Why did you use t-test instead of K-S test? How did you iterate after AB testing?
- How did you choose the model that you chose?
- You used LightGBM for classification--how does LightGBM work? How is it different from XGBoost? Which one worked better and why do you think it was better?

Writing this "Common Questions" section ends up doubling as a great studying and interview-preparation tool for me, as it forces me to dig into the internals of all the different tools I used, e.g. feature engineering methods, dimensionality reduction methods, cost functions, model training methods, and so much more.

## How to Use it

Like with the "Tell Me About Yourself" document, I have this one open alongside the interview video call screen. When they invariably say "tell me about a project", I deliver my "problem" and "solution" section. Then, when they start with their questions, I reference my "Common Questions":
- If it's a question I've got a prepared response for, I glance at it to jog my memory (I don't want to read directly from the page, as it ends up sounding unnatural; besides, it's usually not necessary to do so anyway);
- If they ask a question that I've never gotten before, then I do my best, and make sure to include that question (and my answer) to "Common Questions" section before my next interview.

# Conclusion

Over my 4+ years working as a data scientist, I have given this advice to a few of my friends and colleagues on their respective job-hunts (both those trying to get into the field for the first time and those looking for a new role). They've all told me that this is the most actionable and effective interview advice they've ever received, and so I'm sharing it with you as well in the hopes that it's useful for you on your journey.

Having answers to these questions prepared in clean documents simply gives you two less things to think about, and allows you to focus in your interviews on the hard questions and on building a relationship with your interviewer. A more focused interview with a better interviewer-candidate rapport will help you to land your dream data science job faster and more effectively.

---

_Want me to review your resume, "Tell Me About Yourself", and "Tell Me About a Project" documents? Feel free to submit them for review here:_  
_Are you struggling at work to even get the opportunity to work on an interview-worthy project? I've been there, and I think I can help you get past it. Feel free to schedule some time with me here if you'd like to chat about it:_