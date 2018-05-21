---
layout: post
title: Lessons from my MLB Project
description: Recording some thoughts after the fact
image: assets/images/
---

After completing my second project in the Metis Spring semester, I have a final product that I am proud of. However, looking back, I see many steps and choices that could have been done better. This is natural, of course; this was, after all, my first real independent data science project!

With this in mind, I wanted to jot down a few ideas that I could have done better and lessons learned. Writing these down over the last few days has been illustrative.

######  1)  Define Questions Beforehand

When I first received the last assignment, my mind immediately started to run wild with possible ideas and final products. I jumped into web scraping (first ESPN and Baseball Reference, then ESPN and Fangraphs) with a loose idea of what my final product would be. I didn't have a hypothesis or a goal for what my raw dataset (not to mention my clean dataset) should look like. I had no metric of success besides being done eventually.

I think ultimately lost a lot of time this way. While it's tempting to just "jump" in and refine as you go, spending an hour or two beforehand reading and sketching out my idea would have given me a rough blueprint to operate by. I think this sketch would have saved me hours of redoing data scraping (which I had to do, twice) as well as forced me to define my end-goal before starting. I don't think this would have been a panacea for the issues I faced further downstream, but it would have given me a reference to look at when these inevitable issues arose.

######  2) Organize as you go and document!

I have to give myself a little credit here, I actually did reorganize my code at the midway point of this project. For a two week project, I took a full day over the weekend (halfway through) to add comments to my code and somewhat clean up my documentation. However, even just a few days after starting I had already lost some of the memory of what my specific lines of code were doing.

The lesson here was to make sure that each notebook, each line is written with some sort of clearly defined intention. I don't have to know exactly what I want to do (in fact, exploration is going to be a big part of any data project), but I should be able to clearly articulate (even just in a few commented out words of code) what I'm trying to do for any single step as I do it. This will keep me focused on task and facilitate documentation as I go. It will also facilitate easy understanding for anybody else reading my code afterwards!

For my next project, I'm going to make it a primary goal to document my steps as I go. One specific thing that comes to mind is to name files, data pickles and functions with *very* clear names so that I don't have to go read through 15 lines to figure out what I'd been doing in a given spot.

######  3)  Take Breaks and Know Thyself

I've known for years that I work best in the morning, and that I can't "crank" work for more than an hour or so without needing some fresh air or a stretch. Especially with this type of code-heavy work, I found that I was often tempted to keep going at a really specific obstacle, and I definitely wasted a few hours over the 2 weeks trying the same things over and over again. Ultimately, I rarely found a challenge I couldn't get beyond after taking a short break and coming back to that issue after a snack/meal or sleep.

This ties in nicely with my first two points - knowing that I'll need to take breaks as I go, it is imperative that I organize my work in clearly labeled modules so that when I do have to put stuff down I can easily pick it back up.

I also now know about myself that I **need** snacks when coding - I've already topped my last few months consumption for Swedish Fish in these 3 weeks, so I'm stocking up for this next project. :fish:

######  4)  Don't Beat Yourself Up
No project will ever be perfect, especially when faced with the following two factors:

* 1 - You're learning a large set of new skills
* 2 - You're facing a tight deadline

Both of these factors were especially pertinent in this last project. I'd never scraped an HTML website before, I had some Pandas experience but didn't know close to everything we'd need and had never presented an independent data science project in front of an audience. Naturally, I have lots of room for improvement across the board. And that is OK!

This is an iterative process - one that never ends, because there will always be more tools to master, ways to get better and public speaking skills to build. But I am really excited that I'm excited about continuing to work at it (excuse the double excitement here).
