-- =============================================================
-- 01_data_cleaning.sql
-- NYC HPD 311 Complaints — Data Cleaning & Preparation
-- Author: Sayeh Nelson
-- Purpose: Convert raw HPD dataset into clean, structured format
-- =============================================================

---------------------------------------------------------------
-- FIXING CREATED DATE (Convert to ISO format)
---------------------------------------------------------------

ALTER TABLE service_request
ADD COLUMN created_dt TEXT;

UPDATE service_request
SET created_dt =
    substr("Created Date", 7, 4) || '-' ||   -- YYYY
    substr("Created Date", 1, 2) || '-' ||   -- MM
    substr("Created Date", 4, 2) || 'T' ||   -- DD
    CASE
        WHEN substr("Created Date", -2) = 'PM'
             AND substr("Created Date", 12, 2) != '12'
            THEN printf('%02d', substr("Created Date", 12, 2) + 12)
        WHEN substr("Created Date", -2) = 'AM'
             AND substr("Created Date", 12, 2) = '12'
            THEN '00'
        ELSE substr("Created Date", 12, 2)
    END || ':' ||
    substr("Created Date", 15, 2) || ':' ||
    substr("Created Date", 18, 2);

SELECT MIN(created_dt) AS earliest,
       MAX(created_dt) AS latest
FROM service_request;


---------------------------------------------------------------
-- FIXING CLOSED DATE (Convert to ISO format)
---------------------------------------------------------------

ALTER TABLE service_request
ADD COLUMN closed_dt TEXT;

UPDATE service_request
SET closed_dt =
    substr("Closed Date", 7, 4) || '-' || 
    substr("Closed Date", 1, 2) || '-' ||
    substr("Closed Date", 4, 2) || 'T' ||
    CASE
        WHEN substr("Closed Date", -2) = 'PM'
             AND substr("Closed Date", 12, 2) != '12'
            THEN printf('%02d', substr("Closed Date", 12, 2) + 12)
        WHEN substr("Closed Date", -2) = 'AM'
             AND substr("Closed Date", 12, 2) = '12'
            THEN '00'
        ELSE substr("Closed Date", 12, 2)
    END || ':' ||
    substr("Closed Date", 15, 2) || ':' ||
    substr("Closed Date", 18, 2);

SELECT MIN(closed_dt),
       MAX(closed_dt)
FROM service_request;


---------------------------------------------------------------
-- CALCULATE RESPONSE HOURS
---------------------------------------------------------------

ALTER TABLE service_request
ADD COLUMN response_hours REAL;

UPDATE service_request
SET response_hours =
    (julianday(closed_dt) - julianday(created_dt)) * 24;

-- Validate
SELECT "Created Date", "Closed Date", created_dt, closed_dt, response_hours
FROM service_request
LIMIT 20;


---------------------------------------------------------------
-- REMOVE NEGATIVE RESPONSE TIMES
---------------------------------------------------------------

UPDATE service_request
SET response_hours = NULL
WHERE response_hours < 0;

