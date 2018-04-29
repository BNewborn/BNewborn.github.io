---
layout: post
title: MLB Free Agent Value Predictions
description: Exploring and predicting MLB free agent valuations
image: assets/images/mlb_free_agent_images/baseball_thumbnail.jpg
---

## Intro
For our second project at Metis, we were tasked with creating a linear regression model to predict any continuous variable. I took this opportunity to explore data related to MLB free agent values. I would be predicting per year salaries of players, should they become a free agent. For example, if a player received a contract paying them $100 Million over 10 years, my model would see that as a $10M/Year.

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/header_photo.jpg" alt="Baseball"/>
</p>


First, I had to figure out where to get this data. As you may be aware, there are dozens of sites on the internet that record, aggregate and release detailed baseball statistics. After all, this was the first sport to go through the "analytics revolution", starting almost 40 years ago in the early 1980s. I ultimately committed to using [Fangraphs](https://www.fangraphs.com/), as they do an excellent job of providing detailed stats as well as transparent explanations of any necessary assumptions in the data. I used [ESPN](http://www.espn.com/mlb/freeagents/_/year/2016/type/dollars) for free agent data, as their interface was extremely easy to read and ultimately scrape from the internet.

I then had to decide what type of data I wanted to use. It was obvious that I would need free agent data like contract length, amount of money, player name and year from that side of things, but there was a lot of wiggle room for hitting stats to grab. I wanted to have a hybrid of both traditional (Home Runs, Doubles, Batting Average, Strikeouts) and advanced statistics (weighted runs created, Wins Above Replacement(WAR), and On-Base Percentage + Slugging aka OPS). I wanted this large basket so that I could capture the easy to see stats as well as those that analytics typically front as especially reflective of a player's effectiveness on the field.

As a personal aside, I tend to enjoy the hybrid of these stats in real life - I still appreciate a long home run or a "clutch" hit, but ultimately see immense value in advanced metrics telling us things we cannot see with just a "feel" for the game. I thank Michael Lewis for writing [Moneyball](https://en.wikipedia.org/wiki/Moneyball) in the early 2000's. While I was already a huge sports fan by middle school, this helped cement my love for the numbers behind the great plays.

## Grabbing Baseball Data from the Net

After these decisions, I had to gather this data into a database for measurement and modeling. In class, we learned about a few tools to grab data off the internet. I started my attempt by using a Python library called [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/), which is an excellent tool for parsing through HTML code. However, I had to switch gears after a few false starts. I ended up using [Selenium](http://selenium-python.readthedocs.io/), a  library that allows one to mimic a browser and scrape data as if they were themselves clicking through the page. Selenium afforded me the flexibility and speed to scrape the above pages over the course of a few hours while I worked on other tasks. After scraping, I now had 4 large datasets. 3 were from Fangraphs; I individually scraped 3 of their hitting pages (Standard, Advanced and Value, respectively) for all hitting seasons between 2010 and 2017 (I'll mention later why I ultimately decided to break these down into 3 iterations versus 1 large scrape). The fourth dataset was ESPN's data recording all free agent contracts signed in that same time frame. Ultimately, from the 3500 or so hitters that had qualifying seasons between 2010 and 2017, there were 530 free agents that signed contracts then. These 530 player-seasons would be my modeling dataset, using about 60 statistics as independent, exploratory variables.

## Exploring My Data

With all of this data, I wanted to get a feeling for how each variable correlated with contract money, as well as some larger, macro-level views of hitting in the last 8 seasons.

This past free agent season was particularly noteworthy in the sports world because many of the best players were left without contracts well into 2018 (typically, the top players sign before New Years).

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/MassLive_Headline.png" alt="Headline 1"/>
</p>
<p align="center">
  <img src="/assets/images/mlb_free_agent_images/BizInsider_Headline.png" alt="Headline 2"/>
</p>

I wanted to see if the data supported this notion that 2017 was actually a bad year for free agents. When I summed up all money spent by year, I saw that the data did in fact back this up.

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/Free_Agents_Total_By_Year.png" alt="Free Agents Total By Year"/>
</p>


There's also been a mainstream belief that hitting is struggling as of late, as [pitchers get stronger](http://bleacherreport.com/articles/1559656-the-truth-behind-the-rise-of-the-strikeout-in-major-league-baseball) and [defensive shifts penalize hitters in the long run](https://fivethirtyeight.com/features/yes-the-infield-shift-works-probably/). At least, batting average has seemed to take a dive in the last few decades. Remember that batting average simply measures hits over at-bats. It leaves little room for nuance (are certain hits better than others, like a HR versus a single?) and famously doesn't include walks (which is pretty much the TLDR Sparknotes for Lewis' Moneyball book - sorry for spoiling). But this narrative has a more interesting underside - that hitters are actually swinging for the fences more. And they're connecting - home runs are [even more prevalent](https://www.washingtonpost.com/news/fancy-stats/wp/2017/06/01/mlb-home-run-spike-shows-statcast-science-is-more-potent-than-steroids/?utm_term=.c3277dca83fc) than they were during the famed Steroid Era. There are a few theories for why this has happened - like my favorite baseball conspiracy theory, that the MLB has introduced ["juiced" balls to facilitate more exciting home runs](https://www.theringer.com/2017/5/9/16040456/2017-mlb-home-run-rate-is-the-ball-juiced-report-results-6e1dd0233203). But I tend to lean more towards the analytics here. There's a lot of evidence that hitting home runs (and getting more outs) is better than hitting smaller hits more often. The idea has basis in a simple risk/reward concept; the reward of a home-run (one swing, at least one run), is worth more than the risk of an out. While finding out if this was true was beyond the scope of my analysis, I at least wanted to see if power hitting stats have been increasing in our last 8 seasons.

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/HRs_Total_By_Year.png" alt="HR Total By Year"/>
</p>
<p align="center">
  <img src="/assets/images/mlb_free_agent_images/SLG_Total_By_Year.png" alt="SLG Total By Year"/>
</p>


This was really interesting - power stats have in fact gone up in the last few years. It would then suggest that teams in free agency should be more strongly valuing power statistics (like Home Runs and Slugging %) in free agency. Would a linear regression model reveal that a hitters best path to big bucks is through the long ball?

## Getting to the Model

When starting a linear regression model, there are numerous questions to answer.

* What are we trying to predict/explain?
* Is this variable normally distributed?
* Can a linear model craft predictions that feature normally distributed error terms from real values?

This last question is crucial - if a model is systematically underestimating or overestimating our dependent variable (free agent salary per year, in our example), we cannot feel confident that it will make accurate predictions. This bias suggests that there is some missing variable or that linear regression cannot accurately explain (at least parts of) the variance in our data.

Let's look at free agent contracts over the last 8 years. Are most players paid in the middle amounts? A normally distributed graph would look something like this

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/Normal_Distribution.png" alt="Standard Normal Distribution"/>
</p>

However, when you graph free agent yearly salaries, you see something dramatically different.

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/hist_contractvalue_players.png" alt="Free Agent Histogram"/>
</p>

This doesn't mean a linear model is impossible, it just means we may have more trouble than we'd like in finding one that works.

Undeterred, I first tried creating models that used the raw $/Year measurement. These models struggled to provide good explanations of the variation in free agent salary, however. I tried many combinations of regularization (penalizing data columns that are highly correlated and removing data that's superfluous), polynomial transformations (squaring or cubing our stats to see if exponential relationships helped) and selecting the "K Best" number of features (only taking a subselect of the data points available). Ultimately none of these combos could provide us a model that neither overestimated or underestimated player salary.

Here's a result of one of these models - if our model was consistently between overestimating and underestimating we'd see data (blue) perfectly along the red line here. However, you can see some serious fluctuations around this line. This chart is one example of behavior I saw in linear models over and over again.

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/QQ_Regular" alt="QQ For Untransformed Data"/>
</p>

I hypothesize that this is due to the nature of free agency salaries - there is a heavy skew to the right with a few really highly paid players while most labor for close to the league minimum.  

One way to get around this is to "log" our independent variable. This means that we run a log base10 transformation on our salaries per year. This has the effect of "squeezing" our data (and often times, pushing it into a normally distributed set). But this can sacrifice model interpretability. I decided to see if this would help develop a better model for free agency values.

In fact, it did! My model, now using log(dollars/year) had substantially better results in avoiding over/undersestimating salaries.

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/QQ_Log" alt="QQ For Transformed Data"/>
</p>

While this model still left plenty to be desired, it was at least now consistent with the pre-requisites of linear regression.

## The Model
I then ran a few combinations of models on this new dependent variable and came up with a "pipeline" (a combination of model features) of the following:

* Standard Scaler - this normalized our data so that all values were between 0 and 1. Otherwise, you'd have large numbers, like home runs (which ranges from single digits to 50/60) strongly outweigh percentage statistics (which are marked as decimals like .380 or .740).
* Select KBest Features - I ran Cross-Validation across every possible number of features we could use in our data set, and allowed the computer to tell me the optimal number of features. Ultimately this resulted in us using 26 of the most predictive features.
* Linear Regression - armed with our normalized 26 features, I ran a pure linear regression and came up with a model

The strongest 5 coefficients are shown here in log coefficient - this means that for a 1 point increase in these coefficients, there is approximately a 10 to that number increase in salary. For example, if a log coefficient is 2 for hitting statistic X, a 1 point rise in X would result in a 10^2 = **100** dollar increase in salary.

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/Coefficients_LOG.png" alt="Coefficients - LOG"/>
</p>

I show you these values in log because At-Bats so vastly outweighed any other variable in predictive value. With logs, you can still see the other coefficients slightly. When you see their raw values, the other columns all but disappear

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/Coefficients_TRUE.png" alt="Coefficients - Regular"/>
</p>

## Interpreting the Model

Ultimately, the goal of building this model was to see if there are any hitting statistics that can, more than the others, at least, predict free agent salary. However, as you can see above, none of the primary hitting stats provided much insight into how much a player would get paid.

At-Bats were by far the strongest factor in salary, which confirms the idea that players that get more at-bats will tend to get paid more. This makes sense, of course, as players who get more at-bats will likely be the better hitters on their team. This has been a [new(ish) development](https://www.beyondtheboxscore.com/2015/4/20/8452647/the-new-2-hitter-mlb) in the Major Leagues; managers have been putting their best hitters towards the "top" of the lineup (hitting first or second versus third or fourth, where the "best" hitters traditionally have been slotted) to maximize the number of at-bats that they get. 2018 examples of this include NY Yankees slugger [Aaron Judge](http://www.sportingnews.com/mlb/news/aaron-judge-yankees-lineup-second-batting-order-giancarlo-stanton-fantasy-baseball/17gl5hpmuerdu1284b742dpxc0), Cincinatti Reds perennial MVP candidate [Joey Votto](https://www.mlb.com/news/anthony-castrovince-joey-votto-batting-second-paying-off-for-reds/c-73084424) and arguably the best player in baseball, Mike Trout of the LA Angels. So, my best model ultimately suggested that players who get the most at-bats get paid the most, and we know that the best players tend to now get the most at-bats. So the model confirmed the idea that the best players get paid the most.

<p align="center">
  <img src="/assets/images/mlb_free_agent_images/mike_trout.jpg" alt="Mike Trout"/>
</p>


But the model also, relatively highly, valued Slugging Percentage over other advanced measurements of hitting prowess. Slugging Percentage, for your reference, is the average number of bases a player gets during an at-bat (minimum 0.000, maximum=4.000 for hitting all Home Runs). If a player has a slugging of 1.000, they average 1 base per at bat. They could have this with 4 singles in a row, or going 1 for 4 with a Home Run. Both events come out to 4 total bases over 4 at bats. Slugging is meant to provide weight to "better" hits over singles, as batting average and on-base percentage fail to do. So it's interesting to see that my model reflects what we discussed above; it's worth swinging for the fences and aiming for the larger hits. Not just because your team thinks it will provide more wins, but because it may just get you paid more in free agency.

## Lessons

I learned a lot from this project, both in the technical sense as well as from the project management side of data science. I feel strongly about recording these lessons, so I am going to save these for my next blog. There I want to flesh out some of the lessons and mistakes I made during this process. I think this is a crucial step towards improvement, and am excited to track these thoughts as I improve as a Data Scientist

Thanks! Let me know your thoughts or questions, as always

-Brian
