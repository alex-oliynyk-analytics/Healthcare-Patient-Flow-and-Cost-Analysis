# 🏥 Healthcare Operations Analysis: Patient Flow, Length of Stay, and Cost Consistency

## 📌 Project Overview

This project analyzes a healthcare dataset to evaluate patient flow, operational efficiency, and cost drivers across a hospital system.

The goal of this analysis was to:
- Understand patient admission patterns
- Evaluate length of stay (LOS)
- Identify cost drivers across conditions and demographics
- Assess whether meaningful variation exists across key segments

---

## 🧰 Tools Used

- SQL (SQLite)
- DB Browser for SQLite
- Tableau (for dashboard visualization)

---

## 📊 Dataset Description

- Source: [Healthcare Dataset](https://www.kaggle.com/datasets/prasad22/healthcare-dataset) (Kaggle)
- ~55,000 patient records
- Each row represents a patient admission

Key fields:
- Age, Gender
- Medical Condition
- Admission Type
- Date of Admission / Discharge
- Billing Amount
- Insurance Provider
- Test Results

---

## 🧠 Analytical Approach

The analysis followed a structured workflow:

1. Data validation and summary metrics  
2. Patient flow analysis (admissions)  
3. Length of stay analysis  
4. Cost analysis across segments  
5. Segmentation (age, condition, insurance)  
6. Relationship analysis (cost vs length of stay)  
7. Identification of variance and key patterns  

---

## 📈 SQL Analysis & Findings

---

### 1. Patient Overview

```sql
SELECT
    COUNT(*) AS total_patients,
    ROUND(AVG(Age), 1) AS avg_age,
    ROUND(AVG([Billing Amount]), 2) AS avg_billing
FROM healthcare_dataset;
```
<img width="226" height="51" alt="Query_1_Total_patients_and_basic_overview" src="https://github.com/user-attachments/assets/4a9a9fa2-1c32-42d0-ab22-50ca71062b68" />


### Findings:

* Total Patients: ~55,500
* Average Age: ~51.5
* Average Billing: ~$25,500

---

### 2. Patient Flow by Admission Type

```sql
SELECT
    [Admission Type],
    COUNT(*) AS patient_count,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS pct_of_total
FROM healthcare_dataset
GROUP BY [Admission Type];
```
<img width="267" height="102" alt="Query_2_Patient_flow_by_admission_type" src="https://github.com/user-attachments/assets/5f70085d-6443-4496-adf9-ef611f44eb15" />

### Findings:
* Elective, Urgent, and Emergency admissions are evenly distributed (~33% each)
### Insight:
* Patient inflow is balanced → no dominant admission pathway
---
### 3. Average Length of Stay

```sql
SELECT
    ROUND(AVG(julianday([Discharge Date]) - julianday([Date of Admission])), 2)
    AS avg_length_of_stay
FROM healthcare_dataset;
```
<img width="162" height="51" alt="Query_3_ Average_length_of_stay" src="https://github.com/user-attachments/assets/ea889bf4-dd22-43dd-b89d-4624c71e1255" />

### Findings:
* Average Length of Stay: ~15.5 days

### Insight:
* Indicates standardized treatment duration across conditions
---
### 4. Length of Stay by Medical Condition

```sql
SELECT
    [Medical Condition],
    COUNT(*) AS patient_count,
    ROUND(AVG(julianday([Discharge Date]) - julianday([Date of Admission])), 2)
    AS avg_length_of_stay
FROM healthcare_dataset
GROUP BY [Medical Condition];
```
<img width="358" height="183" alt="Query_4_Length_of_stay_by_medical_condition" src="https://github.com/user-attachments/assets/47e230da-8474-4abb-8f95-0e1d244eade4" />

### Findings:
* Length of stay is nearly identical across all conditions (~15.4–15.7 days)

### Insight:
* Indicates standardized treatment duration across conditions
---
### 5. Billing by Medical Condition

```sql
SELECT
    [Medical Condition],
    COUNT(*) AS patient_count,
    ROUND(AVG([Billing Amount]), 2) AS avg_billing,
    ROUND(SUM([Billing Amount]), 2) AS total_billing
FROM healthcare_dataset
GROUP BY [Medical Condition];
```
<img width="360" height="184" alt="Query_5_Billing_by_medical_condition" src="https://github.com/user-attachments/assets/7658ee40-7810-4212-89ee-0e1039aeafbc" />

### Findings:
* Average billing remains consistent (~$25K) across all conditions

### Insight:
* Billing does not significantly vary by condition, indicating a standardized pricing structure
---

### 6. Billing by Insurance Provider

```sql
SELECT
    [Insurance Provider],
    COUNT(*) AS patient_count,
    ROUND(AVG([Billing Amount]), 2) AS avg_billing,
    ROUND(SUM([Billing Amount]), 2) AS total_billing
FROM healthcare_dataset
GROUP BY [Insurance Provider];
```
<img width="405" height="158" alt="Query_6_Billing_by_insurance_provider" src="https://github.com/user-attachments/assets/c922e401-5501-41a0-a10a-08164f749b88" />

### Findings:
* Insurance providers show similar billing patterns

### Insight:
* Payer mix is evenly distributed → low dependency on a single provider
---

###  7. Length of Stay by Age Group

```sql
SELECT
    CASE
        WHEN Age < 30 THEN 'Under 30'
        WHEN Age BETWEEN 30 AND 49 THEN '30-49'
        WHEN Age BETWEEN 50 AND 69 THEN '50-69'
        ELSE '70+'
    END AS age_group,
    COUNT(*) AS patient_count,
    ROUND(AVG(julianday([Discharge Date]) - julianday([Date of Admission])), 2)
    AS avg_length_of_stay,
    ROUND(AVG([Billing Amount]), 2) AS avg_billing
FROM healthcare_dataset
GROUP BY age_group;
```
<img width="393" height="132" alt="Query_7_Length_of_stay_by_age_group" src="https://github.com/user-attachments/assets/1160c7b3-8fad-44f4-98db-a96b551ce57a" />

### Findings:
* Older patients have slightly longer stays, but differences are minimal

---

###  8. Cost vs Length of Stay Relationship

```sql
SELECT
    [Medical Condition],
    ROUND(AVG(julianday([Discharge Date]) - julianday([Date of Admission])), 2)
    AS avg_length_of_stay,
    ROUND(AVG([Billing Amount]), 2) AS avg_billing
FROM healthcare_dataset
GROUP BY [Medical Condition];
```
<img width="341" height="184" alt="Query_8_Cost_vs_length_of_stay_relationship" src="https://github.com/user-attachments/assets/0b36cae4-c8dc-4490-84ba-87f5767f21bc" />

### Insight:
* Minimal variance across conditions suggests standardized treatment duration rather than condition-specific care differences

### Insight:
*  Relationship exists but is not differentiated by segment

---

###  9. Test Results Distribution

```sql
SELECT
    [Test Results],
    COUNT(*) AS patient_count,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS pct_of_total
FROM healthcare_dataset
GROUP BY [Test Results];
```
<img width="279" height="109" alt="Query_9_Test_results_distribution" src="https://github.com/user-attachments/assets/d151276a-fea6-44e9-b2fa-3541bbff35b8" />

---

### 10. High-Cost Segments

```sql
SELECT
    [Medical Condition],
    [Insurance Provider],
    COUNT(*) AS patient_count,
    ROUND(AVG([Billing Amount]), 2) AS avg_billing
FROM healthcare_dataset
GROUP BY [Medical Condition], [Insurance Provider]
HAVING COUNT(*) >= 5
ORDER BY avg_billing DESC;
```
<img width="432" height="697" alt="Query_10_Highest_cost_segments_PART_1" src="https://github.com/user-attachments/assets/fb74dfd8-4eea-4feb-9b45-9a1893391aa5" /><p>
<img width="408" height="107" alt="Query_10_Highest_cost_segments_PART_2" src="https://github.com/user-attachments/assets/2532fc82-0907-4e51-b2db-72f7e12202e9" />
### 🔑 Key Insights

The analysis reveals a consistent pattern across all major dimensions, with limited variability in key operational metrics.

* Billing remains highly consistent (~$25K) across all conditions
* Length of stay is uniform (~15.5 days) across demographics
* Admission types are evenly distributed
* No meaningful variation across major segmentation dimensions

---
### ⚠️ Key Analytical Finding

Despite extensive segmentation, the dataset shows low variance across all key metrics.

👉 This suggests:

* Standardized treatment durations
* Standardized billing practices
* Limited differentiation across patient groups

---

### 🚀 Next Steps
* Apply similar analysis to real-world healthcare datasets with higher variance
* Incorporate readmission rates and patient outcomes
* Build predictive models for length of stay
* Compare across hospitals or providers
---

### 🔍 Analytical Takeaways

- Applied SQL to analyze patient flow, cost, and operational metrics  
- Evaluated healthcare data to assess variation across key segments  
- Used segmentation (age, condition, insurance) to test for meaningful differences  
- Identified lack of variance as a key finding rather than forcing conclusions  
- Translated analysis into clear business insights around cost consistency and standardized care patterns  
