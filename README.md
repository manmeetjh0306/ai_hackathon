==== PPT ====
Using the attached EXL Hackathon PowerPoint template, create a visually compelling five-minute pitch deck for “AquaSentinel AI.”

AquaSentinel AI is an agentic solution for UK water utilities that detects Leakage, Burst Risk and Low Pressure from network telemetry, explains the evidence, estimates customer and critical-site impact, creates an AOCD incident, assigns the SLA, selects the nearest qualified field engineer, generates a work order and prepares customer and field-team notifications—with human approval before execution.

Follow the template sections:

1. Problem & Customer Focus
2. AI Solution & Innovation
3. Competitive Differentiation
4. Revenue & Business Growth

Include verified UK water-industry statistics from Ofwat, the Environment Agency and Water UK covering leakage, burst pipes, network scale and AMP8 investment. Cite every source and do not invent statistics.

Show this journey visually:

Observe → Detect → Explain → Predict impact → Recommend → Human approval → Create incident → Dispatch engineer → Notify customers → Monitor resolution

Highlight:

- Explainable multi-signal anomaly detection
- AI Situation Room and “What happens if we wait?” simulation
- Detection, Impact, Incident, Dispatch and Communication agents
- Integration with SCADA, GIS, CRM and work-management systems
- Faster AOCD incident creation, triage, field assignment and communication
- An 8–12 week pilot across selected AOCDs and DMAs

Label all projected benefits as:

“Illustrative pilot targets—subject to utility baseline validation.”

Use minimal text, strong headlines, large numbers, agent workflows, UK network maps and AI reasoning visuals. Make the deck feel like an AI product pitch—not a reporting dashboard.

End with:

“AquaSentinel AI transforms fragmented network signals into explainable, coordinated action—helping water utilities respond before customer disruption escalates.”

======= PPT =====


This dataset represents an AI-enabled water-utility asset monitoring and incident-management system. It contains daily sensor readings for 100 assets from 1 January to 20 August 2026, including pressure, flow, vibration and acoustic measurements. The data identifies anomalous readings, classifies their type and severity, and links them to incidents, predicted customer complaints, agent workflows, customer notifications, engineer assignments, work orders and outage-restoration timelines.



Act as a senior water-utility data scientist, rule-engine architect and
backend engineer.

Transform raw daily water-utility sensor readings into expected values,
deviations, anomaly scores, anomaly flags, anomaly types, severity,
incidents, SLAs, complaints, notifications and work orders.

All calculations must be explainable, deterministic and performed separately
for each Asset_ID.

Do not overwrite the raw readings. Create separate enriched output tables and
a decision log.

============================================================
1. REQUIRED INPUT DATA
============================================================

RAW SENSOR READING FIELDS

- Reading_ID
- Reading_Date
- Timestamp
- Asset_ID
- Sensor_ID
- DMA_ID
- Pressure_bar
- Flow_Ls
- Vibration_mm_s
- Acoustic_dB
- Data_Quality_Status
- Maintenance_Flag
- Planned_Shutdown_Flag
- Sensor_Fault_Flag

ASSET DIMENSION FIELDS

- Asset_ID
- Asset_Type
- Material
- Age_Years
- Health_Score
- RUL
- Criticality
- DMA_ID
- Sensor_ID
- Baseline_Pressure_bar
- Baseline_Flow_Ls
- Baseline_Vibration_mm_s
- Baseline_Acoustic_dB
- Asset_Status

DMA DIMENSION FIELDS

- DMA_ID
- Zone
- Population_Served
- Critical_Sites
- Baseline_Demand_MLd

ENGINEER DIMENSION FIELDS

- Engineer_ID
- Skill
- Zone
- Status
- Current_Workload
- Maximum_Workload

============================================================
2. PROCESSING ORDER
============================================================

Process every reading in this exact sequence:

1. Validate the raw reading.
2. Join asset and DMA attributes.
3. Calculate expected values.
4. Calculate signed deviations.
5. Calculate overall variance.
6. Calculate signal component scores.
7. Identify credible sensor patterns.
8. Calculate preliminary anomaly status.
9. Assign a preliminary anomaly type.
10. Estimate customers impacted.
11. Calculate signal-risk and customer-impact indexes.
12. Calculate the final anomaly score.
13. Confirm Anomaly as Yes or No.
14. Assign signal-level severity.
15. Assign final operational severity.
16. Correlate the reading with existing incidents.
17. Create a new incident or update an existing incident.
18. Allocate incident SLA.
19. Determine incident status and resolution date.
20. Estimate complaints.
21. Determine customer and field-team notifications.
22. Determine whether a work order is required.
23. Assign a suitable field engineer.
24. Record every decision in the decision log.

This processing order is important because Customers_Impacted depends on the
preliminary Anomaly_Type, while the final Anomaly_Score includes customer
impact.

============================================================
3. DATA-QUALITY CALCULATIONS
============================================================

Create:

- Data_Quality_Issue
- Data_Quality_Reason

Set Data_Quality_Issue = Yes when any of the following is true:

- Asset_ID is blank.
- Sensor_ID is blank.
- Reading_Date is blank.
- Asset_ID does not exist in DIM_Asset.
- Sensor_ID is not mapped to Asset_ID.
- DMA_ID does not match the asset's DMA_ID.
- Asset_ID + Sensor_ID + Reading_Date is duplicated.
- A sensor measure is null.
- A sensor measure is outside the configured physical range.
- Sensor_Fault_Flag = Yes.
- The sensor is inactive.
- The sensor remains at exactly the same value for an unrealistic period.
- The reading represents an impossible discontinuity.

Do not generate a network anomaly or incident when:

- Data_Quality_Issue = Yes;
- Maintenance_Flag = Yes; or
- Planned_Shutdown_Flag = Yes.

Such records may create a separate sensor-maintenance or data-quality alert.

============================================================
4. EXPECTED-VALUE CALCULATION
============================================================

Expected values must be calculated separately for each Asset_ID.

Do not use the current reading or any future reading in the expected-value
calculation.

Use a rolling window of the previous 28 valid readings for the same Asset_ID.

Valid baseline readings must satisfy:

- Reading_Date is earlier than the current Reading_Date.
- Data_Quality_Issue = No.
- Maintenance_Flag = No.
- Planned_Shutdown_Flag = No.
- Sensor_Fault_Flag = No.
- Asset_Status = Active.
- The historical record is not a confirmed anomaly.

Use the rolling median rather than the rolling mean because the median is less
affected by extreme values.

Expected_Pressure_bar =
MEDIAN of Pressure_bar from the previous 28 valid non-anomalous readings for
the same Asset_ID.

Expected_Flow_Ls =
MEDIAN of Flow_Ls from the previous 28 valid non-anomalous readings for the
same Asset_ID.

Expected_Vibration_mm_s =
MEDIAN of Vibration_mm_s from the previous 28 valid non-anomalous readings for
the same Asset_ID.

Expected_Acoustic_dB =
MEDIAN of Acoustic_dB from the previous 28 valid non-anomalous readings for
the same Asset_ID.

------------------------------------------------------------
4.1 MINIMUM-HISTORY RULE
------------------------------------------------------------

If at least 14 but fewer than 28 valid historical readings exist:

- Use the median of all available valid historical readings.
- Set Baseline_Method = "Available Historical Median".
- Set Baseline_Confidence = "Medium".

If at least 28 valid historical readings exist:

- Use the median of the previous 28 valid readings.
- Set Baseline_Method = "28-Day Rolling Median".
- Set Baseline_Confidence = "High".

If fewer than 14 valid historical readings exist:

- Use the appropriate baseline from DIM_Asset.
- Set Baseline_Method = "Asset Master Baseline".
- Set Baseline_Confidence = "Low".

Fallback calculations:

Expected_Pressure_bar = Baseline_Pressure_bar

Expected_Flow_Ls = Baseline_Flow_Ls

Expected_Vibration_mm_s = Baseline_Vibration_mm_s

Expected_Acoustic_dB = Baseline_Acoustic_dB

------------------------------------------------------------
4.2 INITIAL BASELINE AND ITERATIVE PROCESSING
------------------------------------------------------------

Historical readings do not initially contain anomaly labels. Avoid circular
logic by applying two passes.

PASS 1

- Calculate expected values using prior valid readings.
- Apply basic outlier exclusion using the asset-master baseline or robust
  median and MAD.
- Generate preliminary anomaly labels.

PASS 2

- Recalculate expected values after excluding preliminary anomalies.
- Recalculate deviations, scores and final anomaly classifications.

Do not repeatedly recalculate until all unusual readings disappear. Limit the
process to two controlled passes unless another business rule is provided.

------------------------------------------------------------
4.3 OPTIONAL DAY-PATTERN ADJUSTMENT
------------------------------------------------------------

If sufficient history exists, calculate expected values using comparable days:

- Same weekday
- Weekday versus weekend
- Similar demand condition
- Similar operating mode
- Similar season

For example:

Expected_Pressure_bar =
Median pressure for the same Asset_ID, similar day type and previous valid
operating periods.

If there is insufficient comparable history, use the standard previous
28-reading median.

============================================================
5. SIGNED DEVIATION CALCULATIONS
============================================================

Calculate signed deviations as decimals.

Pressure_Deviation_pct =
(Pressure_bar - Expected_Pressure_bar)
/
Expected_Pressure_bar

Flow_Deviation_pct =
(Flow_Ls - Expected_Flow_Ls)
/
Expected_Flow_Ls

Vibration_Deviation_pct =
(Vibration_mm_s - Expected_Vibration_mm_s)
/
Expected_Vibration_mm_s

Acoustic_Deviation_pct =
(Acoustic_dB - Expected_Acoustic_dB)
/
Expected_Acoustic_dB

Interpretation:

- 0.20 means 20% above expected.
- -0.20 means 20% below expected.
- Negative pressure deviation indicates pressure loss.
- Positive acoustic deviation indicates acoustic elevation.
- Positive vibration deviation indicates vibration elevation.
- Flow direction must be interpreted according to the anomaly pattern.

If an expected value is zero or null:

- Do not divide by zero.
- Set the corresponding deviation to null.
- Set Data_Quality_Issue = Yes unless a valid alternative baseline is
  available.

============================================================
6. OVERALL VARIANCE
============================================================

Overall_Variance_pct =
MAX(
    ABS(Pressure_Deviation_pct),
    ABS(Flow_Deviation_pct),
    ABS(Vibration_Deviation_pct),
    ABS(Acoustic_Deviation_pct)
)

Create Dominant_Signal as the signal producing Overall_Variance_pct.

Allowed values:

- Pressure
- Flow
- Vibration
- Acoustic

============================================================
7. COMPONENT-SCORE CALCULATIONS
============================================================

Use the following configurable reference thresholds:

- Pressure reference threshold = 0.08
- Flow reference threshold = 0.10
- Vibration reference threshold = 0.16
- Acoustic reference threshold = 0.12

Calculate:

Pressure_Component_Score =
ABS(Pressure_Deviation_pct) / 0.08

Flow_Component_Score =
ABS(Flow_Deviation_pct) / 0.10

Vibration_Component_Score =
ABS(Vibration_Deviation_pct) / 0.16

Acoustic_Component_Score =
ABS(Acoustic_Deviation_pct) / 0.12

Interpretation:

- Score below 1.0 = within the reference threshold.
- Score equal to 1.0 = threshold reached.
- Score above 1.0 = threshold exceeded.
- Score at or above 1.5 = materially abnormal.
- Score at or above 3.0 = extreme deviation.

Corroborating_Signal_Count =
Number of component scores greater than or equal to 1.0.

Dominant_Component_Score =
MAX(
    Pressure_Component_Score,
    Flow_Component_Score,
    Vibration_Component_Score,
    Acoustic_Component_Score
)

============================================================
8. SIGNAL-RISK INDEX
============================================================

Calculate a 0–100 sensor-risk index:

Signal_Risk_Index_0_100 =
MIN(
    Dominant_Component_Score / 4,
    1
)
× 100

The value 4 is the configurable saturation point.

Interpretation:

- Dominant component of 1 gives a signal-risk index of 25.
- Dominant component of 2 gives a signal-risk index of 50.
- Dominant component of 3 gives a signal-risk index of 75.
- Dominant component of 4 or more gives a signal-risk index of 100.

Use full-precision component values in calculations and round only the final
displayed result.

============================================================
9. PERSISTENCE CALCULATION
============================================================

Calculate Persistence_Count separately for each Asset_ID.

Persistence_Count =
Number of consecutive daily readings satisfying the same or a compatible
anomaly pattern.

The current reading is included in Persistence_Count.

Interpretation:

- 1 = isolated reading
- 2 = confirmed across two consecutive days
- 3 or more = sustained pattern

Reset Persistence_Count to zero when:

- The signal returns to normal;
- The pattern changes materially; or
- The date sequence is broken.

Allow immediate anomaly confirmation without persistence when:

- Dominant_Component_Score is at least 3.0;
- Two or more signals are Critical;
- A critical burst-risk pattern exists; or
- Multiple assets in the same DMA show a compatible event.

Because the source data is daily, classify burst events as Burst Risk or
Possible Burst. Do not claim second-by-second real-time burst detection.

============================================================
10. PRELIMINARY ANOMALY FLAG
============================================================

Create Preliminary_Anomaly = Yes when any condition is satisfied.

CONDITION A — MULTI-SIGNAL CONFIRMATION

Corroborating_Signal_Count >= 2
AND signal directions form a credible physical pattern.

CONDITION B — PERSISTENT SINGLE SIGNAL

Dominant_Component_Score >= 1.5
AND Persistence_Count >= 2.

CONDITION C — EXTREME SIGNAL

Dominant_Component_Score >= 3.0
AND Data_Quality_Issue = No.

CONDITION D — CRITICAL HYDRAULIC PATTERN

Pressure_Deviation_pct <= -0.40
AND Acoustic_Deviation_pct >= 0.35.

CONDITION E — DMA-WIDE CONFIRMATION

At least the configured minimum number or percentage of assets within the same
DMA show a compatible hydraulic pattern on the same date.

Otherwise:

Preliminary_Anomaly = No.

============================================================
11. PRELIMINARY ANOMALY TYPE
============================================================

Apply the following rules in priority order.

------------------------------------------------------------
11.1 BURST RISK
------------------------------------------------------------

Critical Burst Risk:

Pressure_Deviation_pct <= -0.40
AND Acoustic_Deviation_pct >= 0.50.

Standard Burst Risk:

Pressure_Deviation_pct <= -0.25
AND Acoustic_Deviation_pct >= 0.35
AND at least one condition is true:

- Flow_Deviation_pct >= 0.20;
- Vibration_Deviation_pct >= 0.20;
- Same_DMA_Confirmation = Yes.

------------------------------------------------------------
11.2 LEAKAGE
------------------------------------------------------------

Pressure_Deviation_pct <= -0.08
AND Acoustic_Deviation_pct >= 0.12
AND at least one condition is true:

- Flow_Deviation_pct >= 0.10;
- Persistence_Count >= 2;
- Same_DMA_Confirmation = Yes.

Typical pattern:

- Pressure decreases.
- Flow increases or becomes abnormal.
- Acoustic level increases.

------------------------------------------------------------
11.3 LOW PRESSURE
------------------------------------------------------------

Pressure_Deviation_pct <= -0.08
AND Acoustic_Deviation_pct < 0.20
AND Vibration_Deviation_pct < 0.20.

Supporting indicators:

- Flow is also below expected;
- Persistence_Count is at least 2; or
- Multiple assets in the same DMA show low pressure.

------------------------------------------------------------
11.4 PUMP PERFORMANCE
------------------------------------------------------------

Apply only when Asset_Type = "Pump".

Vibration_Deviation_pct >= 0.20
AND at least one condition is true:

- Acoustic_Deviation_pct >= 0.20;
- Flow_Deviation_pct <= -0.10;
- Pressure_Deviation_pct <= -0.08.

------------------------------------------------------------
11.5 VALVE PERFORMANCE
------------------------------------------------------------

Apply only when Asset_Type = "Valve".

At least one condition is true:

- Vibration_Deviation_pct >= 0.20;
- Acoustic_Deviation_pct >= 0.20.

AND:

ABS(Flow_Deviation_pct) >= 0.10.

AND the Leakage and Burst Risk rules are not satisfied.

------------------------------------------------------------
11.6 METER IRREGULARITY
------------------------------------------------------------

Apply only when Asset_Type = "Meter".

ABS(Flow_Deviation_pct) >= 0.20
AND Pressure_Deviation_pct > -0.08
AND Acoustic_Deviation_pct < 0.20.

------------------------------------------------------------
11.7 SENSOR OR DATA-QUALITY ISSUE
------------------------------------------------------------

Classify as Sensor/Data Quality Issue when:

- One measure changes extremely;
- Other measures remain normal;
- The pattern is physically inconsistent;
- The pattern does not persist; or
- Sensor-quality checks fail.

Do not create a network incident from a Sensor/Data Quality Issue.

============================================================
12. ESTIMATE CUSTOMERS IMPACTED
============================================================

Customers_Impacted must be estimated after assigning the preliminary anomaly
type and before calculating the final anomaly score.

Start with an anomaly-category impact factor:

- Burst Risk = 0.10
- Low Pressure = 0.08
- Leakage = 0.06
- Valve Performance = 0.05
- Pump Performance = 0.04
- Meter Irregularity = 0.02

Calculate Event_Magnitude_Index:

Event_Magnitude_Index =
MIN(
    Overall_Variance_pct / 0.50,
    1
)

This means an overall variance of 50% or more reaches the maximum event
magnitude.

Calculate Asset_Criticality_Factor:

- Standard = 1.00
- High = 1.10
- Critical = 1.25

Calculate Health_Factor:

- Health_Score >= 70 = 1.00
- Health_Score from 50 to 69 = 1.10
- Health_Score below 50 = 1.20

Calculate Persistence_Factor:

- Persistence_Count = 1 → 0.75
- Persistence_Count = 2 → 1.00
- Persistence_Count >= 3 → 1.20

Calculate Critical_Site_Factor:

- Critical_Sites = 0 → 1.00
- Critical_Sites from 1 to 5 → 1.05
- Critical_Sites greater than 5 → 1.10

Estimate impacted customers:

Raw_Customers_Impacted =
Population_Served
× Category_Impact_Factor
× Event_Magnitude_Index
× Asset_Criticality_Factor
× Health_Factor
× Persistence_Factor
× Critical_Site_Factor

Customers_Impacted =
ROUND(
    MIN(
        MAX(Raw_Customers_Impacted, 1),
        Population_Served
    )
)

For normal readings:

Customers_Impacted = 0.

These impact factors are configurable prototype assumptions. Preserve them in
a rule-configuration table instead of hardcoding them permanently.

============================================================
13. CUSTOMER-IMPACT INDEX
============================================================

Do not use a fixed denominator of 300.

Use the proportion of the DMA population affected:

Customer_Impact_Ratio =
Customers_Impacted / Population_Served

Customer_Impact_Index_0_100 =
MIN(
    Customer_Impact_Ratio,
    1
)
× 100

Optional critical-site enhancement:

Critical_Site_Impact_Ratio =
Critical_Sites_Impacted / MAX(Critical_Sites, 1)

Customer_Impact_Index_0_100 =
MIN(
    0.80 × Customer_Impact_Ratio
    +
    0.20 × Critical_Site_Impact_Ratio,
    1
)
× 100

Use the simple population-based formula when Critical_Sites_Impacted is not
available.

============================================================
14. FINAL ANOMALY SCORE
============================================================

Calculate:

Anomaly_Score_0_100 =
0.80 × Signal_Risk_Index_0_100
+
0.20 × Customer_Impact_Index_0_100

Interpretation:

- 80% of the score comes from sensor evidence.
- 20% comes from estimated customer/operational impact.

Use full precision during calculation and round the displayed score to two
decimal places.

The final score must remain between 0 and 100.

============================================================
15. FINAL ANOMALY YES/NO
============================================================

Set Anomaly = Yes when:

- Preliminary_Anomaly = Yes;
- Data_Quality_Issue = No;
- Maintenance_Flag = No;
- Planned_Shutdown_Flag = No;
- A recognised Anomaly_Type was assigned; and
- One of these confidence conditions is satisfied:

  a. Corroborating_Signal_Count >= 2;
  b. Persistence_Count >= 2 and Dominant_Component_Score >= 1.5;
  c. Dominant_Component_Score >= 3.0;
  d. Critical Burst Risk rule is satisfied;
  e. Same_DMA_Confirmation = Yes;
  f. Anomaly_Score_0_100 >= 40.

Set Anomaly = No otherwise.

When Anomaly = No:

- Anomaly_Type = "None"
- Severity = "Normal"
- Customers_Impacted = 0
- Incident_ID = null
- Incident_Status = "No Incident"
- SLA_Hours = null
- Complaints_Raised = 0
- Customer_Notification_Sent = "No"
- Field_Team_Notification_Sent = "No"

============================================================
16. SIGNAL-LEVEL SEVERITY
============================================================

Calculate separate signal severities.

PRESSURE SEVERITY

Use pressure reduction:

- Normal: reduction below 8%
- Low: 8% to below 15%
- Medium: 15% to below 25%
- High: 25% to below 40%
- Critical: 40% or more

FLOW SEVERITY

Use absolute deviation:

- Normal: below 10%
- Low: 10% to below 20%
- Medium: 20% to below 35%
- High: 35% to below 50%
- Critical: 50% or more

VIBRATION SEVERITY

Use positive deviation:

- Normal: below 10%
- Low: 10% to below 20%
- Medium: 20% to below 35%
- High: 35% to below 50%
- Critical: 50% or more

ACOUSTIC SEVERITY

Use positive deviation:

- Normal: below 10%
- Low: 10% to below 20%
- Medium: 20% to below 35%
- High: 35% to below 50%
- Critical: 50% or more

Create:

- Pressure_Severity
- Flow_Severity
- Vibration_Severity
- Acoustic_Severity

============================================================
17. BASE SCORE SEVERITY
============================================================

Calculate Score_Based_Severity:

- Anomaly Score below 40 = Low
- Anomaly Score from 40 to below 60 = Medium
- Anomaly Score from 60 to below 80 = High
- Anomaly Score of 80 or above = Critical

This is a fallback severity and must not override a stronger engineering rule.

============================================================
18. FINAL OPERATIONAL SEVERITY
============================================================

Assign final Severity using signal severity, corroboration, persistence,
customer impact, asset criticality and critical sites.

LOW

Assign Low when:

- The anomaly is credible but weak;
- No signal exceeds Medium;
- Persistence is limited;
- Customer_Impact_Ratio is below 5%;
- Critical sites are not at immediate risk.

MEDIUM

Assign Medium when:

- One Medium signal persists; or
- Two Low/Medium signals corroborate; or
- Customer_Impact_Ratio is at least 5% but below 10%.

HIGH

Assign High when:

- One High signal has supporting evidence; or
- Two or more persistent Medium signals corroborate; or
- Customer_Impact_Ratio is at least 10% but below 20%; or
- The affected asset is Critical and the event requires prioritised response.

CRITICAL

Assign Critical only when at least one condition is satisfied:

1. Two or more supporting signals have Critical signal severity.

2. The Critical Burst Risk rule is satisfied:
   Pressure_Deviation_pct <= -0.40
   AND Acoustic_Deviation_pct >= 0.50.

3. One extreme Critical signal is supported by:
   - Another abnormal signal; and
   - Customer_Impact_Ratio >= 0.20 or critical-site exposure.

4. Multiple assets in the same DMA show an extreme hydraulic event.

5. A High signal event affects critical sites and requires immediate
   operational escalation.

Final Severity should be the highest justified result from:

- Score_Based_Severity;
- Signal-pattern severity;
- Corroboration;
- Persistence;
- Customer impact;
- Asset criticality; and
- Critical-site impact.

Do not assign Critical only because one isolated sensor value is high.

============================================================
19. CONFIDENCE SCORE
============================================================

Calculate a 0–100 confidence score.

Start with:

Base_Confidence =
MIN(
    Corroborating_Signal_Count / 4,
    1
)
× 50

Add:

- 20 points if Persistence_Count >= 2.
- 10 additional points if Persistence_Count >= 3.
- 10 points if Same_DMA_Confirmation = Yes.
- 10 points if Baseline_Confidence = High.

Subtract:

- 25 points if Baseline_Confidence = Low.
- 30 points if Data_Quality_Issue = Yes.
- 20 points if Maintenance_Flag = Yes.
- 20 points if Planned_Shutdown_Flag = Yes.

Confidence_Score =
MIN(
    MAX(
        Base_Confidence + additions - deductions,
        0
    ),
    100
)

Require a configurable minimum confidence before incident creation.

Recommended initial minimum:

Confidence_Score >= 50.

Allow emergency override for a Critical Burst Risk pattern.

============================================================
20. INCIDENT CORRELATION
============================================================

Before creating a new incident, search for an active incident with:

- The same Asset_ID and compatible Anomaly_Type; or
- The same DMA_ID and compatible hydraulic anomaly; and
- A start date within the applicable correlation window.

Initial correlation windows:

- Burst Risk = 1 day for daily data
- Leakage = 3 days
- Low Pressure = 2 days
- Pump Performance = 3 days
- Valve Performance = 3 days
- Meter Irregularity = 3 days

If a matching open incident exists:

- Link the reading to the existing Incident_ID.
- Increase Persistence_Count.
- Recalculate Customers_Impacted.
- Recalculate Anomaly_Score and Severity.
- Do not generate a duplicate incident.

If no matching incident exists and the anomaly is confirmed:

- Generate a new Incident_ID.

============================================================
21. INCIDENT CREATION
============================================================

Create an incident only when:

- Anomaly = Yes;
- Confidence_Score meets the minimum;
- Data_Quality_Issue = No;
- Maintenance_Flag = No;
- Planned_Shutdown_Flag = No; and
- No correlated active incident already represents the event.

Populate:

- Incident_ID
- Triggering_Reading_ID
- Asset_ID
- Sensor_ID
- DMA_ID
- Zone
- Incident_Start_Date
- Anomaly_Type
- Severity
- Anomaly_Score_0_100
- Confidence_Score
- Customers_Impacted
- Critical_Sites_Impacted
- Incident_Status
- Resolution_Date
- SLA_Hours
- SLA_Status
- Complaints_Raised
- Customer_Notification_Sent
- Field_Team_Notification_Sent

Incident_Start_Date =
Timestamp of the first confirmed anomalous reading in the correlated event.

============================================================
22. INCIDENT STATUS AND RESOLUTION
============================================================

Apply the requested synthetic status rule:

If Incident_Start_Date is from 1 August 2026 through 19 August 2026:

- Incident_Status = "Open"
- Resolution_Date = null

If Incident_Start_Date is before 1 August 2026:

- Incident_Status = "Closed"
- Resolution_Date must be populated

For synthetic closed incidents, generate Actual_Resolution_Hours using the
allocated SLA and a controlled performance factor:

Actual_Resolution_Hours =
SLA_Hours × Resolution_Performance_Factor

Use:

- 70% of incidents: factor between 0.50 and 1.00
- 20% of incidents: factor between 1.01 and 1.25
- 10% of incidents: factor between 1.26 and 1.75

Resolution_Date =
Incident_Start_Date + Actual_Resolution_Hours

Resolution_Date must not be earlier than Incident_Start_Date.

For production data, use the actual closure timestamp instead of generating
a resolution date.

============================================================
23. SLA ALLOCATION
============================================================

Allocate SLA using Anomaly_Type:

- Burst Risk = 4 hours
- Leakage = 8 hours
- Pump Performance = 8 hours
- Valve Performance = 12 hours
- Low Pressure = 12 hours
- Meter Irregularity = 24 hours

SLA_Hours =
Lookup SLA from the anomaly-type configuration.

For closed incidents:

Actual_Resolution_Hours =
Difference in hours between Resolution_Date and Incident_Start_Date.

If Actual_Resolution_Hours <= SLA_Hours:

SLA_Status = "Met"

Otherwise:

SLA_Status = "Breached"

For open incidents:

- Resolution_Date = null
- SLA_Status = "Open" if the SLA deadline has not passed
- SLA_Status = "At Risk" when at least 75% of SLA time has elapsed
- SLA_Status = "Breached" when current time exceeds the SLA deadline

SLA_Deadline =
Incident_Start_Date + SLA_Hours.

============================================================
24. COMPLAINT CALCULATION
============================================================

Calculate the base complaint rate by severity:

- Low = 0.03
- Medium = 0.06
- High = 0.10
- Critical = 0.16

Calculate anomaly-type complaint multiplier:

- Burst Risk = 1.30
- Low Pressure = 1.20
- Leakage = 1.10
- Pump Performance = 0.90
- Valve Performance = 0.90
- Meter Irregularity = 0.60

Calculate:

Estimated_Complaints =
Customers_Impacted
× Severity_Complaint_Rate
× Anomaly_Type_Complaint_Multiplier

Complaints_Raised =
ROUND(
    MAX(
        Estimated_Complaints,
        0
    )
)

Do not allow Complaints_Raised to exceed Customers_Impacted unless the
business allows multiple complaints per customer.

For normal readings:

Complaints_Raised = 0.

============================================================
25. CUSTOMER NOTIFICATION
============================================================

Set Customer_Notification_Sent = "Yes" when any condition is satisfied:

- Severity is Medium, High or Critical;
- Customers_Impacted is at least the configured notification threshold;
- Customer_Impact_Ratio is at least 5%;
- Critical sites are affected;
- Anomaly_Type is Burst Risk, Leakage or Low Pressure.

Set Customer_Notification_Sent = "No" when:

- Anomaly = No;
- Customers_Impacted = 0; or
- The event is a sensor/data-quality issue.

Suggested notification threshold:

Customers_Impacted >= 25.

Store a separate notification record for each notification event in a
production model.

============================================================
26. FIELD-TEAM NOTIFICATION
============================================================

Set Field_Team_Notification_Sent = "Yes" when any condition is satisfied:

- Severity is High or Critical;
- Anomaly_Type is Burst Risk;
- Anomaly_Type is Pump Performance;
- Anomaly_Type is Valve Performance and service is materially affected;
- A work order is created;
- Asset Criticality = Critical;
- SLA_Hours <= 8.

For persistent Medium incidents, send a field-team notification when:

Persistence_Count >= 2.

Set Field_Team_Notification_Sent = "No" for:

- Normal readings;
- Suppressed maintenance events;
- Data-quality issues not requiring sensor maintenance; or
- Low anomalies assigned only for monitoring.

============================================================
27. WORK-ORDER CREATION
============================================================

Create a work order when any condition is satisfied:

- Severity is High or Critical;
- Anomaly_Type is Burst Risk;
- Pump Performance persists for at least two readings;
- Valve Performance persists for at least two readings;
- Leakage persists for at least two readings;
- A Medium incident affects a Critical asset;
- Critical sites are affected.

Do not create a field work order for:

- Normal readings;
- Suppressed maintenance events;
- Duplicate incidents;
- Low-confidence data-quality issues.

Populate:

- WO_ID
- Incident_ID
- Asset_ID
- DMA_ID
- Required_Skill
- Engineer_ID
- Priority
- Status
- Created_At
- Assigned_At
- Target_Response_Minutes
- Target_Resolution_Hours
- Assignment_Reason

Required skill by anomaly type:

- Burst Risk → Pipe Repair
- Leakage → Leakage Investigation or Pipe Repair
- Pump Performance → Pump Maintenance
- Valve Performance → Valve Maintenance
- Low Pressure → Network Operations or Leakage Investigation
- Meter Irregularity → Meter Repair

Assign an engineer when:

- Engineer.Zone = Incident.Zone;
- Engineer.Status = "Available";
- Engineer.Skill matches Required_Skill;
- Current_Workload < Maximum_Workload.

Rank eligible engineers using:

1. Required skill match
2. Same operational zone
3. Lowest current workload
4. Shortest distance, if coordinates are available
5. Highest qualification or certification level, if available

============================================================
28. DECISION LOG
============================================================

Create a decision-log record for every major calculation or rule.

Required fields:

- Decision_Log_ID
- Reading_ID
- Incident_ID
- WO_ID
- Decision_Stage
- Rule_ID
- Rule_Description
- Actual_Value
- Expected_Value
- Deviation_Value
- Threshold_Value
- Rule_Result
- Decision
- Decision_Reason
- Processed_At
- Rule_Version

Example decision reasons:

- Pressure was 28% below expected and exceeded the High threshold.
- Acoustic was 42% above expected and corroborated the pressure reduction.
- The event was classified as Burst Risk.
- Customer impact was estimated at 12% of the DMA population.
- Final operational severity was High.
- A four-hour SLA was allocated.
- A pipe-repair work order was generated.

============================================================
29. REQUIRED ENRICHED OUTPUT FIELDS
============================================================

Return:

- Reading_ID
- Reading_Date
- Timestamp
- Asset_ID
- Sensor_ID
- DMA_ID
- Pressure_bar
- Expected_Pressure_bar
- Pressure_Deviation_pct
- Flow_Ls
- Expected_Flow_Ls
- Flow_Deviation_pct
- Vibration_mm_s
- Expected_Vibration_mm_s
- Vibration_Deviation_pct
- Acoustic_dB
- Expected_Acoustic_dB
- Acoustic_Deviation_pct
- Overall_Variance_pct
- Dominant_Signal
- Pressure_Component_Score
- Flow_Component_Score
- Vibration_Component_Score
- Acoustic_Component_Score
- Dominant_Component_Score
- Corroborating_Signal_Count
- Persistence_Count
- Signal_Risk_Index_0_100
- Preliminary_Anomaly
- Preliminary_Anomaly_Type
- Customers_Impacted
- Customer_Impact_Ratio
- Customer_Impact_Index_0_100
- Anomaly_Score_0_100
- Confidence_Score
- Anomaly
- Anomaly_Type
- Pressure_Severity
- Flow_Severity
- Vibration_Severity
- Acoustic_Severity
- Score_Based_Severity
- Severity
- Incident_ID
- Incident_Start_Date
- Resolution_Date
- Incident_Status
- SLA_Hours
- SLA_Deadline
- SLA_Status
- Complaints_Raised
- Customer_Notification_Sent
- Field_Team_Notification_Sent
- WO_ID
- Detection_Reason
- Breached_Rules
- Baseline_Method
- Baseline_Confidence
- Data_Quality_Issue
- Data_Quality_Reason
- Rule_Version

============================================================
30. VALIDATION CHECKS
============================================================

Validate that:

- Expected values do not use future readings.
- Current readings are excluded from their own baselines.
- Normal readings have no Incident_ID.
- Confirmed anomalies have an Anomaly_Type and Severity.
- All Incident_ID values are unique or correctly reused for correlated events.
- August incidents are Open and have blank Resolution_Date.
- Closed incidents have Resolution_Date.
- Resolution_Date is not earlier than Incident_Start_Date.
- SLA_Hours matches Anomaly_Type.
- Complaints_Raised is non-negative.
- Customers_Impacted does not exceed Population_Served.
- Anomaly_Score remains between 0 and 100.
- Confidence_Score remains between 0 and 100.
- Work orders reference valid Incident_ID and Engineer_ID values.
- Every calculation can be traced through the decision log.
