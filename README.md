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

### Explanatory data analysis

EDA involved exploring the clinical data to answer key questions such as:
1. Which demographic factors, such as age and gender, are strongly associated with heart failure incidence?

```sql

   
2. What is the prevalance of conditions like hypertension and diabetes with the heart failure patients?
4. How do ejection fraction and serum creatinine levels vary over the follow-up period?
5. What is the impact of lifestyle choices, such as smoking habits, during the followu up of heart failure?
6. What is the average follow up time in days after a heart failure?
7. What is the overall survival rate among heart failure patients and men vs women during the follow-up period?
   
