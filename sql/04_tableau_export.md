-- =============================================================
-- 04_tableau_export.sql
-- NYC HPD 311 Complaints — Tableau Export Table
-- Purpose: Build a clean, filtered dataset for dashboards
-- =============================================================

DROP TABLE IF EXISTS clean_hpd_requests;

CREATE TABLE clean_hpd_requests AS
SELECT
    "Unique Key" AS unique_key,
    "Created Date" AS created_date_raw,
    "Closed Date" AS closed_date_raw,
    created_dt,
    closed_dt,
    year,
    month,
    day,
    day_of_week,
    season,
    UPPER("Complaint Type") AS complaint_type,
    UPPER("Descriptor") AS descriptor,
    UPPER("Borough") AS borough,
    "City" AS city,
    "Incident Zip" AS incident_zip,
    "Latitude",
    "Longitude",
    response_hours
FROM service_request
WHERE "Agency" = 'HPD'
  AND UPPER("Complaint Type") IN ('HEAT/HOT WATER','PLUMBING','ELECTRIC')
  AND response_hours IS NOT NULL;

SELECT COUNT(*) FROM clean_hpd_requests;
