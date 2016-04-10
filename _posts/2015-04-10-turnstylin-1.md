---
layout: post
title: "Turnstylin' and profilin' I: Theory"
date: 2015-04-10 00:00:00
categories: [Metis, MTA]
tags: Metis, MTA
---

#### The problem

A women-in-tech nonprofit wants to improve their outreach strategy. They have an annual gala coming up, and they want to raise awareness and collect signatures from folks who might attend the gala or contribute to their organization by placing street teams at subway entrances.

<small>A minor underlying problem: I'm sympathetic to women-in-tech issues, as a newly inducted member of that vaunted demographic, but I'm also sympathetic to commuters-avoiding-canvassers. Resolving moral conundrums is left as an exercise for the reader. </small>

#### Defining an approach to the data

How should data be used to solve this problem? Narrowly, it could be enough to say, okay, based on MTA ridership figures, if you want to reach the most people, you should send teams to GCT, Penn Station, and Union Square based on _x_ reasons (e.g. ridership, populations of interest, etc.). But the thing is, x reasons should be defined by the client!

So we take a step back. We can use the data to create sets of options that respond to client input. For maximum usefulness, we want to create a tool that allows the client to select the strategy that's best for them, rather than defining that strategy ourselves.

Okay, so what's our strategy for strategizing? We have a bunch of data (MTA ridership figures), but how do we use it to solve the organization's problem?

Obviously, we should start with the organization's givens.

![goals to strategies](/assets/goalsstrategies.jpg)

In this case, the organization has two stated objectives:
1. raise general awareness
2. reach potential donors and participants

These objectives are complementary, even overlapping, but ultimately distinct: they imply targeting different populations. This in turn determines what data we want to use and how we process it.

In terms of maximizing exposure -- achieving the highest eyeball count -- we don't care who sees. A set of eyeballs is a set of eyeballs (discounting children, non-English readers, etc.) and the more people who are exposed to the organization's name and presence and learn about their mission, the better. To this end, we simply want to identify which stations have the most people at any given time.

Of course, people knowing is not the same as people caring, much less people becoming involved. Given that outreach is a costly activity, we want to try to find the folks who are most likely to become supporters (i.e. to donate and/or to attend the gala). This means selecting demographics that are more likely to be able and/or willing to participate. We can roughly proxy those groups as populations with high incomes and/or high interest in the cause.

Now that we know who we are looking for, we can identify which stations to target and at what times to do it for maximum effectiveness.

High-income people are likely to live in high-income areas (as determined by median-income census figures) and to use the subway stops there. To find residents, specifically, we want to engage those people who are leaving home in the morning (i.e. entering the subway) and coming home in the evening (i.e. exiting the subway).

Highly interested people might be those with strong ties to the subject: they may work in tech or other information fields. ("Women-in-tech" has two dimensions,and it could be argued that the "women" part is more important than the "tech" part, demographically. However, given the smaller population and greater spatial concentration of tech industries, it seems easier to capture women among techies rather than techies among women.) Looking for tech workers suggests concentrating on stations in areas with lots of tech companies, and appealing to commuters who are coming to work in the morning (i.e. exiting the subway) and going home in the evening (i.e. entering the subway) on weekdays. 

Universities also have a strong link to the tech sector. Of course, we can get more specific than that: we can say, look, we have a particular interest in supporting and getting support from young women, university students, who will soon be joining the field. We postulate that people affiliated with a university will use stations nearby, either to come to work/school or because they live on campus. Given that we're looking for both commuters and residents in this case (and because student schedules are notoriously erratic), we look at all times and days.

Okay, so now we have a strategy for identifying and processing relevant data!

#### Assumptions

We want to create the most flexible solution with the most room for client input, but our approach still relies on a certain number of assumptions.

The core idea here is that station usage at a particular location is a proxy for identity. If a person uses a station in a certain way, they are likely to be associated with the places around it in the specified manner. That is to say, all else equal, a user going into the subway near John Jay is more likely to be affiliated with John Jay than a user of another station. Further, a user who enters the subway (which is to say, leaving the area) at 6PM is likely to be going home from work from a place nearby. 

There are also behavioral assumptions at play. All else equal, this approach assumes that people who have more income have more disposable income that may be free for donation. Additionally, people in related fields will be more interested in the cause (and, of course, we assume that interest can be converted into action).

#### Caveats

##### Questioning assumptions

Obviously, these are subject to interrogation. The point of this project, however, is to demonstrate how such assumptions drive data selection. Any other theories about best target populations (ideally client-defined) can be applied. For instance, some research suggests a U-shaped relationship between income and donation (as a percentage of income).<sup>[1]</sup> [1]:http://nccs.urban.org/nccs/statistics/charitable-giving-in-america-some-facts-and-figures.cfm 

Accordingly, we might instead choose the set of stations falling in areas where the residential median income is under $100k, or even betwen $45-50k for a whopping 4% of income donated. We would also want the top end of that distribution, selecting households that report income of $2m or higher, but that is not captured in census figures. We might instead use other sources of data to identify that group: known high-level donors, Forbes, etc. Of course, at that point, a subway street team approach is no longer relevant.

Of course, these things can be infinitely complexified. Is it more effective to solicit a few large donations or many smaller donations? (For a case study, see American electoral fundraising, 2008/12/16.) We might define a variable to capture available money flowing through a station based on median income and use a likelihood-of-giving multiplier to estimate expected yield. We might also calculate, based on IRS data, how _likely_ residents of a given area are to donate, rather than how much they might give.

Other interaction effects can be harder to quantify. What if certain demographics are more likely to give but also less likely to be involved in tech? Further, what if higher income means decreased likelihood of using the subway? Many wealthy people who work in NYC commute from outlying areas, such as CT, so it may be more promising (again, assuming we accept that people with high income are a worthwhile target) to choose stations that link to regional rail and the like.

##### Demographic overlap

It's important to note that though the demographic groups we identifed are theoretically distinct, they may have significant overlap. Ceteris paribus, high-volume stations will have higher numbers of students and tech employees passing through (though the _proportion_ of these targets may not be higher than at stations near their institutions).

Furthermore, a tech worker may also be in the high-income group -- in fact, it's relatively likely. This is a concern if the rate of overlap is high enough that outreach efforts become redundant. 

Demographic groups may also overlap not only substantively but geographically. In fact, this is the ideal: a station with high traffic, of which much is high-income and tech-interested, would be the best choice for street team deployment. Overlap is not a problem if the strategy is non-exclusive.

