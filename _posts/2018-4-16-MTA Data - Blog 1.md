---
layout: post
title: MTA Data - Metis Project 1
description: A review of turnstile activity in Queens
image: assets/images/mta_images/mta_logo.gif
---

Hello world! My name is Brian Newborn, and thanks for coming to my personal blog.

Today I am going to be writing up some interesting findings that I came across while completing Challenge 1 of my [Metis](https://www.thisismetis.com/) spring semester.

For this first week of classes, we worked on multiple projects centered around New York City's publicly available MTA subway usage datasets. You can find this data [here](http://web.mta.info/developers/turnstile.html).

<p align="center">
  <img src="/assets/images/mta_images/mta_logo.gif" alt="MTA Logo"/>
</p>

Though I worked on a group project with a few classmates, I want to share an interesting finding that I discovered when working on a solo coding "challenge". Before we jump into this, let me introduce the dataset and column headers.

### Column headers
<ul>
<li><b>C/A, UNIT, SCP </b>- These are MTA specific labels for a given turnstile and Control Area. The main takeaway is that the combination of these three data points can be isolated to represent a unique turnstile</li>
<li><b>LINENAME, STATION</b>- These are the subway lines that run through a given station and the station name, respectively. You need to use both of these data points to isolate where a turnstile is because certain stations have the same 'name'. For example, the 23rd Street stops on the 1 and 6 lines have the same STATION name. But, their LINENAMEs are different, so you can use both to ensure each's data remains separate</li>
<li><b>DATE, TIME</b> - these reflect the date and time, respectively, that this line of data represents. For example, a line with 1/14/17 and 19:00 represents that this data was taken on January 23rd at 7PM.</li>
<li><b>ENTRIES, EXITS</b> - these represent the <i>cumulative</i> entries and exits at this turnstile at the given time. As you can see, this becomes unusable in its raw form, as the totals continue to increase every time interval. To clean this up, I used Python to find the difference in one line versus its predecessor, so that I had the number of entries/exits in a given time interval. </li>
</ul>

<br>
Here's an example screenshot of some of the MTA data and headers.
<p align="center">
  <img src="/assets/images/mta_images/mta_data_sample.png" alt="MTA Data - Sample"/>
</p>

As the MTA datasets are large files spanning years of subway data, I had to narrow down the time window that my analysis would cover. Being a January birthday, I chose to use data from 12/31/2016 to 1/27/2016. This came out to around 777,000 lines of data (imagine how large this analysis would be with even just a few months!). After cleaning this data and removing negative or erroneous turnstile counts (sometimes the turnstile counters reset their count randomly, but this was a relatively rare occurrence), I was ready to investigate and explore this data using Python.

Specifically, I worked mostly with Pandas, a valuable Python library used for evaluating and viewing tabular data. I also used Seaborn for visualization - any graphs seen below were created with Seaborn.

Amidst a few exercises, I wanted to isolate our data to one station and drill into daily entries and exits. Being a Mets fan (LGM!), I decided to isolate the data to the 7 Train stop at Mets-Willets Point. This is the subway station you get off at when heading to Citi Field, home of the Mets. But, there are plenty of people living around here, as evidenced by the fact that there are at least a few thousand entries and exits through this station on a given day, even in January.

<p align="center">
  <img src="/assets/images/mta_images/citi_field.jpg" width="600" alt="Subway with Citi in the Background"/>
</p>

After filtering our larger dataset down to just this station, I then grouped turnstile counts by day to see entries/exits on a day-by-day basis.
<p align="center">
  <img src="/assets/images/mta_images/citi_daily_ridership_117.png" width="650"  alt="Entries/Exits at Citi"/>
</p>

As you can see, there is a somewhat standard pattern for the first few weeks. Weekend ridership drops to around two thousand per day, while weekdays have closer to 5000 entries/exits per day. Thursdays and Fridays tend to be a bit higher than earlier weekdays, but this is not a massive difference. But wait, notice that huge spike on January 25? Why would there be so many more riders (over 16,000) versus other weekdays?

After double and triple checking my dataset for duplicates or bad values, I drilled further into the outlying data. Let's see the difference between entries and exits at Willets Point across the days - maybe there's a discrepancy there.

<p align="center">
  <img src="/assets/images/mta_images/citi_daily_entriesexits_117.png" width="650" alt="Subway Turnstiles by Entry/Exit by Day"/>
</p>

Daily exits are in red and entries are in purple.

As you can see, these two lines are almost perfectly in sync every day, execept for that late blip in January. On Jan 25, exits are almost 3X higher than entries. And both are substantially higher than their expected weekday values. Why would this be?

I dug into local news articles from then, finding that there was actually a massive fire in Queens on Wednesday, January 25.

[ABC News Article here](http://abc7ny.com/news/fdny-queens-fire-that-destroyed-businesses-caused-by-cooking/1720535/)

This would certainly make sense - Willets Point is somewhat of a larger station on the 7 Line, so maybe the MTA stopped trains there to avoid sending trains into a busy emergency scenario. And then after some more searching, I found our "smoking gun".

<p align="center">
  <img src="/assets/images/mta_images/NYCT_Subway_tweet.png" width="500" alt="Subway with Citi in the Background"/>
</p>

The MTA did in fact stop trains at Mets-Willets Point that day! [Link to the tweet here.](https://twitter.com/NYCTSubway/status/824370519537545217) And, note the time of this tweet. This fire occurred in the afternoon, so the majority of disrupted commutes would occur recorded as exits, since commuters were traveling back home to eastern Queens or Long Island. And anybody planning on taking the subway into Manhattan in the afternoon had to enter here instead of getting on at Main St.

So it certainly makes sense that exits were much higher at Mets-Willets than they normally would have, while entries were somewhat higher than their expected value.

<p align="center">
  <img src="/assets/images/mta_images/7train.png" width="500" alt="7 train route"/>
</p>

When I found this tweet, I was really amazed that I was able to notice something odd in the data, look at the real-world context from that day, and ultimately find a likely cause. It feels pretty cool to be able to parse through a 700K line dataset and find patterns that match the real world. While this is no event to celebrate (I am certainly really sorry for those business owners and local residents that lost property in the fire), this code discovery is worth celebrating.

For a first project, I am quite excited by my early findings and budding capabilities in Python. I'm looking forward to further developing my skills and toolset, and will be posting more data science findings as the Metis semester progresses.

Got a comment? Feedback? Questions? [Email me with your input!](mailto:bri.newborn@gmail.com)
