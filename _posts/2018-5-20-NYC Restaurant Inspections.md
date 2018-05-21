---
layout: post
title: NYC Restaurant Grade Prediction
description: Predicting if a restaurant will get an "A" from the Health Department
image: /assets/images/nyc_restaurant_images/a_grade_real.jpg
---

## Intro
For our third project at Metis, I was expected to create a type of classification model - predicting some sort of discrete labels for a given input. This is in contrast to a continuous model, like my previous project on MLB free agent values. For this model, I dove into New York City's Health Department data on restaurant inspections, and used this data to design an optimal model for the Health Department to predict and assist restaurants with getting more "A" results.

## The Question
For this project, I built the following scenario: a few Senior Staffers in the NYC Department of Health and Mental Hygeine approached me with a question - they have designed an intervention that they believe can help restaurants improve their food safety habits and practices. They want to specifically target restaurants that do not get an "A" from the city, so that they can help these businesses maintain healthy food safety as well as mitigate bureaucratic costs for the city. A true win-win-win for citizens, business owners and the City Treasury.

<p align="center">
  <img src="/assets/images/nyc_restaurant_images/a_grade_real.jpg" alt="A Grade"/>
</p>

## Data
I downloaded historical restaurant inspection data from [NYC OpenData](https://data.cityofnewyork.us/Health/DOHMH-New-York-City-Restaurant-Inspection-Results/xx67-kt59). This portal features raw data across many sectors, but specifically also includes a line by line database of every inspection since 2014. I also downloaded data from the Federal Census Bureau and the New York State Liquor License database, though I was only able to successfully join the former with our NYC data. Ultimately, my inputs looked something like this:

* **Restaurant Type** - NYC tracks what type of cuisine is served at an establishment. Example labels include "Pizza", "American", "Chinese", "Thai", "Japanese", "Cafe" and many others. There were about 100 distinct labels used citywide
* **Month and Year of Inspection** - I included what month and year a given inspection was done as well. This was so that we could control for any seasonal changes in inspection behavior. For example, maybe restaurants do worse in the summer vs other months because of the stifling heat in New York City. By including these data points we would include these ideas in our model
* **Neighborhood Income** - By joining on a restaurant's ZIP code, I used a proxy measurement to estimate a given neighborhood's wealth. I found how many people in a given ZIP code declared over $100,000 in income last year. This would be by no means a perfect measurement of neighborhood prosperity, but I felt it would get us enough information to see if there is an effect.
* **Longitude and Latitude** - Using Google's StreetAPI, I was able to get an exact longitude and latitude for each restaurant in my database. Not only would this help with interesting map visualizations, but it would also add an additional measurement of location based control for our model to use


Armed with these data points, and approximately 80,000 inspections in the last 4 years, I was ready to explore the data and build a predictive model.

## Data Exploration

I had never known this, but it turns out that over 90% of restaurants in New York today have an "A" rating from the Health Department. Digging into my data even further, I found that 87.2% of inspections in the last 5 years resulted in an "A" - this difference makes sense when you think about a restaurant getting a "B" or "C" and then getting an "A" on their next check-up. They'll still have an "A" as of today but their rate of getting an "A" is at 50%. Just an example to clarify that difference

The city also has publicly stated that these rates have only increased since the inspection program started in 2010/2011 under Mayor Bloomberg. Mayor Bloomberg was then publicly stating that the "A" rate was closer to 75-80%. So either restaurants in the 5 boroughs are getting cleaner, or they're getting better at passing these inspections. Since there's a [whole industry](https://www.nytimes.com/2015/10/05/nyregion/health-exam-help-for-restaurants-to-avoid-rodents-or-worse-a-c.html?action=click&contentCollection=N.Y.%20%2F%20Region&module=RelatedCoverage&region=EndOfArticle&pgtype=article) to help restaurants get "A" ratings, I'll leave that up to the reader to decide

So with that clear imbalance in our dataset, I made our prediction metric either "A" or "not A" (i.e. either a "B", "C" or "Pending" grade). Any more detailed splits into the "Not A" group would have risked too small of a dataset for effective analysis.

## Visualization
I wanted to see if there were any gross imbalances in our data for grades when split by Borough or by Restaurant Type. Maybe we could easily see any imbalances in these features that would help predict grades.

<p align="center">
  <img src="/assets/images/nyc_restaurant_images/grades_by_boro_total.png" alt="Grades by Boro, %, 2014-2018"/>
</p>

As you can see, grade distribution by Boro historically has been pretty consistent across the 5 regions.

<p align="center">
  <img src="/assets/images/nyc_restaurant_images/cuisine_type_gross.png" alt="Grades by Restaurant Type, Gross, 2014-2018"/>
</p>

Grade distribution by restaurant type has also been consistent. [Previous research](https://ebph.it/article/view/9442) into the field indicated that restaurant type did not significantly add to a prediction whether a restaurant would grade better or not, but I included it. I did this because this previous research was completed in 2014 and an entirely new Mayoral administration has taken over inspections, so this effect may have changed since then.

## Model Selection
Now that I had my data in SQL, I cleaned and scaled it and began modeling. I tried a few model types, including Naive Bayes, Logistic Regression and Random Forest Ensemble modeling.

For all of these models, the baseline rate that they had to beat was 87.2%. As I mentioned above, the vast majority of restaurants received an "A" from the city on their inspection. Therefore, a "dumb" model that predicted every restaurant getting an "A" would still be right 87% of the time. So, any of our fancy prediction models had to be above that rate.

Naive Bayes consistently couldn't get above 88%, while logistic regression and random forest both approached 90% in accuracy. I decided to use these two and evaluate both against our cost concerns from the city.

## Cost Effectiveness
As I mentioned at the beginning here- our clients were interested in modeling the cost-efficacy of a targeted intervention to help NYC restaurants improve food sanitation practices. According to their office, an inspection resulting in less than an "A" costs New York City (at least) $250 due to having to send the inspector out to the restaurant again as well as the human labor costs of bureaucratically processing another inspection. And, their office had developed a targeted intervention plan that they believed could permanently improve food safety at a given restaurant. This would include literature as well as on-site training for staff. They tagged this intervention as costing $100 per restaurant. Therefore, our cost-effectiveness could be modeled by the following equation.

CB = $250 \* (**False Negatives**) + $100 \* (**False Positives**) - $150 \* (**True Positives**)

For every restaurant we "got right" (predicted Non-A that actually would have been a non-A), we'd save $150. For any false negatives (predicted "A", resulted in non-A), this would cost the city $250. For any false positives (predicted non A, would have resulted in an "A" to begin with), this would cost us an extra $100.

Using this equation, I wanted to measure which of our 2 remaining models would perform the best - ultimately, which model would maximize cost-savings for the city?

We'd do this by adjusting the threshold at which our model deemed likelihood of a restaurant being a not A. Both models, by default, started with a threshold of 0.5 - if the model deemed a chance of an A or not A as greater than 50%, it suggested this result. We can raise this threshold (harder for the model to predict a non A) or lower this threshold (easier for the model to predict a non A) and measure our cost-effectiveness at every instance. We'll then take the lowest cost and use that as our best-case scenario.

<p align="center">
  <img src="/assets/images/nyc_restaurant_images/randforest_cost_eff.png" alt="Random Forest"/>
</p>

As you can see, by lowering the threshold to around 0.02, our random forest model could minimize costs to around $17.70 per intervention. However, this is not very helpful - it essentially is predicting all but the best restaurants as not-A results. So the city would be way overtargeting restaurants with their intervention. Not only did logistic regression have a better cost-per-intervention, but it also had substantially more realistic restaurants predicted as non-A's

<p align="center">
  <img src="/assets/images/nyc_restaurant_images/logistic_cost_eff.png" alt="Logistic Regression"/>
</p>

As you can see, logistic regression was able to minimize costs at a threshold of 0.62 (a bit harder for the model to predict non-A than the default).

It did this by drastically reducing the number of false positives while only somewhat increasing our number of false negatives. This optimized our cost savings down to around $15.50.

**So, I ultimately presented our findings and predictions using this logistic regression model tuned to a threshold of   0.62**

## Conclusion

There were many different paths we could have used to help the City predict restaurant grades. In this example, we illustrated using logistic regression and random forest modeling to quantify the costs of targeted interventions.

By optimizing for the lowest possible cost, we are able to help a department that is always strapped for resources, both financially and human capital, to optimally spend their time and money for the greatest effect.

This was also only one specific way of viewing NYC restaurant grading. One can easily imagine the positive economic benefits of increased food safety on tourism, reduced healthcare costs due to sickness, increased productivity of a healthier citizenry, as well as less friction between small businesses and City Hall. Ultimately, targeting food safety presentations could have positive effects well beyond just the city treasury, but this was our specific way of looking at the issue.

<p align="center">
  <img src="/assets/images/nyc_restaurant_images/a_grade_thanks.png" alt="Thanks!"/>
</p>

Thanks for reading! Let me know your thoughts or questions, as always

-Brian
