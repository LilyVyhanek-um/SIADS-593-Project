# SIADS-593-Project
Nurse Staffing Levels and Hospital Quality: Linking Workforce Capacity to Patient Outcomes and Experience
Project Overview
Examining whether hospital staffing levels are associated with quality outcomes (mortality, readmissions, safety). Combines CMS cost report data with hospital quality ratings and BLS workforce data.

Data Sources
1. CMS_HospitalProviderCostReport (Primary)
Source: https://data.cms.gov/provider-compliance/cost-report/hospital-provider-cost-report
Key fields:

Provider CCN — join key to quality data
FTE - Employees on Payroll — staffing measure (NOTE: all employees, not just nurses)
Total Days (V + XVIII + XIX + Unknown) — patient days for ratio calculation
Number of Beds — alternative denominator
Medicare CBSA Number — for BLS linkage

Caveats:

FTE includes everyone (admin, housekeeping, dietary) — it's a proxy, not nursing-specific
Data lags 12-18 months from fiscal year end
Fiscal years vary by hospital (not all calendar year)


2. CMS_HospitalRatings (Secondary)
Source: https://data.cms.gov/provider-data/dataset/xubh-q36u
Key fields:

Facility ID — join key (= Provider CCN)
Hospital overall rating — 1-5 stars
Count of MORT/Safety/READM Measures Better/No Different/Worse — detailed outcomes

Star Rating breakdown:

5 measure groups: Mortality (22%), Safety (22%), Readmission (22%), Patient Experience (22%), Timely & Effective Care (12%)
Must have 3+ groups with 3+ measures each to get a rating
Hospitals compared within peer groups (based on how many measure groups they report)

Caveats:

Not all hospitals get a rating — check footnote columns
COVID years (2020-2022) may affect underlying data
Methodology changed in 2021 (peer grouping added)


3. BLS_WorkforceData (Secondary)
Source: https://www.bls.gov/oes/tables.htm
Key fields:

AREA — MSA or state FIPS code
OCC_CODE — use 29-1141 for Registered Nurses
TOT_EMP — employment count
A_MEAN — mean annual wage
JOBS_1000 — RNs per 1,000 jobs in area

Caveats:

Lots of suppressed data — small/rural areas often missing
Symbols: * = suppressed, ** = wage unavailable, blank = no data
State-level more complete than MSA-level
3-year rolling average, so it's lagged
Colorado 2024 data has known quality issues


Join Keys
FromToKeyCostReportRatingsProvider CCN = Facility IDCostReportBLSMedicare CBSA Number = AREA (or use State)

Calculated Metrics
python# Staffing intensity
df['fte_per_1000_days'] = (df['FTE - Employees on Payroll'] / df['Total Days (V + XVIII + XIX + Unknown)']) * 1000

df['fte_per_bed'] = df['FTE - Employees on Payroll'] / df['Number of Beds']

Notes to Self

 Decide: MSA-level BLS join (precise but missing) vs state-level (complete but coarse)
 Filter to acute care hospitals only? Or include CAHs?
 Check fiscal year alignment between cost report and quality ratings
 Document how many hospitals drop due to missing star ratings
