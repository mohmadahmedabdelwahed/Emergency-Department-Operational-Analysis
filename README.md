# Emergency-Department-Operational-Analysis
An end-to-end Excel analytics project on 23,964 Emergency Department visits, built to demonstrate hands-on skill in **Power Query**, **Power Pivot / DAX**, and **data modeling**, paired with business-driven EDA and reporting.

## Objective:
Move beyond raw visit counts to answer operational questions an ED or hospital leadership team would actually act on: staffing timing, triage accuracy, resource intensity by complaint, payer mix, and documentation quality.

## Data:
Five related CSV extracts for a single ED, linked by `PatientID`:

| File | Description | Grain |
|---|---|---|
| `dept_b_admit.csv` | Visits ending in admission | 1 row per visit |
| `dept_b_discharge.csv` | Visits ending in discharge | 1 row per visit |
| `dept_b_triage_vitals.csv` | Vital signs, ESI, prior utilization counts | 1 row per visit |
| `dept_b_chief_complaint.csv` | Complaint flags | 1 row per complaint per visit |
| `dept_b_medications.csv` | Medication categories administered | 1 row per medication category per visit |

## Data Model:
Built in Power Pivot after shaping all source tables in Power Query.



- **Visits** (fact table) — `admit` + `discharge` appended into one 23,964-row table, related **1-to-1** with `triage_vitals`
- **dept_b_chief_complaint** and **dept_b_medications** — related **many-to-1** to `Visits`
- **Number_of_medications** — a Power Query–derived summary table (grouped by `PatientID`) merged back onto `Visits` to support the zero/single/multiple-medication segmentation, since visits with no medications don't exist as rows in the source medications table and require an explicit Left Outer merge to surface.


## Skills Demonstrated:
**Power Query**
- Appending tables (admit + discharge → unified fact table)
- Merge Queries (Left Outer joins to preserve all visits, including those with no matching medication/complaint rows)
- Group By (per-visit medication category counts)
- Custom columns with conditional (`if/then/else`) logic — age banding, visit-frequency banding, HR/vitals risk banding, 12-hour arrival interval labeling
- Text transformations (delimiter extraction, prefix removal on attribute/medication columns)

**Power Pivot / DAX**
- Relationship design or single (1-to-1) and many-to-1 relationships, including diagnosing and resolving cross-filter direction issues between two many-side tables (`chief_complaint` and `medications`, both only connected via `Visits`)
- `CALCULATE`, `DIVIDE`, `COUNTROWS`, `CROSSFILTER` measures
- Building rate/ratio measures with correct numerator-denominator grain matching


