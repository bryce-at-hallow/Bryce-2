---
name: parish-data-pull
description: Pulls Necessary Data Points for Omni and creates two separate CSVs
---

# Goal
Pull data from two distinctive dashboards in OMNI and create two CSV files.

## CSV Files to be Created 
[CommunityID]_CommunityMetrics.csv
[CommunityID]_ParishCommunityChallenges.csv

## Dashboard from Omni
*Stored under B2B & Parishes*
1. Community Metrics
2. Parish Community Challenges 

## Necessary Arguments 
- **Community ID** - This is the key value for searching across all Omni Dashboards that is tied directly to a parish. 


### Community Metrics CSV
Referencing Community Metric Dashbard

**Instructions**
1. Locate Entries Based on the Community ID and by the last 6 months. You should 
only get one entry
2. Pull These Data Entries 
 - Num Prayers Started Sum 
 - Num Prayers Completed Sum
 - Average Daily Users Praying 
 - Num Intentions Posted Sum
 - Average Daily Users Posting


### Parish Community Challenges 
Referencing Parish Community Challenges 

**Instructions**
1. Locate Entries Based on the Community ID and by the last 6 months. You may get multiple entries. 
2. Pull These Data Entries 
 - Start Date 
 - Num Users Joined Sum 
 - Num Users Participated Sum 
 - Num Users Participated in [X] Pct Sum
  - Should be in quarters 25, 50, etc. pull all these values 
 - Num Completed Challenge Sum

