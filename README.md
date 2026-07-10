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

## Analysis & Key Findings:
1. Throughput & Capacity:
   Monday is the highest-volume day (3,813 visits) followed by Tuesday (3,543), with only minor variation across the remaining weekdays. June, March, May, and     April are the highest-volume months (~2,400-2,470 visits each), with a clear drop-off across the winter months (November, February at ~1,600-1,630).
   Conclusion: Demand is concentrated in the midday-to-evening window (11am-7pm), which together account for roughly half of all visits, and in the Monday-        Tuesday early week. Staffing schedules should be weighted toward these windows rather than spread evenly across all shifts; overnight (11pm-7am) volume is      roughly one-third of peak and may be a candidate for reduced staffing levels.

2.	Admit Rate — Overall and by Segment:
      Overall Admit Rate: 32.66%
     	Overall Discharge Rate: 67.34%
     	Admit rate held in a narrow band across the year (~29%-35%), dipping to its lowest in August (29.69%) and returning to ~32% by year-end — no strong             seasonal admission trend. Admit rate is nearly identical between genders.
      A clear, monotonic relationship exists between age and admit rate.
     	Conclusion: Admission risk is overwhelmingly driven by age rather than time of year or gender — elderly patients (81+) are admitted at roughly 10x the          rate of young adults.
  	
3. Case-Mix & Resource Intensity:
3.1 Top Chief Complaints by Volume:
   Complaint	Visits
Abdominal Pain	2,444
Other	2,183
Chest Pain	1,637
Shortness of Breath	1,320
Back Pain	1,079

Conclusion: Abdominal pain, chest pain, and shortness of breath — three of the top five complaints — all require broad diagnostic workup capability (labs, imaging). Staff training, equipment stocking, and any fast-track design should be built around this specific complaint mix rather than a generic assumption of ED case mix.

3.2 Average Medication Count per Visit:
Overall average: 2.67 medications per visit

Disposition	Avg. Medications
| Field | Description |
|---|---|
| Admit |	6.82 |
| Discharge |	0.66 |
By complaint (cross-filtered), the most medication-intensive presentations are:

| Field | Description |
|---|---|
| Complaint |	Avg. Medications |
| Shortness of Breath | 6.74 |
| Chest Pain | 4.05 |
| Dizziness |	3.64 |

Conclusion: Admitted patients receive roughly 10x the medication volume of discharged patients, confirming disposition is a strong proxy for clinical resource intensity. Respiratory and cardiac complaints (shortness of breath, chest pain) are the most pharmacy-intensive presentation types and should be prioritized in medication stocking and nursing workload planning.





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


