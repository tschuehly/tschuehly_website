+++
title = "Dave Farley: Continuous Delivery Simply Explained"
date = "2022-06-11T15:52:36+02:00"
author = "Thomas Schühly"
authorTwitter = "tschuehly" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
+++

This is the transcript from the YouTube Video [CONTINUOUS DELIVERY SIMPLY EXPLAINED](https://www.youtube.com/watch?v=qiDIif7JVMo) from Dave Farley's Channel Continuous Delivery. I think it gives an excellent explanation of Continuous Delivery.

As a non-native it helped tremendously being able to read the transcript to better understand the concepts. I added punctuation and removed spelling errors to improve the readability, I also added Chapters.

# Intro
Continuous Delivery is probably not quite what you think. It's the most effective approach to software development that we know.

It doesn't seem to matter what kind of software it is that you're producing. When you apply Continuous Delivery we get better results, we create software that is higher quality, and we create that software more efficiently, and we have more fun while we're doing it.

The companies that employ us to do these things make more money. This is what the data says. For the first time in the history of software development we have data that shows the real impact of a software development methodology, and that impact is really quite profound.

This is the approach to software development practiced by most of the best companies at software development in the world. I think of it as the application of engineering thinking of science to solving problems in software. But what does all this mean and what does it take to achieve it?

# Software Fundamentals
Software development is hard, it's a difficult thing. We build complicated systems, complex systems that can do extraordinary things. We can do things that were impossible before. Software is the thing that drives the revolution that is the information age, for good or ill.

But software is different to most things in human experience. It's very different to things that have gone before. producing software is a highly creative act, similar in many respects to writing or creating music or those sorts of things. But it's also a deeply technical act.
Software is extremely fragile when we're writing code, a small error can have a big impact. It's rather as though if we were writing a book, and we spelt a word wrong or got a comma in the wrong place the book stopped working.

Software has an economic impact such that the speed and quality with which we can deliver the software matters. But how can we produce it quickly and efficiently when it's so easy to make a mistake? Software is extremely unusual in that its cost of creation is very high, but its cost of reproduction is almost free.

Once we have those bits and bytes that represent a software system, we can reproduce them and distribute them for a negligible cost, that again is very unusual in human history. I think that all of these things boil down to two foundational principles for all software development.

Software is primarily and fundamentally always an exercise in learning and discovery, and to achieve that and to allow us to scale that process up we need to focus on managing the complexity of the things that we build. Those seem like the building blocks for effective software development to me, because both of these things require a certain degree of discipline to achieve them.
We don't get to be great at learning and discovery or at managing complexity without learning the tricks.

# You don't know if it's a bad idea

Continuous Delivery provides a structured approach to development that helps us with both of these fundamental principles of software development.
To begin with we need to become less subjective where we can. Most software development starts with a group of people somewhere coming up with an idea for something useful and then they or another group of people come up with some ideas about how they might fulfill the idea and create some software to deliver it.
The difficulty here is that either idea might be bad or wrong. Maybe no one's interested in our business idea, or maybe a competitor has a better or similar idea that they beat us to market with or implemented better than ours.
What if the idea for the design of our software doesn't work or is unpleasant to use? What if we missed something?

In the stone age of software development, we assumed that the only way to solve these problems was to think harder. We'd plan really, really hard until our plans were sure to work.
But they didn't. The problem is that however good our plans there are things that we don't know and can't know.
Instead of beginning by assuming that we are correct in our plans and knowing the answers, maybe we should start by assuming that we might be wrong. Our product idea or our solution ideas, either one might be wrong. If that were the case how would we proceed then? How could we find out efficiently if we made a mistake somewhere? How could we recover quickly and cheaply if there had been a mistake?

# Develop Software like a scientists does science stuff

We should begin by focusing on learning. We could proceed in a series of small experiments, we could be willing to risk that one of those experiments might fail. But if it did then we'd learn from that.
We must measure, we must control the variables in order to be able to understand the results of our experiments. We want to be able to focus in on what it is that we want to learn and try and figure out how to accurately and precisely learn that lesson. Whatever it might be.

To control the variables, we need to automate in software terms nearly everything and version control nearly everything. Does any of this sound familiar at all? This is really fundamentally how science works. Humanity's best problem-solving technique. How could we apply that kind of thinking to software and software development?

# What is Continuous Delivery?

Continuous Delivery is a holistic approach to software development, and it starts with that idea we have for something that might be useful. And then we want to try and get that idea in the form of working software into the hands of users. So that we can verify that it is in fact useful. And that the implementation the solution that we came up with was also useful and effective.
And whether it was nice to use and all of those other things. We gather feedback from that learning, and now we're in a position to understand which ideas are good ideas and which ideas are poor ideas, and optimize for the good ones.

# Cycle Time

So we will measure how long it takes us to go from idea to working software in the hands of our users. We call that measurement „cycle time“ in Continuous Delivery terminology. Then we will work to reduce that cycle time until we can work in a way, so our software is always releasable, and that cycle time is incredibly short. We aim to get our cycle time from idea to working software that is releasable into the hands of users within a day, ideally in under an hour.
Working to reduce it has a lot of positive benefits. In fact if you take an experimental approach to reducing cycle time in your development process, it gives you a tool that helps you to establish the principles of Agile, Lean, DevOps and Continuous Delivery.
Workings to reduce cycle time drives out waste from our process. We can't have short efficient effective cycle times if the teams are too big. We can't have such cycle times if the teams need to coordinate their work. So the teams need to be autonomous. We have to have a great statement on automation: automated testing, deployment management, configuration management.

All of these things need to come together to produce high quality output that we can verify is useful to our users and quickly learn when it is not.

# Feedback is crucial
I tend to use this picture as a kind of icon for continuous delivery:
![/posts/img_3.png](/posts/img_3.png)

It shows three feedback loops. At the outside we have that crucial feedback loop from idea to valuable working software in the hands of our users.
At the center we have the fast seconds only times feedback loop of test-driven development. And in between we use ideas like executable specifications to have a broader view of the development of our system.
Continuous delivery is an approach to software development absolutely fundamentally grounded in the ideas of iteration and feedback. And so there are many more feedback loops than this.

# Where it comes from
But these three principal feedbacks kind of capture the essence of what it is that we're trying to achieve. Fundamentally Continuous Delivery is aiming to improve our approach to software development overall. 
The phrase Continuous Delivery comes from the agile manifesto the first principle of which stated that our highest priority is to satisfy the customer through the early and continuous delivery of valuable software. This is the first principle of the agile manifesto and the foundational idea behind the underpins of Continuous Delivery.
We're going to optimize for that to keep our software releasable so that we can keep delivering it into the hands of our users quickly efficiently and cheaply.

It's kind of a logical extension of the ideas of Continuous Integration which was a technical practice focused on development teams that was practiced for many, many years but was popularized at the start of this century, when tools started to be released that supported the approach.
Continuous integration gave developers feedback: yes my code compiles, yes it runs a few tests.
So it's good, it's better than not knowing that stuff. 

Continuous Delivery takes that ideas and just extends it further into the process. Continuous Delivery is a holistic approach to developing software, it takes all aspects of software development to collaborate and to coordinate to achieve it.
To be able to continuously deliver ideas into the hands of users requires us to be good at the technical practices, but also have the right kinds of organizational support and structures in place and the right kind of development culture and organizational culture too.

# Proving your code is bad
A good mental model for Continuous Delivery is that every time that we commit a change we give birth to a release candidate and the job of the rest of our process and systems is to prove that that release candidate is not fit to make it into production.
That might sound the wrong way around, but this is one of those ideas that we learn from science. 

We can never prove that our software is good however many tests we have, I might have missed something, so I can't be definitive, I can't be certain that my software is good, however many tests I have.
However, if one test fails  I know that my software is not good enough, and therefore I can discard that release candidate. That immediately says something about the quality and the seriousness with which we're going to treat our automated tests. We're going to use those tests to falsify our system and if a test fails, we're going to discard that release candidate and that change.

We're going to go back to the beginning and commit a new change that either fixes the problem or reverts it. Done means "in the hands of users delivering value" and we're going to focus on trying to achieve that. 

# Automate the Boring Stuff ~~with Python~~

Ultimately what we're trying to do is that we're trying to create a repeatable reliable process for releasing software. That means that we're going to use automation for nearly everything. 
If we're going to automate everything, we're going to keep everything in version control. 
We're going to speed up the process so if something is difficult and painful it kind of shines the light on us and forces us to address the difficulty and the pain. 

Usually through automation, but maybe through other sorts of process optimizations, and we're going to aim to build quality into the process from the beginning and to improve continuously in our approach.

# Is it done?
One of the problems with software development is understanding progress. How well are we doing in our aim to achieve some kind of outcome. And often historically what we've tended to do is that we've tended to use some kind of interim measure. Ideas like partial definitions of done or counting lines of code, or function points, or feature points and all of this kind of stuff. And really none of that works.

In Continuous Delivery we avoid all of these problems. Instead, we're going to work in small steps, and after each of our changes, each one of those small steps, our software is going to be releasable. So we're going to work to a releasable outcome. 
We're going to work to a level of completeness and that's what's going to tell us whether we're finished or not. Can we release it into production and is it being useful to our users? 

So we use tools like deployment pipelines in Continuous Delivery to centralize and host this automation. A deployment pipeline goes from commit to releasable outcome, and that's its scope. And the job of the deployment pipeline therefore is to falsify our changes. We're going to take a commit we're going to try and reject it through running a whole bunch of tests against it.
And if we can't reject it at the end of its transit of the pipeline it's releasable with no more work to do.

# The key ideas
The technical practices of Continuous Delivery are really centered around three key ideas. 
- We're going to aim to produce high quality software repeatably, and reliably 
- We're going to apply scientific principles and work experimentally to achieve that 
- We're going to create a machine, a deployment pipeline to control the variables automate nearly everything and include all of the steps that are going to be able to assert that releasability

# Tackling complexity 
Working in small steps each step being evaluated from commit to release of releasability applies a pressure for more modular design. Continuous delivery encourages to focus on the testability and deployability of our software. Both of these encourage us to create more modular cohesive systems with better separations of concerns and improving these properties of software also tends to compartmentalize it.
Compartmentalizing problems is the best way to manage complexity. 

There are seven essential techniques that you can use to establish Continuous Delivery.
We can aim to reduce the cycle time
- automate nearly everything 
- control the variables 
- work in small steps 
- make evidence-based decisions 
- work in small empowered teams 
- apply lean and agile principles to the ways in which we work 

# It's engineering
If you'd like to learn more about these techniques then you might be interested in Dave Farley's Continuous Delivery course [Better Software Faster](https://courses.cd.training/) if you'd like to know more.

So Continuous Delivery is a mindset more than a technical approach it is about applying scientific style reasoning to solving practical problems. Outside of software development we usually call that engineering.
Thank you very much for watching.
