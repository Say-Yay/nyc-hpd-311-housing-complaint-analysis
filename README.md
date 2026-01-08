**NYC 311 HPD Housing Complaints -- Data Analysis & Insights Dashboard**
=======================================================================

### *A data-driven look into housing conditions, seasonal demand, and operational performance across NYC (2023--2025)*

**Author:** *Sayeh Nelson*

* * * * *

**Project Overview**
------------------------

This project analyzes **NYC 311 HPD (Housing Preservation & Development)** complaints from **2023--2025** to uncover patterns in housing conditions, operational performance, and resident needs. Using **SQL, Excel, Tableau, and EDA**, I explored:

-   What types of housing issues are most common

-   When demand is highest

-   Which boroughs experience the greatest workload

-   How response times vary by season, complaint type, and more

-   Where operational bottlenecks exist

The final result is a **multi-dashboard Tableau experience** designed to present a clear, actionable view of NYC housing challenges.

**Dataset Description**
=======================

**Source:** NYC OpenData --- 311 Service Requests (Filtered for HPD complaints)\
**Rows After Cleaning:** ~878,000\
**Years Covered:** 2023, 2024, partial 2025\
**Target Columns Engineered:**

-   `response_hours` (closed_dt -- created_dt)

-   `season`

-   `year`, `month`, `day`, `day_of_week`

-   `Latitude`, `Longitude`

-   Flags: **resolved < 48 hours**

Cleaning steps included:

✔ Handling null/invalid timestamps\
✔ Creating consistent datetime formats\
✔ Removing unresolved/duplicate complaint keys\
✔ Standardizing categorical values (Complaint Type, Borough)

**Key Insights**
================

### **1\. Complaint Volume**

-   HPD receives the **highest volume in Winter**, driven by heating issues.

-   **Heat/Hot Water** complaints account for **~74%** of all cases.

-   Bronx consistently shows the **highest density of complaints**.

* * * * *

### **2\. Response Time Performance**

-   **Average response time:** ~178 hours (~7.4 days)

-   **Median response time:** ~47 hours

-   **51% of cases are resolved within 48 hours**

-   Staten Island has the **longest average delays**, while Queens is faster.

* * * * *

### **3\. Operational Patterns**

-   **Peak delay hour:** 13:00--15:00

-   **Fastest response hours:** early morning (1--5 AM)

-   **Day-of-week trend:** Friday sees the slowest responses.

* * * * *

### **4\. Geographic Patterns**

-   Complaint hotspots cluster in:

    -   **Upper Manhattan**

    -   **North Bronx**

    -   **Brooklyn Central**

-   Latitude/longitude mapping reveals clear seasonal shifts.

* * * * *

Tableau Dashboards
======================

**Tableau Public Dashboard:** https://public.tableau.com/app/profile/sayeh.nelson/viz/NYC-HPD-Dashboard/Dashboard1?publish=yes

* * * * *

** Dashboard 1 --- NYC HPD Complaint Overview**
-----------------------------------------------

KPIs + geospatial view of complaint hotspots.

**Screenshot:**
![Dashboard 1](images/dashboard_screenshots/dashboard-1.png)


* * * * *

** Dashboard 2 and 3 --- Complaint Seasonality + Monthly Trends + Response Time**
----------------------------------------------

Deep dive into HPD efficiency across seasons, boroughs, and time.

**Screenshot:**
![Dashboard 2](images/dashboard_screenshots/dashboard-2.png)

**Screenshot:**
![Dashboard 3](images/dashboard_screenshots/dashboard-3.png)


* * * * *

** Dashboard 4 --- Interactive Explorer**
-----------------------------------------

User-driven dashboard with ZIP code filtering, type breakdowns, and parameter-based views.

**Screenshot:**
![Dashboard 4](images/dashboard_screenshots/dashboard-4.png)


* * * * *

**Business & Operational Recommendations**
==========================================

### **1\. Winter Staffing Surge**

Since winter volume spikes, HPD should temporarily increase field staff during December--February.

### **2\. Priority Routing for High-Delay ZIP Codes**

ZIP codes with 20k--30k+ complaints need:

-   Additional inspectors

-   Faster routing

-   Localized operations units

### **3\. Predictive Workload Planning**

Based on seasonality + hour-of-day patterns, HPD could forecast:

-   Inspector demand

-   Expected case backlog

-   Resource allocation needs

### **4\. Proactive Outreach for Heat/Hot Water Issues**

Given these make up 70%+ of complaints, targeted building inspections can reduce complaint load.

**About Me**
============

I'm **Sayeh Nelson**, a data analyst focused on blending analytics with real-world impact.\
This project reflects my ability to:

-   Clean and transform large datasets

-   Derive meaningful insights from messy data

-   Build polished, stakeholder-ready dashboards

-   Communicate findings clearly

If you'd like to connect:\
**LinkedIn:** www.linkedin.com/in/sayeh-nelson-814941349
**GitHub:** https://github.com/Say-Yay