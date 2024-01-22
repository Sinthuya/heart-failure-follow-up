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
   
2. What is the prevalance of conditions like high blood pressure and diabetes with the follow-up of patients with heart failure?

```sql
-- Calculate the prevalence of hypertension 
SELECT
    high_blood_pressure,
    COUNT(*) AS total_patients,
    ROUND((COUNT(*) * 100.0 / (SELECT COUNT(*) FROM clinical_records)))::integer AS prevalence_percentage
FROM clinical_records
WHERE high_blood_pressure = true 
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

5. What is the minimum, maximum and average follow up time in days after a heart failure?

```sql
SELECT
    MIN(follow_up_days) AS min_follow_up_days,
    MAX(follow_up_days) AS max_follow_up_days,
    AVG(follow_up_days) AS avg_follow_up_days
FROM clinical_records
```


6. What is the overall survival rate among heart failure patients and men vs women during the follow-up period?

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

The dataset comprising 299 patients with heart failure revealed diverse historical and demographic insights. Patients experienced follow-up periods ranging from a minimum of 4 days to a maximum of 285 days, with an average follow-up duration of 130 days.
In terms of gender distribution, the dataset skewed towards males (65%) compared to females (35%). The age spectrum ranged from 40 to 95 years, with an average age of 61 years. Notably, 35% of patients exhibited high blood pressure, and 42% had diabetes, aligning with recent findings from the American Heart Association, highlighting how common these conditions are in heart failure cases. Additionally, only 14% of patients presented with both high blood pressure and diabetes.
Critical clinical indicators for heart failure, such as ejection fraction and serum creatinine levels, were assessed. The average ejection fraction, measuring the percentage of blood pumped out of the left ventricle with each contraction, was 38%—significantly below the normal range of 50% to 70%. Meanwhile, the average serum creatinine level, an indicator of kidney function, was at 137 micromol/L for both men and women, again showing the potential heart failure or an underlying cardiac condition.
Despite the well-known exacerbating effects of smoking on heart failure symptoms, the dataset revealed that nearly a third of patients (32%) continued smoking post-heart failure.
During the follow-up period, the death rate reached approximately 32% for both men and women, while the survival rate stood at 68%. These figures highlight the significant impact of heart failure on patient outcomes, emphasizing the need for targeted interventions and ongoing research into improving the quality of life


### Recomendations

Based on the analysis I recomend the following:

Develop public health campaigns targeting lifestyle factors, such as smoking habits, to reduce the incidence of heart failure.
Establish a long-term monitoring system to continue tracking trends in heart failure and related factors
Develop education and awareness programs to inform the public about the importance of regular health check-ups, early intervention and followe-ups.

### Limitations

Sample Size: The dataset may have a limited sample size (299 patients), which could affect the generalizability of findings to larger populations.
Limited Variables: The available 13 clinical features may not cover all relevant aspects of heart failure.
Assumption on Death Event Indicator: Additional information on cause of death could provide a more accurate picture.
Follow-Up Period: The length of the follow-up period may be limited, limiting the ability to capture long term trends or outcomes.
Limited Lifestyle Data: The dataset may lack detailed lifestyle information beyond smoking habits, limiting the exploration of broader lifestyle factors that could influence heart failure.


   
