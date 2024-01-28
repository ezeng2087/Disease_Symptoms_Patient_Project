# Disease_Symptoms_Patient_Project

## Description

This [dataset](https://www.kaggle.com/datasets/uom190346a/disease-symptoms-and-patient-profile-dataset) provides information related to air quality, including various indicators measured across different geographic areas and time periods within NYC.


## Objective
Come up with a Disease Symptom Patient dashboard that does the following:

1. Total % of Patients stratified by
   
a) **Disease** 

b) **Gender**

c) **Age Groups**

d) **Blood Pressure Levels**

e) **Cholesterol Levels**

f) **Symptom Presence (Fever, Cough, Fatigue, Difficulty Breathing)**

### Columns
| Column                | Description                                            |
|-----------------------|--------------------------------------------------------|
| `Disease`             | Type of disease.                                       |
| `Fever`               | Presence of fever (Yes/No).                            |
| `Cough`               | Presence of cough (Yes/No).                            |
| `Fatigue`             | Presence of fatigue (Yes/No).                          |
| `DifficultyBreathing` | Difficulty in breathing (Yes/No).                      |
| `Age`                 | Age of the patient.                                    |
| `Gender`              | Gender of the patient.                                 |
| `BloodPressure`       | Blood pressure level.                                  |
| `CholesterolLevel`    | Cholesterol level.                                     |
| `OutcomeVariable`     | Original outcome variable indicating disease status (Positive/Negative). |

   
# Excel
Counting for any blank cells with COUNTBLANK function

![a21e0206ac1c0f5caf74aec08f43fd45](https://github.com/ezeng2087/Disease_Symptoms_Patient_Project/assets/154565917/1473183c-c6ae-47bf-a870-cba0c7816aab)


Created New Column "Outcome Variable(Number)" as a numerical value from adjustment of the binary values from "Outcome Variable" with IF function, returning 1 if "Positve" else 0.

![772cca7c5fbd143ea3e1903aedcd4ed7](https://github.com/ezeng2087/Disease_Symptoms_Patient_Project/assets/154565917/6b373160-b25c-49f9-9f45-2eb3d8dccad4)

Ensuring Data validation through expected values and ranges based on the column through filtering.

![ca81ffad15de17a13675c63cebe7c02e](https://github.com/ezeng2087/Disease_Symptoms_Patient_Project/assets/154565917/064a4dc5-6e82-486d-a195-4e1e2241b903)

# Dataset Description

## Columns

1. **Disease:** Type of disease.
2. **Fever:** Presence of fever (Yes/No).
3. **Cough:** Presence of cough (Yes/No).
4. **Fatigue:** Presence of fatigue (Yes/No).
5. **Difficulty Breathing:** Difficulty in breathing (Yes/No).
6. **Age:** Age of the patient.
7. **Gender:** Gender of the patient.
8. **Blood Pressure:** Blood pressure level.
9. **Cholesterol Level:** Cholesterol level.
10. **Outcome Variable:** Original outcome variable indicating disease status (Positive/Negative).
11. **Outcome Variable (Number):** Transformed outcome variable with 1 for Positive and 0 for Negative.

## Sample Rows

| Disease       | Fever | Cough | Fatigue | Difficulty Breathing | Age | Gender | Blood Pressure | Cholesterol Level | Outcome Variable | Outcome Variable (Number) |
|---------------|-------|-------|---------|-----------------------|-----|--------|-----------------|---------------------|-------------------|---------------------------|
| Influenza      | Yes   | No    | Yes     | Yes                   | 19  | Female | Low             | Normal              | Positive          | 1                         |
| Common Cold   | No    | Yes   | Yes     | No                    | 25  | Female | Normal          | Normal              | Negative          | 0                         |
| Eczema         | No    | Yes   | Yes     | No                    | 25  | Female | Normal          | Normal              | Negative          | 0                         |

## Data Transformation

The "Outcome Variable (Number)" column has been created to represent the outcome variable as numerical values, where 1 corresponds to a Positive outcome and 0 corresponds to a Negative outcome.

## Notes

- Ensure that the data is properly cleaned and standardized before further analysis.
- Data transformation steps have been documented in the dataset preparation process.


# PostgreSQL

Creating table matching the columns 

```sql
CREATE TABLE patients (
    Disease VARCHAR(255) NOT NULL,
    Fever VARCHAR(3) NOT NULL,
    Cough VARCHAR(3) NOT NULL,
    Fatigue VARCHAR(3) NOT NULL,
    DifficultyBreathing VARCHAR(3) NOT NULL,
    Age INT NOT NULL,
    Gender VARCHAR(10) NOT NULL,
    BloodPressure VARCHAR(10) NOT NULL,
    CholesterolLevel VARCHAR(10) NOT NULL,
    OutcomeVariable VARCHAR(10) NOT NULL,
    OutcomeVariableNumber INT NOT NULL
);
```

# Disease Symptom Patient Dashboard

## Objective

Come up with a Disease Symptom Patient dashboard that provides insights into the distribution of patients based on various factors.

## Total % of Patients Stratified by

```sql
-- CTE for Total Patients
WITH TotalPatients AS (
    SELECT COUNT(*) AS Total FROM patients
)
```

### a) Disease

```sql
-- Query to calculate the total percentage of patients stratified by disease
, DiseaseCTE AS (
    SELECT
        Disease,
        COUNT(*) AS TotalPatients,
        ROUND(COUNT(*) * 100.0 /(SELECT Total FROM TotalPatients), 2) AS Percentage
    FROM patients
    CROSS JOIN TotalPatients
    GROUP BY Disease
)

```

### b) Gender
```sql
-- Query to calculate the total percentage of patients stratified by gender
, GenderCTE AS (
    SELECT
        Gender,
        COUNT(*) AS TotalPatients,
        ROUND(COUNT(*) * 100.0 / (SELECT Total FROM TotalPatients), 2) AS Percentage
    FROM patients
    CROSS JOIN TotalPatients
    GROUP BY Gender
)

```

### c) Age Groups
```sql
, AgeGroupsCTE AS (
    SELECT
        CASE
            WHEN Age BETWEEN 0 AND 18 THEN '0-18'
            WHEN Age BETWEEN 19 AND 30 THEN '19-30'
            WHEN Age BETWEEN 31 AND 50 THEN '31-50'
            WHEN Age > 50 THEN '51+'
        END AS AgeGroup,
        COUNT(*) AS TotalPatients,
        ROUND(COUNT(*) * 100.0 / (SELECT Total FROM TotalPatients), 2) AS Percentage
    FROM patients
    CROSS JOIN TotalPatients
    GROUP BY AgeGroup
)

```

### d) Blood Pressure Levels
```sql
, BloodPressureCTE AS (
    SELECT
        BloodPressure,
        COUNT(*) AS TotalPatients,
        ROUND(COUNT(*) * 100.0 / (SELECT Total FROM TotalPatients), 2) AS Percentage
    FROM patients
    CROSS JOIN TotalPatients
    GROUP BY BloodPressure
)

```
### e) Cholesterol Levels
```sql
, CholesterolLevelCTE AS (
    SELECT
        CholesterolLevel,
        COUNT(*) AS TotalPatients,
        ROUND(COUNT(*) * 100.0 / (SELECT Total FROM TotalPatients), 2) AS Percentage
    FROM patients
    CROSS JOIN TotalPatients
    GROUP BY CholesterolLevel
)

```
### f) Symptom Presence
```sql
, SymptomPresenceCTE AS (
    SELECT
        Fever,
        Cough,
        Fatigue,
        DifficultyBreathing,
        COUNT(*) AS TotalPatients,
        ROUND(COUNT(*) * 100.0 / (SELECT Total FROM TotalPatients), 2) AS Percentage
    FROM patients
    CROSS JOIN TotalPatients
    GROUP BY Fever, Cough, Fatigue, DifficultyBreathing
)
```

# Selecting Results
```sql
SELECT * FROM DiseaseCTE
ORDER BY TotalPatients DESC;
```
![eb49a86ecdf75e83cb353ac007f31aa6](https://github.com/ezeng2087/Disease_Symptoms_Patient_Project/assets/154565917/0ba1d5a0-04df-4f8f-b38f-9575555595c8)

```sql
SELECT * FROM GenderCTE
ORDER BY TotalPatients DESC;
```
![0517f0d5ff0ae6790d2af87f061bfab6](https://github.com/ezeng2087/Disease_Symptoms_Patient_Project/assets/154565917/b701b67d-f1b9-4739-8454-70dd0561c0a1)

```sql
SELECT * FROM AgeGroupsCTE
ORDER BY TotalPatients DESC;
```
![80bbd228385b810758a75ca8fc9b553b](https://github.com/ezeng2087/Disease_Symptoms_Patient_Project/assets/154565917/ee6f7180-7a5d-4a42-a598-787ded32ef5f)

```sql
SELECT * FROM BloodPressureCTE
ORDER BY TotalPatients DESC;
```
![726a70fb95ac6551ad1155176ca1bbf2](https://github.com/ezeng2087/Disease_Symptoms_Patient_Project/assets/154565917/43801720-4511-45c2-8d6a-e48523410878)

```sql
SELECT * FROM CholesterolLevelCTE
ORDER BY TotalPatients DESC;
```
![3bf965a47902eb3cf498b3ecc895440d](https://github.com/ezeng2087/Disease_Symptoms_Patient_Project/assets/154565917/720076b7-11f4-4b3e-a4c9-5c07cb0ea114)

```sql
SELECT * FROM SymptomPresenceCTE
ORDER BY TotalPatients DESC;
```
![528d84f20131f1dba3d1f00bdba8d56f](https://github.com/ezeng2087/Disease_Symptoms_Patient_Project/assets/154565917/3c969777-c491-41b2-b4e5-bacb35e98b55)

Tableau Visualization
[View the Tableau Public Dashboard](https://public.tableau.com/views/Patients_17062693596110/Dashboard?:language=en-US&publish=yes&:display_count=n&:origin=viz_share_link)

