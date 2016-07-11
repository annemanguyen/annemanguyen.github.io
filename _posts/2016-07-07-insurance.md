---
layout: post
title: "Under the Coverage: Summary"
date: 2016-07-07 00:00:00
categories: [blog]
tags: Metis, census
excerpt: "What US Census features predict lack of health insurance coverage?
---

Substantively (shout out to Jer), the goal of this project was to identify individual-level predictors of lack of health insurance coverage. On the technical side, I tested multiple classification algorithms: logistic regression, decision tree/random forest, and support vector machines. I also made some D3 visualizations.

In full disclosure, I had been using a Chromebook up until the end of this project, which worked surprisingly well until it decided to delete my entire Crouton installation, so unfortunately I'm reconstructing my work for this post based off my notes (shout out to antiquated writing technologies) and presentation slides. (Now I use a souped-up five-year-old ThinkPad - true nerd computer - and I put everything on Git like I should.)

### Data

I used the [2014 PUMS ACS data from the US Census Bureau](http://factfinder.census.gov/faces/tableservices/jsf/pages/productview.xhtml?pid=ACS_pums_csv_2014&prodType=document), which collects both individual- and household-level responses from residents on a variety of economic/demographic questions. This is a pretty large dataset, like 8GB, so I worked with it on an AWS instance (Chromebook, remember?). I should point out that the Census Bureau has their own set of [reports](http://www.census.gov/topics/health/health-insurance.html) on health insurance coverage.

So the first thing to note is that there are state-level differences in rate of non-coverage. There are also policy differences, namely in whether or not they have participated in Medicaid expansion. Here's a fun D3 that looks more bizarre the more you look at it (uh what are you doing there West Virginia...?):

![states](/assets/3_expansion_states.png)

You can kinda already see that, unsurprisingly, non-expansion states have larger uninsured populations.

At the individual level, there are a couple of factors that intuitively would seem to have a large impact on the likelihood that a person would have health insurance coverage.

Here are the coverage rates for types of employment, with the size of the bubble as a function of the size of each category (as a proportion of the population). That outlying self-employment circle definitely suggests a market for Oscar and the like! I pooled unemployed people and people not in the workforce into a 'not working' category - ended up doing a lot of those types of adjustments; more below.

![employment](/assets/3_work.png)

 So my intuition was that income would be the main driver of insurance coverage, which does appear to be the case:

 ![income](/assets/3_income_coverage.png)

 The effect of age looks similar:

 ![age](/assets/3_age_coverage.png)

 (As you can see, women tend to be more likely to have coverage than men at all levels of income and age.)

 But just a reminder that policy matters:

 ![actual](/assets/3_age_cutoff.png)

 Here are the actual rates of coverage. You can see where Medicaid kicks in at 65, and you can also see the dip at 26 when young adults have to leave their parents' plans.

### Modeling

So we can put all these things together and try to create a predictive model for health insurance coverage.

The main problem here is that this is a highly unbalanced dataset. The overall insured rate across the US based on this PUMS data (counting only 18+ adults living in households [versus group housing arrangements]) is something like 74%, which means simply always predicting 'covered' will give you a decent-looking accuracy but obviously isn't very useful. I tried both upsampling and downsampling, but it didn't particularly improve the logistic model.

Anyway, I read a bunch of the literature and came up with some features. The way certain things are coded in the ACS data makes it kind of a process to back out particular categories, like separating roommates from unmarried couples and other household/family arrangements. I also chose to combine a couple of different variables to create new indicators of things like the proportional economic responsibility of each household member (aka how many people a particular person supports relative to income).

Here's a list of variables I used in various configurations:

 | | | |
 ---|---|---|---
food stamps | internet access | multigenerational family | workers in family
rent | property value | family income | household income
no. children | no. family members | citizenship | sex
disability | race/hispanic | employed | married
military | schooling | industry | age
wages | earnings | income | poverty line
state | household type

Lots of these measure the same thing in different ways, which I had to be careful with in the logistic regression. I used L1 regularization and feature importances from random forest to whittle down the model. Here are the coefficients from the final model:

![coeffs](/assets/3_logit_coeffs.png)

I would probably take away direction and magnitude from this, but not necessarily the actual value of the coefficient. I believe the r-squared for the model was fairly low, around 0.5 or so. As expected, individual income and wealth are by far the strongest predictors of insurance coverage. It was gratifying to see that receiving entitlement benefits tends to go with having health insurance, because it suggests that lower-income populations are being reached, and that it's probably easiest to roll initiatives out when there are already existing points of engagement (which is to say it's easier, obviously, to become informed/involved when you're already in contact with government agencies). (Spoiler: this is something we'll see again in my final project regarding 311 service requests calls!) Again, I think you can see a lot of these variables go together in describing a particular type of household - probably immigrant, lower-income, multigenerational, etc.

Finally, in addition to building the logistic regression model, I also tried random forest and SVM, as well as a logit with gradient descent. All of these performed fairly similarly, which is to say, not great - about 10-15% better than a naive all-yes prediction.

Model | Precision | Recall | True Negatives
--- | --- | --- | ---
base logit | 0.85 | 0.89 | 0.58
random forest | 0.89 | 0.89 | 0.89
decision tree | 0.87 | 0.89 | 0.68
SGD logit | 0.87 | 0.81 | 0.29
SVM | 0.79 | 0.89 | 0.00

I give the precision and recall for the best-tuned models, but the real parameter of interest are the true negatives, which is to say that substantively, what we care about is correctly identifying who _doesn't_ have insurance, so we can see what populations are underserved and fix the issue. The explanatory logit hopefully gives us clues as to contributing factors and therefore tells us whom to target with outreach campaigns or policy changes. (Although my model doesn't tell us _why_ certain groups don't have insurance - whether they fall into an expansion gap or they are less likely to know about the coverage requirements or have more trouble accessing the marketplace or etc.)

Random forest performed the best, which was a trend I saw in other projects as well. It also has the advantage of recovering feature importance, but you do lose some interpretability of the results.
