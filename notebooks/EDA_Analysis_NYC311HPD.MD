# 🌆 NYC 311 HPD Complaints – Exploratory Data Analysis
Time Range: 2023–2025
Author: Sayeh Nelson
---
## 1. Project Overview
### Objective

This analysis explores NYC HPD (Housing Preservation & Development) complaints to understand how service demand changes across time, boroughs, complaint types, and seasons. The goal is to uncover patterns that help explain workload surges, operational slowdowns, and where the city may need more resources.

### Dataset Summary
- Source: NYC OpenData – 311 Service Requests
- Main fields: Created Date, Complaint Type, Descriptor, Borough, Status
- Engineered fields: year, month, day, day_of_week, season, response_hours

This dataset gives a wide view of how NYC’s housing infrastructure performs and how residents rely on 311 services.
---
## 2. Data Checks & Initial Validation

Before diving deeper, I made sure the dataset was complete, valid, and covered the right time periods.
---
### 2.1 Row Count

Total rows: 886,778

**Observation**
This is a big dataset have almost 900k service requests across 2.5 years. Plenty of volume to analyze trends and patterns.
---
### 2.2 Date Range
- Earliest: 2023-01-01
- Latest: 2025-08-31

**Observation**
We’re working with almost three full years of data. That gives us enough time to truly evaluate trends, seasonality, and operational changes.
---
### 2.3 Complaint Volume by Year

| Year     | Total Requests             |
| -------- | -------------------------- |
| **2023** | 322,330                    |
| **2024** | 358,813                    |
| **2025** | 205,635 (through Aug only) |

**Interpretation**
Complaint volume grew from 2023 to 2024 (+11%), which could mean:
- colder weather
- more reporting
- aging buildings
- stronger enforcement

2025 looks lower only because the year is incomplete. Based on monthly pace, 2025 would likely land around 310k–320k, keeping it consistent with previous years.
---
### Complaint Volume by Month (Seasonality)

| Complaint Type     | Total |
| ------------------ | ----- |
| **Heat/Hot Water** | ~656k |
| **Plumbing**       | ~160k |
| **Electric**       | ~70k  |

**Interpretation**
Heat/Hot Water complaints dominate completely which makes sense. NYC’s heating systems are old, highly regulated, and heavily used.

Plumbing comes second, and Electric is a distant third.

This breakdown instantly shows where NYC buildings struggle the most and where HPD spends most of its time: heating.
---
### 2.6 Complaint Type by Month / Season

My take:
- Heat/Hot Water: Huge winter spikes
- Plumbing: Slight winter increase
- Electric: No major seasonal change

This reinforces that heating systems are NYC’s biggest vulnerability in cold months. Electric systems are the most stable.
---
### 2.7 Complaint Type by Borough

Key pattern: The Bronx leads in complaints across all types.

| Borough           | Total |
| ----------------- | ----- |
| **Bronx**         | 317k  |
| **Brooklyn**      | 238k  |
| **Manhattan**     | 200k  |
| **Queens**        | 118k  |
| **Staten Island** | 12k   |

**Interpretation**
- The Bronx has the highest volume because it has older buildings, more multi-unit housing, and historically underfunded infrastructure.
- Brooklyn and Manhattan follow, consistent with population size and density.
- Staten Island is extremely low — newer homes, more space, and fewer multi-family units.

This is a clear signal of housing inequality across boroughs.
---
### 2.8 Complaint Type by Borough + Season

**Interpretation**
- Winter hits every borough, but the Bronx suffers the most.
- Plumbing also rises slightly in colder months.
- Electric stays steady year-round.

This is a perfect example of how environment + infrastructure + demographics shape service demand.
---
## 3. Response Time Analysis
This is where the analysis shifts from what complaints happen to how fast they get resolved.
---
### 3.0 Overall Response Time

**Summary:**
- Average: ~178 hours (7.4 days)
- Median: ~47 hours (≈ 2 days)
- Fastest: 0 hours
- Slowest: ~23,000 hours (≈ 2.6 years)

**Interpretaion**
The huge gap between mean and median tells us the distribution is very skewed.
Most cases are handled quickly (2 days), but a small number take months or years, dragging the average upward.

We have 0-hour cases. These are valid because they represent duplicates, admin closures, or instant resolutions.
---
### 3.1 Response Time by Borough (Average + Median)

#### Average Response Time Ranking (Slowest to Fastest)
1. Unspecified
2. Staten Island
3. Manhattan
4. Brooklyn
5. Bronx
6. Queens

#### Median Response Time Ranking (Slowest to Fastest)
1. Unspecified
2. Staten Island
3. Manhattan
4. Brooklyn
5. Queens
6. Bronx

**Interpretation**
- Bronx: Surprisingly fast median despite highest workload. Likely heavy prioritization + streamlined processes.
- Queens: Consistently fast across metrics.
- Staten Island: Slowest median Geography and staffing probably plays a role.
- Unspecified: Delays caused by incomplete or messy data.

This suggests borough performance isn’t only about volume. Routing, building types, and resource allocation play major roles.
---
### 3.2 Response Time by Complaint Type

| Season     | Avg (hrs) | Median (hrs) |
| ---------- | --------- | ------------ |
| **Summer** | 308       | 103          |
| **Spring** | 185       | 42           |
| **Fall**   | 175       | 49           |
| **Winter** | 154       | 46           |

**Interpretaion**
Heat/Hot Water is treated as an emergency, so HPD moves fast. Plumbing and Electric take significantly longer (over a week on average), and the long-tail cases are extreme.

The fact that Heat/Hot Water has both the highest volume and the fastest response time shows the city has structured these workflows well.
---
### 3.3 Response Time by Season

| Season     | Avg (hrs) | Median (hrs) |
| ---------- | --------- | ------------ |
| **Summer** | 308       | 103          |
| **Spring** | 185       | 42           |
| **Fall**   | 175       | 49           |
| **Winter** | 154       | 46           |

**Interpretation**
Summer is surprisingly the slowest season.
Possibly because:
- staffing drops (vacations)
- plumbing/electric issues pile up
- less urgency than winter heating issues

Winter is the most efficient season even though the volume is highest. Again this shows urgency and strong workflows around heating.
---
### 3.3 Response Time by Hour of Day

**Interpretation**
- Slowest hours: 11 AM – 4 PM → peak complaint volume, peak operational load.
- Fastest hours: Early morning (3–7 AM) → lower volume, cleaner workflows.
- The median is much lower than the mean, meaning outliers heavily skew averages.
---
### Case Volume Summary

#### By Borough
- Bronx dominates (317k cases)
- Brooklyn and Manhattan follow
- Queens moderate
- Staten Island extremely low

#### By Complaint Type
- Heat/Hot Water = 65% of all cases
- Plumbing = 18%
- Electric = 8%

#### By Season
- Winter = heaviest workload
- Summer = lowest workload
---
### Facility Type Analysis
All rows contain NULLs → this field cannot be used in segmentation.
Good to document this for transparency.
---
### Final Thoughts
This EDA reveals clear patterns in how NYC residents experience housing issues and how HPD responds to them. Heat/Hot Water drives most of the demand, especially in winter, but those cases are also resolved the fastest. Plumbing and Electric are handled much slower and show extreme outliers that pull averages upward.

Geographically, the Bronx experiences the highest volume but surprisingly fast median responses, while Staten Island tends to lag behind.

Across all dimensions such as seasons, hours, days, boroughs, and complaint types, the patterns are consistent and meaningful. These insights set a strong foundation for building an operational dashboard, staffing recommendations, and eventually a predictive model.