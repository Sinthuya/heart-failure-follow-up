# heart failure follow-up analysis

### Project overview

This data analysis project utilises PostgreSQL for comprehensive analysis of clinical records from 299 heart failure patients, aiming to extract insights into key clinical features and identify trends during their follow-up period.This will contribute to a deeper understanding of heart failure and its associated factors.

### Data source
The primary dataset used for this analysis is the "heart-failure-clinical-records" from The UCI Machine Learning Repository. [Click here to access the dataset](https://archive.ics.uci.edu/dataset/519/heart+failure+clinical+records)

### Tools

- PostgreSQL - data cleaning and data analysis

### Data cleaning/prepartaion 

In the initial data preparation phase, I performed the folloing tasks:
1. data loading and inspection
2. Handling missing values

```sql
SELECT
    COUNT(*) - COUNT(id) AS missing_id,
    COUNT(*) - COUNT(age) AS missing_age,
    COUNT(*) - COUNT(anaemia) AS missing_anaemia,
    COUNT(*) - COUNT(cpk) AS missing_cpk,
    COUNT(*) - COUNT(diabetes) AS missing_diabetes,
    COUNT(*) - COUNT(ejection_fraction) AS missing_ejection_fraction,
    COUNT(*) - COUNT(high_blood_pressure) AS missing_high_blood_pressure,
    COUNT(*) - COUNT(platelets) AS missing_platelets,
    COUNT(*) - COUNT(sex) AS missing_sex,
    COUNT(*) - COUNT(serum_creatinine) AS missing_serum_creatinine,
    COUNT(*) - COUNT(serum_sodium) AS missing_serum_sodium,
    COUNT(*) - COUNT(smoking) AS missing_smoking,
    COUNT(*) - COUNT(follow_up_days) AS missing_follow_up_days,
    COUNT(*) - COUNT(death_event) AS missing_death_event
FROM clinical_records;
```
   
3. Data cleaning and formatting

 ```sql
--Added a new colum 'id' as the primary key for my own referance

ALTER TABLE clinical_records
ADD COLUMN id SERIAL PRIMARY KEY;
```
```sql
-- Add a temporary column to store the updated values
ALTER TABLE clinical_records ADD COLUMN sex_temp text;

-- Update the temporary column
UPDATE clinical_records
SET sex_temp = CASE WHEN sex = 0 THEN 'female' ELSE 'male' END;

-- Drop the original 'sex' column
ALTER TABLE clinical_records DROP COLUMN sex;

-- Rename the temporary column to 'sex'
ALTER TABLE clinical_records RENAME COLUMN sex_temp TO sex;
```

### Explanatory data analysis

EDA involved exploring the clinical data to answer key questions such as:
1. Which demographic factors, such as age and gender are represnted in the dataset?

```sql
-- Retrieve percentage distribution of gender
SELECT
    sex,
    COUNT(*) AS total_patients,
    ROUND((COUNT(*) * 100.0 / (SELECT COUNT(*) FROM clinical_records)))::integer AS percentage
FROM clinical_records
GROUP BY sex
ORDER BY sex;
```
```sql
-- Retrieve percentage distribution of age groups
SELECT
    WIDTH_BUCKET(age, 0, 100, 10) * 10 - 9 AS age_from,
    WIDTH_BUCKET(age, 0, 100, 10) * 10 AS age_to,
    COUNT(*) AS total_patients,
    ROUND((COUNT(*) * 100.0 / (SELECT COUNT(*) FROM clinical_records)))::integer AS percentage
FROM clinical_records
GROUP BY age_from, age_to
ORDER BY age_from;
```
   
2. What is the prevalance of conditions like hypertension and diabetes with the follow-up of patients with heart failure?

```sql
-- Calculate the prevalence of hypertension 
SELECT
    hypertension,
    COUNT(*) AS total_patients,
    ROUND((COUNT(*) * 100.0 / (SELECT COUNT(*) FROM clinical_records)))::integer AS prevalence_percentage
FROM clinical_records
GROUP BY hypertension;
```
```sql
-- Calculate the prevalence of diabetes 
SELECT
    diabetes,
    COUNT(*) AS total_patients,
    ROUND((COUNT(*) * 100.0 / (SELECT COUNT(*) FROM clinical_records)))::integer AS prevalence_percentage
FROM clinical_records
WHERE diabetes = true 
GROUP BY diabetes
```
3. How do ejection fraction and serum creatinine levels vary over the follow-up period?

```sql
-- Analyse the variation of ejection fraction and serum creatinine over the follow-up period
SELECT
    AVG(ejection_fraction) AS avg_ejection_fraction,
    AVG(serum_creatinine) AS avg_serum_creatinine
FROM clinical_records
```

4. What is the impact of lifestyle choices, such as smoking habits, during the followu up of heart failure?

```sql
-- Analyse the distribution of smoking habits during the follow-up of heart failure
SELECT
    smoking,
    COUNT(*) AS total_patients,
    ROUND((COUNT(*) * 100.0 / (SELECT COUNT(*) FROM clinical_records)))::integer AS smoking_prevalence
FROM clinical_records
GROUP BY smoking
```

6. What is the minimum, maximum and average follow up time in days after a heart failure?

```sql
SELECT
    MIN(follow_up_days) AS min_follow_up_days,
    MAX(follow_up_days) AS max_follow_up_days,
    AVG(follow_up_days) AS avg_follow_up_days
FROM clinical_records
```


8. What is the overall survival rate among heart failure patients and men vs women during the follow-up period?

```sql
-- Calculates overall survival rate and death rate and compare rates between men and women
SELECT
    sex,
    COUNT(*) AS total_patients,
    SUM(CASE WHEN death_event = false THEN 1 ELSE 0 END) AS survivors,
    SUM(CASE WHEN death_event = true THEN 1 ELSE 0 END) AS deaths,
    ROUND((SUM(CASE WHEN death_event = false THEN 1 ELSE 0 END) * 100.0 / COUNT(*)))::integer AS survival_rate,
    ROUND((SUM(CASE WHEN death_event = true THEN 1 ELSE 0 END) * 100.0 / COUNT(*)))::integer AS death_rate
FROM clinical_records
GROUP BY sex
ORDER BY sex;
---

### Results/findings

There were more males(65%) than females(35%). 



   
