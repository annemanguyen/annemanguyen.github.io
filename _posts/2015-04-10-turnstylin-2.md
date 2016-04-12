---
layout: post
title: "Turnstylin' and Profilin' II: Data"
date: 2015-04-10 00:00:00
categories: [Metis, MTA]
tags: Metis, MTA
excerpt: "
#### Cleaning the data

The basics: MTA data (available [here](http://web.mta.info/developers/turnstile.html)) is a set of weekly tables. Entries and exits are given as running totals for each turnstile in a station at 4-hour intervals. (For the most part; see below.)"
---

#### Cleaning the data

The basics: MTA data (available [here](http://web.mta.info/developers/turnstile.html)) is a set of weekly tables. Entries and exits are given as running totals for each turnstile in a station at 4-hour intervals. (For the most part; see below.)

Understanding what a row of data is can be difficult because there's no clear definition of the terminology the MTA provides. For example, here are the column names and the first row from last week's data.

~~~
C/A,UNIT,SCP,STATION,LINENAME,DIVISION,DATE,TIME,DESC,ENTRIES,EXITS
02,R051,02-00-00,59 ST,NQR456,BMT,03/26/2016,00:00:00,REGULAR,0005595746,0001893277
~~~

It's easier I think, to start with SCP. That is the ID for the specific turnstile (e.g. the second one out of three) in the UNIT R051, which is the bank of three turnstiles. C/A, or control area, refers to the vestibule area of the station: from where you start going underground up to where you actually pass through the turnstiles. This area may contain the booth or the ticket machines (or either, or neither). There can be multiple of these entrances, or control areas, per station.

So, grouping these three IDs (C/A, UNIT, SCP) together will give you an exact description of a turnstile, which you can use to compare across days, for example. Practically speaking, though, it's more useful to look at groups of control areas or stations.

LINENAME is particularly important, as it's the easiest way to distinguish stations with the same name. There are four stations named "23ST," for example (and they are all called 23rd St on their respective lines), but if you care about geographic location, you need to group by line. The 23rd St. ACE station is on 8th Avenue, for example, and it's 0.7 miles away from the 23rd St. 456 on Park Ave.

DIVISION is sort of an artifact of old subway operation. DATE and TIME are self-explanatory, and again(!) it's important to remember that ENTRIES and EXITS are running totals, so you have to subtract the count from 00:00 from the count from 00:04 to get how many people actually passed through that turnstile in that four-hour period. I don't actually know when the running count starts - from the beginning of data collection? I don't think it resets per year, for example, since this line shows that 5.5 million people have passed through this one turnstile as of midnight on 3/26/15.
___

As frustrated data scientists all over the internet will attest, this somewhat confusingly structured and prone to errors. 
    - Variation in measurement intervals: 
        - random spotchecks, resulting in an additional/replacement measurement at 08:00, 12:00, 14:34, 16:00, etc. 
        - intervals are not always in four-hour increments, and they may start at different times, e.g. 07:00, 11:00, etc.
    - Straight-up miscoding: 
        - seriously large numbers that appear to be two counts pasted together: if 00:00 is 54321, 04:00 is 5432155321.
        - negative counts, both independently and between intervals, e.g. 04:00 shows a lower cumulative total than 00:00 (I still don't know how to explain this one)
        - 0 counts or missing data for turnstiles that are not open for some period

It could be problematic to simply recode the interval times, so I would suggest grouping them such that both 08:00-12:00 and 07:00-11:00 are AM entrances, etc. Of course this gets tricky with an interval like 10:00-02:00 - is that morning or afternoon? Obviously, outliers and obvious errors should be dropped. Make sure to differentiate between stations with the same name!

#### Selecting data

We assume that this nonprofit's annual gala is in the beginning of June of each year. As such, given limited resources, we recommend outreach efforts occur in the six-week leadup, mid-April through May, to the event. Looking at last year's data for this period gives an estimate of station traffic and controls for seasonal effects. Therefore I use the MTA datasets from the week of 4/18/15 to 5/30/15.

##### Selecting universities

I specifically looked at entry/exit numbers for stations near universities (as in the last post, we select this category based on the assumption that people who use stations near universities are affiliated with them, and that they may be more interested in women-in-tech issues [due to increased likelihood of being in tech? or specifically being a woman in tech? or specifically being a woman thinking about going into a tech career?]).

As such, I took the top ten largest colleges/universities in New York City based on student population (a list found at (https://colleges.niche.com/rankings/largest-colleges)) and found all subways within a 0.3 mile radius. This had the effect of excluding universities in the outer boroughs, despite their large sizes. Here's the list by size: 

|University | Station Name
|---|---
|NYU | W 4 ST-WASH SQ
| | 8 ST-B'WAY NYU
|CUNY Hunter | 68 ST-HUNTER COL
|CUNY Baruch | 23 ST (6 line)
|CUNY John Jay | 59 ST-COLUMBUS
|CUNY City College | 135 ST (BC line)
| | 137 ST-CITY COL
|Columbia | 116 ST-COLUMBIA

We can already see that there's overlap between university stations and transit hubs/busy areas (e.g. W 4th and Columbus Circle). The proportion of students to the general population at those stations is probably lower than, for example, the proportion at 137 St. Still, ceteris paribus, we expect more student traffic even at stations like Columbus Circle than at similar but distant hubs.

We should also note that several of these stations (as well as other selected stations in the analysis) do double-duty: they fall within a 0.3 mile radius of universities that were too small to make the cut. Cooper Union, for example, is right next to NYU and uses the same subway stations. I haven't tried to differentiate them, but they are good to keep in mind as an added benefit for certain stations.

##### Selecting tech hubs

Defining tech hubs is a bit fuzzier. Looking at startup density maps like the one found at (https://digital.nyc/map) doesn't show a clear visual pattern. Instead, I used conventional wisdom/real estate definitions as propagated in the media, both official (e.g. Mayor's Office materials) and unofficial (newsmedia). These commonly refer to two NYC areas, Silicon Alley, around the Flatiron Building in Manhattan, and the Brooklyn Tech Triangle, in DUMBO. I selected stations that fell within these boundaries.

Here's the list:

|Silicon Alley 
|*---| ---
| 14TH STREET (23 line) 
| 14 ST-UNION SQ 
| 18 ST 
| 23 ST (6 line)
| 28 ST (1 line)
| 28 ST (CE line)
| 28 ST-5 AVE
| 28 ST-BROADWAY
| 33 ST
| 34 ST-HERALD SQ
|---
| **Brooklyn Tech Triangle**
| ---
| HIGH ST
| CLARK ST
| BOROUGH HALL/CT
| HOYT ST
| DEKALB AVE
| JAY ST-METROTEC
| HOYT/SCHERMER
| BARCLAYS CENTER
| NEVINS ST
| YORK ST

#### Processing the data

Due to computing limitations, I processed each station separately, having compiled all entries for a particular station from each week's data. (This is a really simple process of appending each entry that matches the station name and sometimes station line to a master station file.) Here's the code for generating daily station entry/exit totals for W 4th St:

~~~ python

import pandas as pd
import datetime

days = pd.read_csv('w4.csv', names=[
                   'day', 'time', 'entry', 'exit'], usecols=[6, 7, 9, 10])
# this dataframe looks like "4/12/15 00:00:00 23450 35939" but it's still grouped by turnstiles

# groups on day and time so all turnstile readings at that point are consecutive
dayintervals = days.groupby(['day', 'time'])

# sums turnstiles per date/time, giving station totals per day/time
dayinterval_totals = pd.DataFrame(dayintervals.sum())

# gives actual entries/exits per interval rather than cumulative count
running_diff = pd.DataFrame(dayinterval_totals.diff().fillna(0))

# sums up 00:00, 04:00, etc for total entry/exit per day
daily_totals = pd.DataFrame(running_diff.reset_index().groupby('day').sum())

# add column of summing entry and exit
daily_totals['total'] = daily_totals['entry'] + daily_totals['exit']

# dropping negatives
daily_totals = daily_totals.drop(daily_totals[daily_totals.total < 0].index)

# dropping outliers and undoing the "groupby" so the full index is restored
daily_totals = daily_totals.drop(daily_totals[daily_totals.total - daily_totals.total.median() > 150000].index).reset_index()

# add station label
daily_totals['station'] = 'w4'

# convert date in format 04/12/15 to weekday index and adds weekday column
daily_totals['day'] = pd.to_datetime(daily_totals['day'])
daily_totals['weekday'] = daily_totals['day'].dt.weekday

# create dataframe that groups all mondays, etc. and averages traffic
weekday_averages = pd.DataFrame(daily_totals.groupby('weekday').total.mean())

# add station label
weekday_averages['station'] = 'w4'

# writing to summary files for combination with other station totals
daily_totals.to_csv('univdaytotals.csv', header=False, mode='a')
weekday_averages.to_csv('univweekdayavg.csv', header=False,  mode='a')

~~~

Looking at the data, outliers were either negative or at least an order of magnitude larger than other figures, so I dropped any interval count (again, that would be, for example, 3500 entries between 12:00 and 16:00 at 59th St on 3/26/16) less than zero and for which the difference between the observation and the median number of daily entries for that station was greater than 150k (an arbitrary but effective threshold). In retrospect, I should have dropped the outliers at the individual turnstile/time level, but I didn't do so until I had summed all turnstiles per station per day, meaning that I dropped observations for whole station-days that were only problematic in a few time turnstile readings.

##### University findings

Again, the strategy for this nonprofit assumes that all stations near universities should be targeted due to the higher proportion of people in our demographic of interest using them. (This, of course, assumes that that likelihood is constant across university/station locations, which is probably not true.) Therefore, within the set of university stations, we want to find which stations have the highest traffic (measured by total entries and exits), and when that traffic passes through. 

This should allow the organization to say, we want to capture universities, but given the number of street teams that we have, it would be most effective to send people to stations near John Jay, NYU, and Baruch. A full ranked list allows the organization to choose their own cutoff for outreach targets based on their resources.

We also want to know when to send street teams. To that end, I found average station totals for each weekday for each station. It appears that general commuter patterns predominate even in university stations, with higher traffic on weekdays, peaking on Tuesdays and Wednesdays.

![weekdaytotalgraph](assets/weekdaytotalgraph.jpg)

Here's a map and a list of day of the highest traffic at each station:

![univ](assets/univ.jpg)

As you can see, there is a pretty sharp drop-off between the fourth busiest station and the fifth, so that might be a natural cutoff point for choosing university station targets.

#### Next: [Analysis](https://amn34.github.io/turnstylin-3)

