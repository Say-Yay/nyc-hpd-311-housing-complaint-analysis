# 03_eda_queries.sql  
### NYC HPD 311 Complaints — Exploratory Data Analysis (EDA)  
**Purpose:** Explore patterns, volume, and response behavior using SQL.

---

```sql

-- EDA Time! --

-- Total Rows 
SELECT COUNT(*) AS total_rows
FROM service_request;

-- Date Range 
SELECT 
    MIN(created_dt) AS earliest,
    MAX(created_dt) AS latest
FROM service_request;


-- Complaint count by year 
SELECT year,COUNT(*) AS total_requests
FROM service_request
GROUP BY year
ORDER BY year;

-- Complaint count by month
SELECT 
	year,
	month,
	COUNT(*) AS monthly_request
FROM service_request
GROUP BY year, month
ORDER by year, month;

-- Complaint Type 
SELECT
	"Complaint Type",
	COUNT(*) AS total
FROM service_request
GROUP BY "Complaint Type"
ORDER BY total DESC
LIMIT 20; 

-- Combining duplicate categories into 1 standardized type.
SELECT
	UPPER("Complaint Type") as standardized_type,
	COUNT(*) AS total
FROM service_request
GROUP BY standardized_type
ORDER BY total DESC;

-- Complaint Type by Month 
SELECT
    UPPER("Complaint Type") AS standardized_type,
	year,
    CASE month
        WHEN 1 THEN 'January'
        WHEN 2 THEN 'February'
        WHEN 3 THEN 'March'
        WHEN 4 THEN 'April'
        WHEN 5 THEN 'May'
        WHEN 6 THEN 'June'
        WHEN 7 THEN 'July'
        WHEN 8 THEN 'August'
        WHEN 9 THEN 'September'
        WHEN 10 THEN 'October'
        WHEN 11 THEN 'November'
        WHEN 12 THEN 'December'
    END AS month_name,
    COUNT(*) AS total
FROM service_request
GROUP BY standardized_type, month, year
ORDER BY standardized_type, month, year;

-- Complaint Type by Borough
SELECT 
    Borough,
    UPPER("Complaint Type") AS type,
    COUNT(*) AS total
FROM service_request
GROUP BY Borough, type
ORDER BY Borough, total DESC;

-- Complaint Type by Borough and Season
SELECT
	Borough,
	UPPER("Complaint Type") AS type,
	CASE month
        WHEN 1 THEN 'January'
        WHEN 2 THEN 'February'
        WHEN 3 THEN 'March'
        WHEN 4 THEN 'April'
        WHEN 5 THEN 'May'
        WHEN 6 THEN 'June'
        WHEN 7 THEN 'July'
        WHEN 8 THEN 'August'
        WHEN 9 THEN 'September'
        WHEN 10 THEN 'October'
        WHEN 11 THEN 'November'
        WHEN 12 THEN 'December'
    END AS month_name,
	COUNT(*) AS total
FROM service_request
GROUP BY Borough, type, month
ORDER BY Borough, type, month;

SELECT
	Borough,
	season,
	UPPER("Complaint Type") AS type,
	COUNT(*) AS total
FROM service_request
GROUP BY Borough, season, type
ORDER BY Borough, season, type;

-- Looking into Response Time --
SELECT
    AVG(response_hours) AS avg_hours,
    MIN(response_hours) AS fastest_case,
    MAX(response_hours) AS slowest_case,
    (
        SELECT response_hours
        FROM service_request
        WHERE response_hours IS NOT NULL
        ORDER BY response_hours
        LIMIT 1 OFFSET (
            (SELECT COUNT(*) FROM service_request WHERE response_hours IS NOT NULL) / 2
        )
    ) AS median_hours
FROM service_request;

-- Converting negative response times to NULL
UPDATE service_request
SET response_hours = NULL
WHERE response_hours < 0;

-- Rerunning mectric
SELECT
    AVG(response_hours) AS avg_hours,
    MIN(response_hours) AS fastest_case,
    MAX(response_hours) AS slowest_case,
    (
        SELECT response_hours
        FROM service_request
        WHERE response_hours IS NOT NULL
        ORDER BY response_hours
        LIMIT 1 OFFSET (
            (SELECT COUNT(*) FROM service_request WHERE response_hours IS NOT NULL) / 2
        )
    ) AS median_hours
FROM service_request;

-- Results from the previous query shows result (0.0 fastest closed case). Taking a deeper look.
SELECT
	"Created Date",
	"Closed Date",
	created_dt,
	closed_dt,
	response_hours
FROM service_request
WHERE response_hours = 0
LIMIT 20; 

-- Response Time by Borough (without median)
SELECT
    "Borough",
    AVG(response_hours) AS avg_response_hours,
    MIN(response_hours) AS fastest_case,
    MAX(response_hours) AS slowest_case,
    COUNT(*) AS total_cases
FROM service_request
WHERE response_hours IS NOT NULL
GROUP BY "Borough"
ORDER BY avg_response_hours DESC;

-- Median Response Time by Borough
WITH ordered AS (
    SELECT
        "Borough",
        response_hours,
        ROW_NUMBER() OVER (
            PARTITION BY "Borough"
            ORDER BY response_hours
        ) AS rn,
        COUNT(*) OVER (
            PARTITION BY "Borough"
        ) AS cnt
    FROM service_request
    WHERE response_hours IS NOT NULL
)
SELECT
    "Borough",
    response_hours AS median_response_hours
FROM ordered
WHERE rn = (cnt + 1) / 2
ORDER BY median_response_hours DESC;

-- Response Time by Complaint Type (Count)
SELECT
    UPPER("Complaint Type") AS type,
    COUNT(*) AS total_cases,
    COUNT(response_hours) AS cases_with_response_hours
FROM service_request
GROUP BY type
ORDER BY cases_with_response_hours DESC;

-- Response time by complaint type (avg, min, max response)
SELECT
	UPPER("Complaint Type") as type,
	AVG(response_hours) as avg_response_hours,
	MIN(response_hours) as fastest_case,
	MAX(response_hours) as slowest_case,
	COUNT(*) AS total_cases
FROM service_request
WHERE response_hours IS NOT NULL
GROUP BY type
ORDER BY avg_response_hours DESC; 

-- Median response time by complaint type --

-- Median HEAT/HOT WATER
SELECT response_hours
FROM service_request
WHERE UPPER("Complaint Type") = 'HEAT/HOT WATER'
	AND response_hours IS NOT NULL
ORDER BY response_hours
LIMIT 1 OFFSET(
	(SELECT COUNT(*)
	FROM service_request
	WHERE UPPER("Complaint Type") = 'HEAT/HOT WATER'
		AND response_hours IS NOT NULL) / 2
);

-- Median PLUMBING
SELECT response_hours
FROM service_request
WHERE UPPER("Complaint Type") = 'PLUMBING'
  AND response_hours IS NOT NULL
ORDER BY response_hours
LIMIT 1 OFFSET (
    (SELECT COUNT(*)
     FROM service_request
     WHERE UPPER("Complaint Type") = 'PLUMBING'
       AND response_hours IS NOT NULL) / 2
);

-- Median ELECTRIC
SELECT response_hours
FROM service_request
WHERE UPPER("Complaint Type") = 'ELECTRIC'
  AND response_hours IS NOT NULL
ORDER BY response_hours
LIMIT 1 OFFSET (
    (SELECT COUNT(*)
     FROM service_request
     WHERE UPPER("Complaint Type") = 'ELECTRIC'
       AND response_hours IS NOT NULL) / 2
);

-- Response time by season --

-- Response Time by Season (avg, min, max, count)
SELECT
    season,
    AVG(response_hours) AS avg_response_hours,
    MIN(response_hours) AS fastest_case,
    MAX(response_hours) AS slowest_case,
    COUNT(*) AS total_cases
FROM service_request
WHERE response_hours IS NOT NULL
GROUP BY season
ORDER BY avg_response_hours DESC;

-- Median Response Time by Season

-- Median Fall
SELECT response_hours
FROM service_request
WHERE season = 'Fall'
  AND response_hours IS NOT NULL
ORDER BY response_hours
LIMIT 1 OFFSET (
    (SELECT COUNT(*)
     FROM service_request
     WHERE season = 'Fall'
       AND response_hours IS NOT NULL) / 2
);

-- Median Winter
SELECT response_hours
FROM service_request
WHERE season = 'Winter'
  AND response_hours IS NOT NULL
ORDER BY response_hours
LIMIT 1 OFFSET (
    (SELECT COUNT(*)
     FROM service_request
     WHERE season = 'Winter'
       AND response_hours IS NOT NULL) / 2
);

-- Median Spring
SELECT response_hours
FROM service_request
WHERE season = 'Spring'
  AND response_hours IS NOT NULL
ORDER BY response_hours
LIMIT 1 OFFSET (
    (SELECT COUNT(*)
     FROM service_request
     WHERE season = 'Spring'
       AND response_hours IS NOT NULL) / 2
);

-- Median Summer
SELECT response_hours
FROM service_request
WHERE season = 'Summer'
  AND response_hours IS NOT NULL
ORDER BY response_hours
LIMIT 1 OFFSET (
    (SELECT COUNT(*)
     FROM service_request
     WHERE season = 'Summer'
       AND response_hours IS NOT NULL) / 2
);

-- Response Time by Month (avg, min, max, count)
SELECT
    month,
    AVG(response_hours) AS avg_response_hours,
    MIN(response_hours) AS fastest_case,
    MAX(response_hours) AS slowest_case,
    COUNT(*) AS total_cases
FROM service_request
WHERE response_hours IS NOT NULL
GROUP BY month
ORDER BY avg_response_hours DESC;

-- Median by Month 
WITH ordered AS (
    SELECT
        month,
        response_hours,
        ROW_NUMBER() OVER (PARTITION BY month ORDER BY response_hours) AS rn,
        COUNT(*) OVER (PARTITION BY month) AS cnt
    FROM service_request
    WHERE response_hours IS NOT NULL
)
SELECT
    month,
    response_hours AS median_response_hours
FROM ordered
WHERE rn = (cnt + 1) / 2
ORDER BY median_response_hours DESC;

-- Response Time By year --

-- Response Time by Year (avg, min, max, count)
SELECT
    year,
    AVG(response_hours) AS avg_response_hours,
    MIN(response_hours) AS fastest_case,
    MAX(response_hours) AS slowest_case,
    COUNT(*) AS total_cases
FROM service_request
WHERE response_hours IS NOT NULL
GROUP BY year
ORDER BY avg_response_hours DESC;

-- Median Response Time by year
WITH ordered AS (
    SELECT
        year,
        response_hours,
        ROW_NUMBER() OVER (PARTITION BY year ORDER BY response_hours) AS rn,
        COUNT(*) OVER (PARTITION BY year) AS cnt
    FROM service_request
    WHERE response_hours IS NOT NULL
)
SELECT
    year,
    response_hours AS median_response_hours
FROM ordered
WHERE rn = (cnt + 1) / 2
ORDER BY median_response_hours DESC;

-- Response Time by Hour of Day (avg, min, max, count)
SELECT	
	CAST(strftime('%H', created_dt) AS INTGER) AS hour_of_day,
	AVG(response_hours) AS avg_response_hours,
	MIN(response_hours) AS fastest_case,
	MAX(response_hours) AS slowest_case,
	COUNT(*) AS total_cases
FROM service_request
WHERE response_hours IS NOT NULL
GROUP BY hour_of_day
ORDER BY avg_response_hours DESC;

-- Median Response Time by Hour of Day
WITH ordered AS (
    SELECT
        CAST(strftime('%H', created_dt) AS INTEGER) AS hour_of_day,
        response_hours,
        ROW_NUMBER() OVER (PARTITION BY CAST(strftime('%H', created_dt) AS INTEGER)
                           ORDER BY response_hours) AS rn,
        COUNT(*) OVER (PARTITION BY CAST(strftime('%H', created_dt) AS INTEGER)) AS cnt
    FROM service_request
    WHERE response_hours IS NOT NULL
)
SELECT
    hour_of_day,
    response_hours AS median_response_hours
FROM ordered
WHERE rn = (cnt + 1) / 2
ORDER BY median_response_hours DESC;

-- Response Time By of Week (avg, min, max, count)
SELECT	
	CASE strftime('%w', created_dt)
		WHEN '0' THEN 'Sunday'
		WHEN '1' THEN 'Monday'
		WHEN '2' THEN 'Tuesday'
		WHEN '3' THEN 'Wednesday'
		WHEN '4' THEN 'Thursday'
		WHEN '5' THEN 'Friday'
		WHEN '6' THEN 'Saturday'
	END AS weekday,
	AVG(response_hours) AS avg_response_hours,
	MIN(response_hours) AS fastest_case,
	MAX(response_hours) AS slowest_case,
	COUNT(*) AS total_cases
FROM service_request
WHERE response_hours IS NOT NULL
GROUP BY weekday
ORDER BY avg_response_hours DESC; 

-- Median Response Time by Day of Week
WITH ordered AS (
    SELECT
        CASE strftime('%w', created_dt)
            WHEN '0' THEN 'Sunday'
            WHEN '1' THEN 'Monday'
            WHEN '2' THEN 'Tuesday'
            WHEN '3' THEN 'Wednesday'
            WHEN '4' THEN 'Thursday'
            WHEN '5' THEN 'Friday'
            WHEN '6' THEN 'Saturday'
        END AS weekday,
        response_hours,
        ROW_NUMBER() OVER (
            PARTITION BY strftime('%w', created_dt)
            ORDER BY response_hours
        ) AS rn,
        COUNT(*) OVER (
            PARTITION BY strftime('%w', created_dt)
        ) AS cnt
    FROM service_request
    WHERE response_hours IS NOT NULL
)
SELECT
    weekday,
    response_hours AS median_response_hours
FROM ordered
WHERE rn = (cnt + 1) / 2
ORDER BY median_response_hours DESC;

-- Case Volume by Borough
SELECT
	"Borough" AS borough,
	COUNT(*) AS total_cases,
	COUNT(response_hours) AS case_with_repsonse_hours
FROM service_request
GROUP BY borough 
ORDER BY total_cases DESC;

-- Case Volume by Complaint Type
SELECT 
    UPPER("Complaint Type") AS type,
    COUNT(*) AS total_cases,
    COUNT(response_hours) AS cases_with_response_hours
FROM service_request
GROUP BY type
ORDER BY total_cases DESC;

-- Case Volume by Facility Type
SELECT
    UPPER("Facility Type") AS facility_type,
    COUNT(*) AS total_cases,
    COUNT(response_hours) AS cases_with_response_hours
FROM service_request
GROUP BY facility_type
ORDER BY total_cases DESC;

-- How many rows have non-null Facility type
SELECT 
    COUNT(*) AS total_rows,
    SUM(CASE WHEN "Facility Type" IS NOT NULL THEN 1 ELSE 0 END) AS non_null_facility_rows
FROM service_request;

-- Case Volume by Season
SELECT 
    season,
    COUNT(*) AS total_cases,
    COUNT(response_hours) AS cases_with_response_hours
FROM service_request
GROUP BY season

ORDER BY total_cases DESC;
