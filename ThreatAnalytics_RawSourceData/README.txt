THREAT INTEL FRAMEWORK — RAW SOURCE DATA
Milestone 1: Data Integration & Incident Modeling (Weeks 1-2)
================================================================

These are 5 files, one per source system, exactly as they'd land
before any integration work. Nothing here has been cleaned, joined,
or standardized yet — that's your Milestone 1 task. Each file has
its own quirks, on purpose, since that's what real source systems
look like.

1. incident_logs.csv   (2,600 rows)  — internal incident management system
   - event_time_raw: THREE different date formats mixed together
     ("2026-07-23 15:41", "07/23/2026 15:41", "23-Jul-2026 15:41")
   - incident_type_raw: same 5 categories, written inconsistently
     ("intrusion detected", "INTRUSION", "Intrusion Attempt", etc.)
   - location_raw: some blank/missing
   - severity_raw: mixed scales — words (Low/Medium/High/Critical)
     AND raw numbers (3, 4), plus some blanks

2. operational_data.csv   (2,100 rows)  — dispatch / response system
   - No direct foreign key to incident_logs — link by nearest
     zone_code + timestamp, the way two real systems usually do
   - dispatch_time / arrival_time → use these to compute response
     time in minutes
   - zone_code uses the short code (A-J), not the full zone name

3. environmental_feeds.csv   (1,850 rows)  — sensor network, ~87% complete
   - risk_factor_code is a raw code (EF-01 … EF-08); look for a
     cluster of EF-07 readings with anomaly_flag = True — that's
     the anomaly this milestone's dashboard should surface

4. external_threat_feed.csv   (1,600 rows)  — third-party API, ~72% complete
   - The messiest file on purpose (mirrors a lower ingestion %):
     threat_category_raw has mixed casing, abbreviations, and blanks
   - geo_hint is inconsistent — sometimes a zone name, sometimes a
     region name, sometimes blank

5. geo_metadata.csv   (10 rows)  — clean reference table, 100% complete
   - Use this to resolve zone_code → zone_name → region → risk_tier
   - This is your Dim_Geo dimension almost as-is

WHAT MILESTONE 1 ASKS YOU TO DO WITH THIS
------------------------------------------
1. Standardize incident_type_raw / threat_category_raw into one
   consistent set of categories: Intrusion, Physical, Cyber,
   Environmental, Other.
2. Parse all date formats into one consistent date/time field, and
   build a Dim_Time calendar table from it.
3. Resolve every location reference (zone name, zone code, region
   name) down to a single zone_code, joined against geo_metadata.
4. Design a star schema: one Fact_Incident table (grain = one row
   per incident) with foreign keys to Dim_Time, Dim_ThreatType,
   Dim_Geo, Dim_Source (and Dim_Operational if you bring in the
   response data).
5. Calculate a baseline Threat Severity Index (TSI) — e.g. map
   severity_raw onto a 0-100 scale, or blend severity with
   confidence_score from the external feed.
6. Track ingestion completeness per source (this is what the
   "ETL Pipeline Status" panel on the Milestone 1 dashboard shows —
   incident_logs and operational_data should land near 100%,
   environmental_feeds near 87%, external_threat_feed near 72%).

Once you've built the fact + dimension tables, they can be loaded
straight into Power BI (or Excel/SQL) to reproduce the Milestone 1
dashboard and data model.
