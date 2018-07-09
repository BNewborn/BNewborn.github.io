---
layout: post
title: Increasing Transparency in Albany
description: Using NLP to analyze, sort and review bill proposals in the NYS Legislature
image: /assets/images/state_senate_images/legislature.png
---

## Intro
For our final project at Metis, I wanted to use my data science skills on a project related to government transparency. After a chat with a friend earlier this year about non-profit advocacy and lobbying in Albany, I knew that I wanted to work on making the legislative process in Albany easier to understand for everybody.

## The Problem
Shout out to my friend (credited here as "J"), who mentioned to me during a friendly conversation that her non-profit, every year, has to spend tens of thousands of dollars hiring lobbyists. They do this so that they can effective advocate and understand the legislative process in Albany. I was blown away hearing that - her organization should be spending every available dollar on bettering the lives of NYC's children (and they do an amazing job), not on chasing expensive lobbyists to get access in the policy arena. We also discussed how often the only easy insight into these bills from the city is newspaper coverage, which probably doesn't give the whole policy scene as much as an observer wants.

After we talked about this, I wanted to see if we could use publicly available data to provide better insight into the lawmaking calendar combined with a historical overview of the last decade or so in the legislature.

![The Capitol Building in Albany](/assets/images/state_senate_images/legislature.png ){:height="50%" width="50%"}


## Data
I downloaded two major data sets for this project. The first was thousands of articles from 2009-2018 featuring key words like "State Senate" and "New York" (among others, including "Albany Legislature", "Bill" etc.) via the NY Times API. The Times API search function is a bit tough to use, so I intentionally kept my search terms vague and figured I'd cull down content later in MongoDB. Once I cut down the original pull, I had about 900 articles over that 9 year span. I used the URLs from the NYT API to then scrape full article text (the NYT API doesn't release the full article text, only a "snippet", as they call it).

The second data set was all bills proposed and adopted in the NY State Senate via their [Open Legislation portal](http://legislation.nysenate.gov/static/docs/html/). If you are interested in legislative transparency, this is a fantastic API to use. I relied mostly on the "Bills and Resolutions API" that they keep available as I was interested in the full text and metadata (sponsorship, vote count, date, etc.) of bills, but there are also APIs publishing public hearing data, calendars and even the NYS Laws themselves (i.e. what's actually on the books). Shoutout to the NYSenate Tech Team [Git here](https://github.com/nysenate/OpenLegislation) for their great work and fantastic documentation.

Both datasets were cleaned - stripped of punctuation, spaces, capital letters and stop words - and each ran through a separate topic analysis pipeline. At first, I wanted to see if we could tie the two datasets' topics together, but I soon realized that the NYT Topics would not map well to legislative efforts. I'll discuss why below. Instead, I ran two separate pipelines to find topics in each body of text accordingly.

For both bodies, I tried both CountVectorizer-->LDA and TF-IDF-->NMF for topics. Both, in my experience, have worked on different types of text better, so I wanted to try each and pick which one best expressed accurate topics of our article or bills. Ultimately, the Times topics were clearest with LDA while the NYS Legislature bill topics were clearest with NMF.

## NYT Exploration
First, analyzing the NY Times coverage of the State Senate, I found that most of the Times' coverage tends to focus on more of the political side of Albany - ethics, corruption and infighting amongst the Democrats and Republicans (of which there has been **plenty** in the last 10 years. For a few of these years there was a splinter in the Democrats where a few of them sat with their Republican colleagues and gave a majority to the red team. Could you imagine drama like this in DC? It would break CNN.)

Here's a topic graph reflecting the most common topics in NYT Coverage.

![Topics in NYT Coverage of the State Senate](/assets/images/state_senate_images/times_topics_bar.png){:height="50%" width="50%"}

As you can see, 3 of these heavily reflect the aforementioned political side of the Senate. First off, let me say that these are very important issues - for decades Albany has been notoriously corrupt, and the Times' coverage of Albany have helped push for ethics reform as well as jail legislative leaders for bribery and corruption. It's just that these articles don't really help my friend's non-profit understand what's being passed or not passed in Albany. So the main takeaway is that the Times coverage is likely insufficient by itself to understand the legislative process in Albany. This fits with my first instinct, but it was reassuring to see the data back this up.

And, on top of this, the quantity of coverage around the State Senate by the Times has dropped over the last decade. There are sometimes spikes in coverage, like when the state legalized same-sex marriage in 2011, but it soon after reverts to the downward trend.

![Topics in NYT Coverage of the State Senate](/assets/images/state_senate_images/nyt_coverage_overtime.png){:height="50%" width="50%"}


## Senate Bills Exploration
Running each bill's legislative text through topic analysis gave me distinct topics, broken down by policy sector. This was extremely helpful as it allowed us to easily parse through thousands of bills to find those that are relevant to our cause (in my friend J's case, education bills were of paramount importance). Each year in Albany, thousands of bills are proposed and passed, and most of those that are passed are "Honoring Resolutions" - i.e. honoring a local high school team for a championship, or a local citizen for bravery. While these are a nice aspect of our government, they tended to overwhelm any quick glances of "recent bills", so using topic analysis and filtering was an easy way to parse through the stacks of these honoring resolutions.

![Topics in NYS Senate Legislation](/assets/images/state_senate_images/senate_topics.png){:height="50%" width="50%"}

## Visualization
Ultimately, this analysis is only as useful as the takeaways that we can provide to key stakeholders in the non-profit sector. The bulk of my energy was spent on figuring out how to best present this information in an easy-to-digest and clear way. I decided to build an interactive dashboard that could ultimately be used by end users. I had worked a bit with and wanted to improve my skills in [Dash by Plotly](https://plot.ly/products/dash/) as it combines the beauty of d3.js (which can be super hard to learn at first, some say!) with the ease and comfort of python. After a few weeks of practicing with Dash, I now feel much more comfortable using it for graphs, charts and tables, and am excited to continue to grow in this.

For this dashboard, I made a few pieces of information easily available. First, in the top left, you'll have a by-topic bar graph of bills adopted and proposed in a given month/year. This serves as the launch point for the other data you'll see. Click on a topic like "education", and the rest of the dashboard updates.

![Link to Dashboard!](http://bnsenatefinal-env.qtqc42jatv.us-east-1.elasticbeanstalk.com/)

![My Dashboard](/assets/images/state_senate_images/full_dashboard.png){:height="50%" width="50%"}

On the top right, you'll see two tables. These represent the lawmakers, in both the Senate and Assembly, that have proposed and passed the most laws in education (or any other selected section) in that year. This is very valuable insight for an advocacy team. I studied Public Policy in college, and we once had an assignment to design policy advocacy strategy. It took me days to filter through recent legislation to figure out common co-sponsors across an issue type, and this dashboard does that in just a click. An organization can use this to figure out which lawmakers can be approached as allies for voting or drafting laws, as well as which should potentially be targeted as swing votes for their passion causes.

![Education-focused lawmakers](/assets/images/state_senate_images/legislators_education.png){:height="50%" width="50%"}


Finally, on the bottom are two line graphs that share an X-Axis (timeline by month and year). The graph in blue shows the proposal of laws in this topic over time. An easy trend to pick out is that there is a big spike in proposals every January of odd years. This makes sense, as this is when the legislative session starts in the Senate. Senators are elected for 2 year terms in November of even years (a few months from now, November 2018, for example, is a senator election cycle). Senators then sit for the first time in January (same as the US congress, in case that rings a bell).

![Proposals/Passage by Month](/assets/images/state_senate_images/blueorange_education.png){:height="50%" width="50%"}

The graph in orange reflects a percentage of *passed* laws in our topic as a percentage of all passed laws that month. So a higher orange line represents a higher focus of importance on our topic versus others, most likely. There's another trend here worth pointing out - for education, while most bills are proposed in January, there is actually a solid percentage of laws proposed and passed in the later spring months (April, May, June) before the Senators depart for summer recess (having a summer recess sounds lovely, by the way. All industries should take note :smiley:). A takeaway here could be that while it's tempting to propose laws you're passionate about early in the session, there may be too many bills then to effectively lobby other members (who in turn have other focuses they care about). Organizations should potentially wait for the January spike to subside, and then push their allied lawmakers to propose bills later in the spring.

Here's a demo of the full dashboard in action

<iframe src="https://player.vimeo.com/video/278546669" width="640" height="400" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>


## Conclusion

I presented these findings, and a demo of my dashboard to Metis' Spring Career Day on June 28. From data cleaning, topic analysis and clear visualization, I believe that I was able to add substantial value for organizations looking to clearly understand the legislative process without relying entirely on lobbyists. This dashboard represented just a fraction of available patterns, insights and lessons, as there are numerous other questions that could be of value regarding topic-based bill segmentation.

But for now, I am excited about this product. I had a lot of fun working in Dash to get the dashboard to be user-friendly, and am looking forward to getting even better with it in future data visualization questions.

*The code for this project is summarized and cleaned [here](https://github.com/BNewborn/nys_senate)* in GitHub.

[Link to Dashboard!](http://bnsenatefinal-env.qtqc42jatv.us-east-1.elasticbeanstalk.com/)

Thanks for reading! Let me know your thoughts or questions, as always

-Brian
