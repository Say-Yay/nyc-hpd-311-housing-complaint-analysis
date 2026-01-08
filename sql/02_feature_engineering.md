-- =============================================================
-- 02_feature_engineering.sql
-- NYC HPD 311 Complaints — Feature Engineering
-- Purpose: Add engineered columns for EDA + Tableau
-- =============================================================

---------------------------------------------------------------
-- YEAR / MONTH / DAY
---------------------------------------------------------------

UPDATE service_request
SET year  = CAST(substr("Created Date", 7, 4) AS INTEGER),
    month = CAST(substr("Created Date", 1, 2) AS INTEGER),
    day   = CAST(substr("Created Date", 4, 2) AS INTEGER);


---------------------------------------------------------------
-- SEASON
---------------------------------------------------------------

UPDATE service_request
SET season =
    CASE 
        WHEN month IN (12,1,2) THEN 'Winter'
        WHEN month IN (3,4,5) THEN 'Spring'
        WHEN month IN (6,7,8) THEN 'Summer'
        WHEN month IN (9,10,11) THEN 'Fall'
    END;


---------------------------------------------------------------
-- DAY OF WEEK
---------------------------------------------------------------

UPDATE service_request
SET day_of_week = strftime('%w',
    substr("Created Date", 7,4) || '-' ||
    substr("Created Date", 1,2) || '-' ||
    substr("Created Date", 4,2)
);

UPDATE service_request
SET day_of_week =
    CASE day_of_week
        WHEN '0' THEN 'Sunday'
        WHEN '1' THEN 'Monday'
        WHEN '2' THEN 'Tuesday'
        WHEN '3' THEN 'Wednesday'
        WHEN '4' THEN 'Thursday'
        WHEN '5' THEN 'Friday'
        WHEN '6' THEN 'Saturday'
    END;

