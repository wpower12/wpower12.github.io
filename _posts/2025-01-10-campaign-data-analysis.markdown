---
layout: post
title:  "Campaign Data Analysis"
date:   2025-01-10 12:07:56 -0500
categories: data-demo
---
In 2022, I was lucky to get a chance to contribute to a political race in my district. With the political climate the way it is, I was excited to get a chance to help the State Representative from my district here in Montgomery County PA in their 2022 election. In this election, the democratic candidate Missy Cerato was able to unseat the long-serving republican incumbent Todd Stephans. 

This race was interesting for a few reasons. First, the win by Rep. Cerrato provided the dead-lock breaking majority needed for the democrats in the PA House. Second, it was the first election to occur after a major redistricting effort re-allocated the voting precincts amongst the house districts. 

![Overview](/assets/overview.png)

In this document, I’d like to give an overview of the three areas where I was able to help the team. 
1. **Calculating a Win Number**
  - Using historic turnout and return data to estimate the votes needed to win.
2. **Analysing Split Ballots**
  - Estimating how often voters switch parties for down-ballot candidates.
3. **Displaying Information Geographically**
  - How to display precinct-level data using geographic shapefiles.


## Calculating a Win Number
To run an election campaign, you need to get resources, and spend those resources. Ideally, you spend these resources in ways that get you votes on election day. To figure out how to efficiently spend these resources, you need to know an estimate of the likely number of votes needed. The win number. 

Typically, this is a straight forward calculation. Estimate the expected voter turnout for your voting base, then mulitply that by 0.5 and add 1. Yay, we have a number! However, this becomes slightly more complicated as you consider all the ways one might estimate the expected turnout. Additionally, this gets fuzzier if the district in question recently lost some precincts and gained others. Which is the case here.

For the 151st 2022 election, the main sources of data came from the election results books provided by the county. A full list of these can be found [here](https://www.montgomerycountypa.gov/767/Archived-Elections). Additionally, we need a list of the voting precincts associated with the 151st district. Here is our first source of data-diving. Before the 2022 election, the district-precinct mapping follows a listing found [here](https://www.montgomerycountypa.gov/DocumentCenter/View/6425/Voting-Precinct-Detail-List). However, these changed during the 2020 redistricting effort, resulting in changes that are outlined in the states redistricting plan, [here](https://www.pa.gov/agencies/vote/resources/redistricting/pennsylvania-redistricting-house-of-representative.html). 

With a set of historic turnouts in hand, estimates can be made for the turnouts across the two elections. Summing these values, and multiplying by 1/2 and adding 1 gave us our number. In reality, this estimate is used to help break down the estimated impact of various outreach and persuassion efforts within each major group of precincts. Ultimatly, this led to the creation of a 'Field Plan' spreadsheet, which can take in an estimate of a win number, and estimates of the votes we expect to gain from an area based on the persuasion plans. Given these, we can see how far off a given locale is from our expectations, and change resources accordingly. A link to a view-only copy of this Field Plan can be found [here](https://docs.google.com/spreadsheets/d/1Drpxe-zdHvdmzxG-aQm4Mde0dmmtIUGKV4m0oTYoqSs/edit?usp=sharing)

![Field Plan Overview](/assets/field_plan_00.png)

## Analysing Split Ballots
A split vote is when a voter does not vote for a single party on the entire ticket. This can occur for a lot of reasons, and is a useful thing to keep track of. Whether this effect is due to the psychology of voting in an off year, or due to the nature of an individual candidate, it is a useful metric to keep track of when assigining resources during a campaign. 

Tracking the actual number of split ballots is not possible, so instead, the differences in vote shares between different races on the ballot can be used as a proxy for the amount of split-ballots. As an example, consider an election consisting of two races, between two parties. For each race, a voter selects a candidate from one of these two parties. If we know the amount of votes for the national candidate in both parties; $$N_a, N_b$$, and the local returns, $$L_a, L_b$$, the ratios $$N_a/L_b$$ and $$N_b/L_a$$ tell us about the amount of vote splitting occuring in the election. When this ratio is far from 1, we know there have been a good amount of split votes. 

When we do this calculation for our historical data, we can get an idea of the amount of support the local candidate has above and beyond their corresponding national level candidate. In the 151st, this information is summarized in the Precinct-Level margins. Looking at these values, the campaign manager can make more choices about where to send what resources. 

![Historic Split Vote Margins](/assets/margins_00.png)

## Geographic Presentation
To make this data easier to interpret, it'd be nice to display these types of margins overtop their actual geographic locations. Maps of this type, the [choropleth map](https://en.wikipedia.org/wiki/Choropleth_map), require shape information to help correctly draw the areas of interest.  To gather these files, we start with the shapefiles curated by the US Census dept. The Census maintains [shapefiles](https://www.census.gov/geographies/mapping-files/time-series/geo/tiger-line-file.2020.html#list-tab-790442341) for all 'voting areas', with the most recent data being from 2020. However, these lack the necessary association with a State House District. To remedy this, the known 2022 precinct names were used to manually filter down to the geometries for the 'current' 151st district. 

With that in hand, the [geopandas](https://automating-gis-processes.github.io/CSC/notebooks/L2/geopandas-basics.html) python library was used to ingest the shapefile geometries, associate it with the correct precints, and add in the data of interest. The result is a pipeline that lets us easily produce maps that show the margins and meta margins. The example below shows such a meta margin map, highlighting the areas where the local candidate (Kassa) had splitting (in either direction) with respect to the national candidate (Trump), allowing us to see what precincts might need more persuassion to help them split from the republicans.  

![Kassa Trumpe Meta Margins by Precinct](/assets/k-t_top.png)
