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
> **Conclusion:** Demand is concentrated in the midday-to-evening window (11am-7pm), which together account for roughly half of all visits, and in the Monday-        Tuesday early week. Staffing schedules should be weighted toward these windows rather than spread evenly across all shifts; overnight (11pm-7am) volume is      roughly one-third of peak and may be a candidate for reduced staffing levels.

2.	Admit Rate — Overall and by Segment:
Overall Admit Rate: 32.66%
Overall Discharge Rate: 67.34%
Admit rate held in a narrow band across the year (~29%-35%), dipping to its lowest in August (29.69%) and returning to ~32% by year-end — no strong             seasonal admission trend. Admit rate is nearly identical between genders.
A clear, monotonic relationship exists between age and admit rate.
> **Conclusion:** Admission risk is overwhelmingly driven by age rather than time of year or gender — elderly patients (81+) are admitted at roughly 10x the          rate of young adults.
  	
3. Case-Mix & Resource Intensity:

3.1 Top Chief Complaints by Volume:
| Complaint |	Visits |
|---|---|
| Abdominal Pain |	2,444 |
| Other |	2,183 |
| Chest Pain |	1,637 |
| Shortness of Breath |	1,320 |
| Back Pain |	1,079 |

> **Conclusion:** Abdominal pain, chest pain, and shortness of breath — three of the top five complaints — all require broad diagnostic workup capability (labs, imaging). Staff training, equipment stocking, and any fast-track design should be built around this specific complaint mix rather than a generic assumption of ED case mix.


3.2 Average Medication Count per Visit:
Overall average: 2.67 medications per visit

| Disposition | Avg. Medications |
|---|---|
| Admit |	6.82 |
| Discharge |	0.66 |

By complaint (cross-filtered), the most medication-intensive presentations are:

| Complaint |	Avg. Medications |
|---|---|
| Shortness of Breath | 6.74 |
| Chest Pain | 4.05 |
| Dizziness |	3.64 |

> **Conclusion:** Admitted patients receive roughly 10x the medication volume of discharged patients, confirming disposition is a strong proxy for clinical resource intensity. Respiratory and cardiac complaints (shortness of breath, chest pain) are the most pharmacy-intensive presentation types and should be prioritized in medication stocking and nursing workload planning.


3.3 Zero, Single, and Multiple Medication Visits:
| Category | % of Visits |
|---|---|
| Zero medications | 65.14% |
| Multiple medications |	27.45% |
| One medication |	7.41% |
> **Conclusion:** Roughly two-thirds of ED visits require no medication at all, while just over a quarter are medication-intensive (2+ medications). This bimodal split — very few visits land in the middle 'one medication' category — suggests two structurally different visit types are driving volume: quick, low-intervention visits and resource-heavy visits.

4. Insurance Mix
| Insurance Type | % of Visits |
|---|---|
| Medicaid | 65.14% |
| Commercial |	27.45% |
| Medicare |	7.41% |
| Other | 5.14%|
| Self Pay | 0.22% |
> **Conclusion:** Medicaid is the dominant payer at nearly 44% of volume, with Commercial and Medicare roughly tied for second. Self-pay is negligible (0.22%). This payer mix has direct reimbursement planning implications for hospital finance, given that Medicaid reimbursement rates and terms typically differ substantially from Commercial coverage.

5. Quality & Safety:
5.1 1 Triage Accuracy Proxy: ESI vs. Disposition:
| ESI | Admit % | Discharge % |
|---|---|---|
| 1 (n=101) |	91.09% |	8.91% |
| 2 (n=5,549) |	63.72% |	36.28% |
| 3 (n=10,942) |	37.04% |	62.96% |
| 4 (n=6,406) |	2.06% |	97.94% |
| 5 (n=945) |	0.63% |	99.37% |
| Blank (n=21) |	38.10% |	61.90% |
> **Conclusion:**Admit rate declines monotonically and sharply as ESI moves from 1 to 5 (91% down to 0.6%), confirming triage acuity is a strong, reliable predictor of actual outcome in this ED. The blank-ESI group (n=21) is too small to interpret with confidence and should be flagged as a data-completeness note rather than a finding. Missing ESI itself is rare overall (0.09% of visits), so triage documentation compliance for this specific field is strong.

5.2 Missing Vitals Rate:
| Vital Sign | % Missing |
|---|---|
| Heart Rate |	49% |
| Systolic BP |	49% |
| Diastolic BP |	49% |
| Respiratory Rate |	49% |
| Oxygen Saturation |	61% |
| Temperature |	56% |

> **Conclusion:** four of six vital signs show a strikingly uniform ~49% missing rate, which is more consistent with a structural documentation gap affecting a defined subset of visits than with random per-field data entry omissions. Oxygen saturation has a gap of (61%) and oxygen saturation devices has a gap of (56%), suggesting an additional, field-specific issue (e.g., device availability or workflow step) beyond whatever is driving the shared ~49% baseline. This is a genuine documentation-compliance finding warranting a targeted process review at triage.

6. Admission Risk and Acuity Across Utilization Buckets:
Admission rate rises steadily from the 0-visit through 11-20-visit buckets, consistent with escalating or unmanaged clinical need. The 21+ bucket breaks this pattern: it has the lowest admission rate of all buckets, despite carrying the most urgent (lowest) average ESI of any group.

Investigation into chief complaints for this bucket found alcohol intoxication accounts for approximately 40% of presentations — far above its share in any other bucket, where abdominal pain is consistently the top complaint. This complaint is characteristically triaged as urgent (altered mental status, unstable presentation) but typically resolves through observation and discharge rather than admission.

> **Conclusion:** Admit rate is not positively correlated with prior ED visit count — it rises through the 6-20 visit bucket but then falls sharply for the 21+ segment. This isn't a contradiction; it reflects the fact that a high visit count doesn't automatically mean a patient requires hospitalization. Many repeat visits present with urgent, danger-flagged symptoms (low ESI) that are nonetheless resolved through immediate treatment rather than admission — the alcohol intoxication finding is the clearest example of this. This means number of visists alone is not a reliable severity indicator.


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


