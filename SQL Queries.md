# SQL Queries for Lung Cancer Data Analysis

## Basic Level

1. **Retrieve all records for individuals diagnosed with lung cancer.**  
   ```
   select * from lung_cancer_data
   where Lung_Cancer_Diagnosis = "Yes";
   ```

2. **Count the number of smokers and non-smokers.**  
   ```
   select smoker, count(*) as count_of_people
   from lung_cancer_data
   group by 1;
   ```

3. **List all unique cancer stages present in the dataset.**  
   ```
   select distinct(Cancer_Stage) as stages
   from lung_cancer_data
   where Cancer_Stage != "None";
   ```

4. **Retrieve the average number of cigarettes smoked per day by smokers.**  
   ```
   select round(avg(Cigarettes_per_Day)) as avg_number_of_Cigarettes_per_day
   from lung_cancer_data
   where Smoker = "Yes";
   ```

5. **Count the number of people exposed to high air pollution.**  
   ```
   select Air_Pollution_Exposure , count(*) as people_count
   from lung_cancer_data
   where Air_Pollution_Exposure = "High";
   ```

6. **Find the top 5 countries with the highest lung cancer deaths.**  
   ```
   select Country , sum(Annual_Lung_Cancer_Deaths) as total_deaths
   from lung_cancer_data
   group by 1
   order by 2 desc
   limit 5;
   ```

7. **Count the number of people diagnosed with lung cancer by gender.**  
   ```
   select Gender , count(Lung_Cancer_Diagnosis) as total_diagnoised
   from lung_cancer_data
   where Lung_Cancer_Diagnosis = "Yes"
   group by 1;
   ```

8. **Retrieve records of individuals older than 60 who are diagnosed with lung cancer.**  
   ```
   select * from lung_cancer_data
   where Age>60 and Lung_Cancer_Diagnosis = "Yes";
   ```

---

## Intermediate Level

1. **Find the percentage of smokers who developed lung cancer.**  
   ```
   with t1 as (select * from lung_cancer_data
   where Smoker = "Yes")
   select concat(round(count(Lung_Cancer_Diagnosis)/(select count(*) from t1)*100,2),"%") 
   as cancer_pct_out_of_smkrs
   from t1 
   where Lung_Cancer_Diagnosis  = "Yes";
   ```

2. **Calculate the average survival years based on cancer stages.**  
   ```
   select Cancer_Stage , round(avg(Survival_Years),1) as avg_survival_years
   from lung_cancer_data
   where Cancer_Stage != "None"
   group by 1;
   ```

3. **Count the number of lung cancer patients based on passive smoking.**  
   ```
   select Passive_Smoker , count(Lung_Cancer_Diagnosis) as cancer_diagnoised
   from lung_cancer_data
   where Lung_Cancer_Diagnosis = "Yes"
   group by 1;
   ```

4. **Find the country with the highest lung cancer prevalence rate.**  
   ```
   select Country , round(avg(Lung_cancer_Prevalence_Rate),3) as avg_prevalence_rate
   from lung_cancer_data
   group by 1
   order by 2 desc
   limit 1;
   ```

5. **Identify the smoking years' impact on lung cancer.**  
   ```
   with t1 as (select Years_of_Smoking , count(Smoker) as cancered_smokers
   from lung_cancer_data
   where Lung_Cancer_Diagnosis = "Yes" and Smoker = "Yes"
   group by 1
   order by 1 asc),
   t2 as (select Years_of_Smoking , count(Smoker) as total_smokers
   from lung_cancer_data
   where Smoker = "Yes"
   group by 1
   order by 1 asc)
   select t1.Years_of_smoking , t1.cancered_smokers , t2.total_smokers , concat(round((t1.cancered_smokers/t2.total_smokers)*100,2),"%") as cancered_pct
   from t1 join t2 on t1.Years_of_Smoking = t2.Years_of_Smoking
   group by 1
   order by 1;
   ```

6. **Determine the mortality rate for patients with and without early detection.**  
   ```
   select Early_Detection , round(avg(Mortality_Rate),2) as average_mortality_rate
   from lung_cancer_data
   group by 1;
   ```

7. **Group the lung cancer prevalence rate by developed vs. developing countries.**  
   ```
   select Developed_or_Developing ,
   round(avg(Lung_Cancer_Prevalence_Rate),2) as average_prevalence_rate
   from lung_cancer_data
   group by 1;
   ```

---


## Advanced Level

1. **Identify the correlation between lung cancer prevalence and air pollution levels.**  
   ```
   select Air_Pollution_Exposure , round(avg(Lung_Cancer_Prevalence_Rate),4) as Average_Cancer_Prevalence_Rate
   from lung_cancer_data
   group by 1;
   ```

2. **Find the average age of lung cancer patients for each country.**  
   ```
   select Country , round(avg(Age)) as avg_age
   from lung_cancer_data
   where Lung_Cancer_Diagnosis = "Yes"
   group by 1
   order by 1;
   ```

3. **Calculate the risk factor of lung cancer by smoker status, passive smoking, and family history.**  
   ```
   with t1 as (
   select Smoker , Passive_Smoker, Family_History ,  
   Case  
   when Lung_Cancer_Diagnosis = "Yes" then 1  
   when Lung_Cancer_Diagnosis = "No" then 0  
   end as Lung_Cancer  
   from lung_cancer_data)
   select Smoker, Passive_Smoker, Family_History,  
   (SUM(Lung_Cancer)/ COUNT(*))*100 AS Risk_Factor_Percentage  
   from t1  
   group by Smoker, Passive_Smoker, Family_History  
   order by Risk_Factor_Percentage desc;
   ```

4. **Rank countries based on their mortality rate.**  
   ```
   select Country , round(avg(Mortality_rate),2) as Avg_mortality_rate ,
   rank() over (Order by avg(Mortality_rate) desc) as ranks
   from lung_cancer_data
   group by 1;
   ```

5. **Determine if treatment type has a significant impact on survival years.**  
   ```
   select Treatment_Type , round(avg(Survival_Years),1) as avg_survival_years
   from lung_cancer_data
   group by 1
   order by 2 desc;
   ```



6. **Compare lung cancer prevalence in men vs. women across countries.**  
   ```
   select Country , Gender ,
   round(avg(Lung_Cancer_Prevalence_Rate),2) as avg_prevalence_rate
   from lung_cancer_data
   group by 1,2
   order by 1,2;
   ```

7. **Find how occupational exposure, smoking, and air pollution collectively impact lung cancer rates.**  
   ```
   with t1 as (
   select Occupational_Exposure, Smoker, Air_Pollution_Exposure ,
   Case 
   when Lung_Cancer_Diagnosis = "Yes" then 1
   when Lung_Cancer_Diagnosis = "No" then 0
   end as Lung_Cancer
   from lung_cancer_data)
   select Occupational_Exposure, Smoker, Air_Pollution_Exposure,
   round((SUM(Lung_Cancer)/ COUNT(*))*100,2) 
   as Lung_Cancer_Percentage
   from t1
   group by Occupational_Exposure, Smoker, Air_Pollution_Exposure
   order by Lung_Cancer_Percentage desc;
   ```

8. **Analyze the impact of early detection on survival years.**  
   ```
   select Cancer_Stage ,Early_detection , round(avg(Survival_Years),1) as average_survival_years
   from lung_cancer_data
   where Survival_Years is not null and Cancer_Stage != "None"
   group by 1,2
   order by 1,2;
   ```

