# Below is a more detailed, structured analytical summary of the Patient Emergency Room Visit Report, suitable for management review, audit documentation, or dashboard narration.

# 1.Executive Overview
The Emergency Room recorded 4,632 total patient visits, indicating sustained demand on ER services. Patient flow is primarily walk-in driven, with a strong weekday concentration and a predominantly adult patient base. While operational throughput appears stable, patient feedback capture is notably weak, limiting service quality insights.

# 2. Visit Composition & Appointment Type

•	Administrative visits: 49.35%

•	Non-administrative visits: 50.65%

**Interpretation:**

The near-even split suggests the ER is handling a significant administrative workload alongside clinical care, which may contribute to congestion and extended wait times.

# 4. Patient Access & Entry Mode

•	Walk-in patients: 58.85%

•	Referred patients: 41.15%

**Implication:**

High walk-in volume increases unpredictability in patient inflow, placing pressure on triage, staffing, and bed availability—especially during peak weekday hours.

# 5. Waiting Time & Service Efficiency

•	Average waiting time: 35.53 minutes

**Assessment:**

This wait time is moderate for an ER setting but may be perceived negatively by walk-in patients without acuity-based prioritization. Variation by age group and department likely exists and should be monitored.

# 6. Patient Satisfaction & Feedback Gaps

•	Average satisfaction score: 5.45

•	Services not rated: 75.71%

**Critical Observation:**

The satisfaction score is based on a small subset of respondents, making it statistically weak. The very high non-response rate suggests:

•	Poor survey capture at discharge

•	Digital/operational barriers to feedback
•	Potential bias toward extreme experiences

**Recommendation:**

Implement automated or mandatory post-visit feedback collection (SMS/QR-based).

# 7. Demographic Analysis

Gender Distribution

•	Male: 51.47%

•	Female: 48.23%

•	Unknown: 0.30%

**Insight:**

Gender distribution is balanced, indicating no major access disparity.

Age Group Distribution

Age Group	Visits

Adults	3,550

Teenagers	369

Middle Childhood	389

Early Childhood	222

Infancy	102

**Insight:**

Adults account for ~77% of all ER visits, making adult medicine the primary driver of ER workload.

# 7. Temporal Patterns

**Weekly Pattern**

•	Weekdays: ~3,300 visits

•	Weekends: ~1,300 visits

**Operational Impact:**

Staffing, diagnostics, and bed capacity should be weekday-optimized, with reduced but flexible weekend coverage.

Annual Trend
•	2019: ~2,177 visits
•	2020: ~2,455 visits

**Trend:**

A year-over-year increase (~13%), indicating rising ER dependency and potential future capacity constraints.

# 8. Department-Level Utilization

**Department	Visits**

None / Unassigned	2,726

General Practice	924

Orthopedics	469

Physiotherapy	164

Cardiology	117

Neurology	96

Gastroenterology	94

Renal	42

**Key Concern:**

The high “None / Unassigned” category suggests:

•	Data capture issues

•	Incomplete triage classification

•	Initial routing not being updated post-treatment

This reduces analytical accuracy and resource planning effectiveness.

# 9. Equity & Wait-Time Distribution (Heatmap Insight)

•	Darker green indicates shorter wait times

•	Certain race–age combinations experience longer waits, implying:

o	Possible triage prioritization gaps

o	Language or administrative delays

o	Uneven service distribution

Recommendation:
Conduct equity-focused wait-time audits by race and age group.

# 10. Key Risks & Opportunities

Risks

•	High walk-in dependence

•	Poor patient feedback coverage

•	Incomplete department assignment

•	Growing year-over-year demand

**Opportunities**

•	Improve triage and appointment routing

•	Strengthen feedback mechanisms

•	Reallocate staff based on weekday demand

•	Enhance data quality for better forecasting

# 11. Strategic Recommendations (Actionable)

1.	Reduce administrative ER load via pre-registration and outpatient routing.	

2.	Improve feedback capture using automated digital surveys.

3.	Clean department assignment data at discharge.

4.	Optimize weekday staffing models.

5.	Introduce predictive demand planning using historical trends.
   
<img width="961" height="470" alt="data model" src="https://github.com/user-attachments/assets/5a33d285-6338-45c7-b63a-e5963a91feab" />

<img width="845" height="512" alt="MY DASHBOARD" src="https://github.com/user-attachments/assets/aa4fe2f1-b88a-479c-96fa-9efc63906d9e" />

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# DAX MEASURES AND CALCULATED COLUMNS
```
% Administrative Schedule = DIVIDE(COUNTROWS(FILTER(Patient_Dataset,Patient_Dataset[patient_admin_flag]=TRUE())),[Total Patients])
```
```
% Non-Administrative Schedule = DIVIDE(COUNTROWS(FILTER(Patient_Dataset,Patient_Dataset[patient_admin_flag]=FALSE())),[Total Patients])
```

```
% Noratings = 
VAR _NoRatings= CALCULATE([Total Patients], Patient_Dataset[patient_sat_score]=BLANK())
RETURN
DIVIDE(_NoRatings,[Total Patients])
```
```
% of Female Visit = DIVIDE(CALCULATE([Total Patients], Patient_Dataset[patient_gender]="F"), [Total Patients])
```
```

% of Male Visit = DIVIDE(CALCULATE([Total Patients], Patient_Dataset[patient_gender]="M"), [Total Patients])
```
```
% of Unknown Visit = DIVIDE(CALCULATE([Total Patients], Patient_Dataset[patient_gender]="NC"), [Total Patients])
```
```
% Referred Patients = 
VAR _FilteredPatients= CALCULATE([Total Patients], Patient_Dataset[department_referral]<>"none")
RETURN
DIVIDE(_FilteredPatients,[Total Patients])
```
```
% Un Referred Patients = 
VAR _FilteredPatients= CALCULATE([Total Patients], Patient_Dataset[department_referral]="none")
RETURN
DIVIDE(_FilteredPatients,[Total Patients])
```
```
Average waittime = AVERAGE(Patient_Dataset[patient_waittime])
```
```
Avg satisfaction Score = CALCULATE(AVERAGE(Patient_Dataset[patient_sat_score]), Patient_Dataset[patient_sat_score]<> BLANK()) 
```
```
CF Max_Min Point(Month) = 
VAR _PatientTable= CALCULATETABLE(
    ADDCOLUMNS(
        SUMMARIZE('Date','Date'[Month]),
        "@Total_patients",[Total Patients]
        ),
        ALLSELECTED()
    )
VAR _MinValue= MINX(_PatientTable,[@Total_patients])
VAR _MaxValue= MAXX(_PatientTable,[@Total_patients])
VAR _Totalpatients=[Total Patients]

return
switch(
    TRUE(),
_Totalpatients=_MinValue, 0,
_Totalpatients=_MaxValue, 1,
BLANK()
)
```
```
CF Max_Min Point(Year) = 
VAR _PatientTable= CALCULATETABLE(
    ADDCOLUMNS(
        SUMMARIZE('Date','Date'[Year]),
        "@Total_patients",[Total Patients]
        ),
        ALLSELECTED()
    )
VAR _MinValue= MINX(_PatientTable,[@Total_patients])
VAR _MaxValue= MAXX(_PatientTable,[@Total_patients])
VAR _Totalpatients=[Total Patients]

return
switch(
    TRUE(),
_Totalpatients=_MinValue, 0,
_Totalpatients=_MaxValue, 1,
BLANK()
)
```
```
Heatmap caption = 
VAR _selectedmeasure= SELECTEDVALUE(Parameter[Parameter Order])
RETURN
if(_selectedmeasure=0 , "The darkest GREEN on the scale denotes low waittime on the Age-Group","Patients are the Most SATISFIED when the scale shows the darkest GREEN on the Age Group.")
```
```
Total Patients = COUNTROWS(Patient_Dataset)
```

```
Values Max_Min Point(Month) = 
VAR _PatientTable= CALCULATETABLE(
    ADDCOLUMNS(
        SUMMARIZE('Date','Date'[Month]),
        "@Total_patients",[Total Patients]
        ),
        ALLSELECTED()
    )
VAR _MinValue= MINX(_PatientTable,[@Total_patients])
VAR _MaxValue= MAXX(_PatientTable,[@Total_patients])
VAR _Totalpatients=[Total Patients]

return
switch(
    TRUE(),
_Totalpatients=_MinValue, [Total Patients],
_Totalpatients=_MaxValue, [Total Patients],
BLANK()
)
```
```
Values Max_Min Point(Year) = 
VAR _PatientTable= CALCULATETABLE(
    ADDCOLUMNS(
        SUMMARIZE('Date','Date'[Year]),
        "@Total_patients",[Total Patients]
        ),
        ALLSELECTED()
    )
VAR _MinValue= MINX(_PatientTable,[@Total_patients])
VAR _MaxValue= MAXX(_PatientTable,[@Total_patients])
VAR _Totalpatients=[Total Patients]

return
switch(
    TRUE(),
_Totalpatients=_MinValue, [Total Patients],
_Totalpatients=_MaxValue, [Total Patients],
BLANK()
)
```
```
Age Bucket = SWITCH(TRUE(),
Patient_Dataset[patient_age]<=10, "0-10",
Patient_Dataset[patient_age]<=20, "11-20", 
Patient_Dataset[patient_age]<=30, "21-30", 
Patient_Dataset[patient_age]<=40, "31-40", 
Patient_Dataset[patient_age]<=50, "41-50", 
Patient_Dataset[patient_age]<=60, "51-60",
Patient_Dataset[patient_age]<=70, "61-70",
 "70+"
)
```
```
Age Group = 
VAR _PatientAge= Patient_Dataset[patient_age]
RETURN
IF(_PatientAge<=2,"Infancy", 
    IF(_PatientAge<=6,"Early Childhood",
        IF(_PatientAge<=12,"Middle Childhood",
            IF(_PatientAge<=18, "Teenger","Adult")
        )
    )
)
```


