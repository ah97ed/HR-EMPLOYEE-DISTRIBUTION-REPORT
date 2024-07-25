 # HR-EMPLOYEE-DISTRIBUTION-REPORT
 ![Dashbord pic](https://github.com/user-attachments/assets/3a41025b-e5ba-4cb6-9f2f-87f4e8de750b)

## Project Overview

This project involves the development of an HR dashboard using MySQL for data cleaning and analysis and Power BI for data visualization. The dashboard is designed to provide insights into various aspects of employee demographics, employment trends, and organizational metrics using HR data collected from the year 2000 to 2020. The dataset includes over 22,000 rows, covering multiple facets of the HR landscape such as employee demographics, employment status, and tenure.

## Data Sources

The dataset used in this project is sourced from the company's HR records spanning 20 years, from 2000 to 2020. The data encompasses a wide range of information including employee demographics, employment dates, termination details, and job-related attributes.

## Objectives

The main objectives of the HR Dashboard project are to:

1. Understand Employee Demographics: Analyze the gender and race/ethnicity distribution within the company.
2. Examine Employment Trends: Study the age distribution, remote versus on-site work status, and changes in employee count over time.
3. Evaluate Turnover and Tenure: Assess turnover rates across departments and average employment lengths, particularly for terminated employees.
4. Departmental Analysis: Investigate gender distribution across departments and job titles, and understand the distribution of job roles within the company.
5. Geographical Distribution: Analyze the distribution of employees across different locations and states.

## Tools
- My SQL - Data Cleaning and Data Analysis
- Power BI - Creating Repotrs

## Data Cleaning / Preparation

In the initial data preparation phase, we proformed the following tasks:

- Data loding and inspaction.
- handling the missing values.
- Rename id column to emp_id.
- Change birthdate, hire_date and termdate column datatype.
- Add new cloumn called Age.

## Exploratory Data Analysis

EDA involved exploring the HR data to answer key questions, such as:

1. What is the gender breakdown of employees in the company?
2. What is the race/ethnicity breakdown of employees in the company?
3. What is the age distribution of employees in the company?
4. How many employees work at headquarters versus remote locations?
5. What is the average length of employment for employees who have been terminated?
6. How does the gender distribution vary across departments and job titles?
7. What is the distribution of job titles across the company?
8. Which department has the highest turnover rate?
9. What is the distribution of employees across locations by state?
10. How has the company's employee count changed over time based on hire and term dates?
11. What is the tenure distribution for each department?

## Data Analysis

We answer the key questions by using SQL programing languge.

```sql
-- Q1. What is the gender breakdown of employees in the company?
SELECT gender, COUNT(*) AS count
FROM hr
WHERE age >= 18
GROUP BY gender;

-- Q2. What is the race/ethnicity breakdown of employees in the company?
SELECT race, COUNT(*) AS Employee_NUM
FROM hr
WHERE age >= 18
GROUP BY race
ORDER BY Employee_NUM DESC;
-- Q3. What is the age distribution of employees in the company?

SELECT 
  MIN(age) AS youngest,
  MAX(age) AS oldest
FROM hr
WHERE age >= 18;


SELECT FLOOR(age/10)*10 AS age_group, COUNT(*) AS count
FROM hr
WHERE age >= 18
GROUP BY FLOOR(age/10)*10;

SELECT 
  CASE 
    WHEN age >= 18 AND age <= 24 THEN '18-24'
    WHEN age >= 25 AND age <= 34 THEN '25-34'
    WHEN age >= 35 AND age <= 44 THEN '35-44'
    WHEN age >= 45 AND age <= 54 THEN '45-54'
    WHEN age >= 55 AND age <= 64 THEN '55-64'
    ELSE '65+' 
  END AS age_group, 
  COUNT(*) AS count
FROM 
  hr
WHERE 
  age >= 18
GROUP BY age_group
ORDER BY age_group;

SELECT 
  CASE 
    WHEN age >= 18 AND age <= 24 THEN '18-24'
    WHEN age >= 25 AND age <= 34 THEN '25-34'
    WHEN age >= 35 AND age <= 44 THEN '35-44'
    WHEN age >= 45 AND age <= 54 THEN '45-54'
    WHEN age >= 55 AND age <= 64 THEN '55-64'
    ELSE '65+' 
  END AS age_group, gender,
  COUNT(*) AS count
FROM 
  hr
WHERE 
  age >= 18
GROUP BY age_group, gender
ORDER BY age_group, gender;

-- Q4. How many employees work at headquarters versus remote locations?

SELECT location, COUNT(*) as count
FROM hr
WHERE age >= 18 and termdate=''
GROUP BY location;



-- Q5. What is the average length of employment for employees who have been terminated?

SELECT ROUND(AVG(DATEDIFF(termdate, hire_date))/365,0) AS avg_length_of_employment
FROM hr
WHERE termdate <> '' AND termdate <= CURDATE() AND age >= 18;

-- Q6. How does the gender distribution vary across departments and job titles?

SELECT department, gender, COUNT(*) as Employee_NUM
FROM hr
WHERE age >= 18
GROUP BY department, gender
ORDER BY department;

-- Q7. What is the distribution of job titles across the company?

SELECT jobtitle, COUNT(*) as Employee_NUM
FROM hr
WHERE age >= 18
GROUP BY jobtitle
ORDER BY jobtitle DESC;

-- Q8. Which department has the highest turnover rate?

SELECT department, COUNT(*) as total_count, 
    SUM(CASE WHEN termdate <= CURDATE() AND termdate <> '' THEN 1 ELSE 0 END) as terminated_count, 
    SUM(CASE WHEN termdate = '' THEN 1 ELSE 0 END) as active_count,
    (SUM(CASE WHEN termdate <= CURDATE() THEN 1 ELSE 0 END) / COUNT(*)) as termination_rate
FROM hr
WHERE age >= 18
GROUP BY department
ORDER BY termination_rate DESC;


-- Q9. What is the distribution of employees across locations by city and state?

SELECT location_state, COUNT(*) as count
FROM hr
WHERE age >= 18  and termdate=''
GROUP BY location_state
ORDER BY count DESC;

-- Q10. How has the company's employee count changed over time based on hire and term dates?
SELECT 
    YEAR(hire_date) AS year, 
    COUNT(*) AS hires, 
    SUM(CASE WHEN termdate <> '' AND termdate <= CURDATE() THEN 1 ELSE 0 END) AS terminations, 
    COUNT(*) - SUM(CASE WHEN termdate <> '' AND termdate <= CURDATE() THEN 1 ELSE 0 END) AS net_change,
    ROUND(((COUNT(*) - SUM(CASE WHEN termdate <> '' AND termdate <= CURDATE() THEN 1 ELSE 0 END)) / COUNT(*) * 100),2) AS net_change_percent
FROM 
    hr
WHERE age >= 18
GROUP BY 
    YEAR(hire_date)
ORDER BY 
    YEAR(hire_date) ASC;


-- Q11. What is the tenure distribution for each department?

SELECT department, ROUND(AVG(DATEDIFF(CURDATE(), termdate)/365),0) as avg_tenure
FROM hr
WHERE termdate <= CURDATE() AND termdate <> '' AND age >= 18
GROUP BY department;

```

## Results / Findings

- There are more male employees
- White race is the most dominant while Native Hawaiian and American Indian are the least dominant.
- The youngest employee is 20 years old and the oldest is 57 years old
- 5 age groups were created (18-24, 25-34, 35-44, 45-54, 55-64). A large number of employees were between 35-44 followed by 25-34  while the smallest group was 55-64.
- A large number of employees work at the headquarters versus remotely.
- The average length of employment for terminated employees is around 7 years.
- The gender distribution across departments is fairly balanced but there are generally more male than female employees.
- The Marketing department has the highest turnover rate followed by Training. The least turn over rate are in the Research and development, Support and Legal departments.
- A large number of employees come from the state of Ohio.
- The net change in employees has increased over the years.
- The average tenure for each department is about 8 years with Legal and Auditing having the highest and Services, Sales and Marketing having the lowest.

## Limitations

- Some records had negative ages and these were excluded during querying(967 records). Ages used were 18 years and above.
- Some termdates were far into the future and were not included in the analysis(1599 records). The only term dates used were those less than or equal to the current date.














