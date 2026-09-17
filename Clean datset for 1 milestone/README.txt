THREAT INTEL FRAMEWORK — CLEAN STAR-SCHEMA DATASET
Ready to load directly into Power BI
====================================================

6 CSV files, already cleaned and modeled — one fact table, five dimensions.

FILES
-----
Fact_Incident.csv       6,000 rows — one row per incident, with foreign keys
                         to every dimension below, plus severity_score and tsi
Dim_ThreatType.csv      5 rows  — Intrusion, Physical, Cyber, Environmental, Other
Dim_Time.csv            90 rows — daily calendar table
Dim_Geo.csv             10 rows — zones A-J, grouped into 5 regions
Dim_Operational.csv     8 rows  — response units, shift, asset criticality
Dim_Source.csv          5 rows  — the 5 source systems + ETL completion %

LOAD INTO POWER BI
------------------
1. Power BI Desktop → Get Data → Text/CSV (or Folder, to grab all 6 at once)
   → select each file → Load.
2. Go to Model view. Draw these 5 relationships (drag key field from
   Fact_Incident to the matching dimension), all one-to-many,
   single direction, dimension → fact:

     Fact_Incident[date_key]         → Dim_Time[date_key]
     Fact_Incident[threat_type_key]  → Dim_ThreatType[threat_type_key]
     Fact_Incident[geo_key]          → Dim_Geo[geo_key]
     Fact_Incident[operational_key]  → Dim_Operational[operational_key]
     Fact_Incident[source_key]       → Dim_Source[source_key]

3. Add these DAX measures (New Measure, on Fact_Incident):

     Total Incidents      := COUNTROWS(Fact_Incident)
     Avg TSI               := AVERAGE(Fact_Incident[tsi])
     Avg Response Time     := AVERAGE(Fact_Incident[response_time_minutes])
     Escalated Incidents   := CALCULATE([Total Incidents], Fact_Incident[status] = "Escalated")
     Sources Connected     := DISTINCTCOUNT(Fact_Incident[source_key])
     Avg Schema Validation %:= AVERAGE(Dim_Source[ingestion_status_pct])

4. Build the report page:
     • Card visuals — Total Incidents, Avg TSI, Avg Response Time, Sources Connected
     • Line chart — incidents over time (Dim_Time[full_date])
     • Bar chart — Avg Response Time by Dim_Geo[zone] (watch for Zone D)
     • Donut chart — incidents by Dim_ThreatType[threat_type_name]
     • Table — Dim_Source[source_name] vs ingestion_status_pct

This reproduces the Milestone 1 dashboard (star schema + ETL status + KPIs)
as a real, working Power BI report.
