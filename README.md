# Dashboard For HR Department Manager

# Introduction
📊 Exploring employee data to find key staff metrics which include: 💰 Average Salary, 🔥 Top Earners , 📈 Headcount By Location and 📅 Headcount By Department.

🔍 Interactive Dashboard? Check it out here: [Dashboard](https://github.com/stephenjbarratt/Dashboard_For_HR_Department_Manager/blob/4079a8490dac14bd6480e72762299512491f0997/Dashboard.gif)

# Background
In this project I took on the role of a member of the HR Department for an American Company. The manager has asked for a report to summarise staff metrics at the company, to help with future planning and decision-making.

The project contains employee data, with insights on employee names, salary, department, employment type and location. The data itself comes from the Pivot Table Mastery Course on XelPlus [XelPlus course](https://www.xelplus.com/course/excel-pivot-tables/).

### The requirements I wanted to include in the report are:
1. Total headcount
2. Total FTE
3. Average Salary
4. Top Earner Salary
5. Headcount by Department
6. Headcount by Employment type
7. Top 10 earners by Department
8. Employees by Department and Location


# Tools I Used
To explore the employee dataset I used several tools:
* **Git & GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.
* **Excel:** For storytelling the data through charts.


# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here’s how I approached each question:

## 1. Highest Paid Data Analyst Jobs
To identify the highest paid jobs, I filtered data analyst positions by averaging yearly salary and location, focusing on jobs in the United Kingdom.
``` SQL
SELECT
    job_id,
    job_title,
    job_country,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    company_dim.name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_country = 'United Kingdom' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT

```

Analysis of results of the Top 10 Highest Paid Data Analytics jobs in the UK (2023):
- **Wide Salary Range:** Yearly Salaries range from $180,000 to $132,500 with top employers being large banks and corperations such as Deutsche Bank and Shell
- **Location:** Majority of the highest paid jobs are located in London. Only one of the top 10 was remote working.
- **Job title:** A job that appeared the most was Data Architect with 4 of the 10 employed in this particular role.


![image alt](https://github.com/stephenjbarratt/SQL_Project_Data_Analysis_Jobs/blob/971215a98e3de15d682d3209487c35e667ef3939/Charts/Bar%20Graph.png)

*Figure 1: Bar Graph Visualising the Top 10 Highest Paid Data Analyst Jobs in the Uk in 2023*


## 2. Skills for highest paid jobs
Next let's take it a step further from the highest paying roles from the last query and now find the most commonly used skills associated with these roles. By knowing these skills, it will help to identify what skills to develop to achieve these higher paid roles. I used the following query to get this information.

``` SQL
WITH highest_paid_jobs AS (
SELECT
    job_id,
    job_title,
    salary_year_avg,
    job_posted_date,
    company_dim.name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_location = 'United Kingdom' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10
)
SELECT 
highest_paid_jobs.*,
skills
FROM highest_paid_jobs
INNER JOIN skills_job_dim ON highest_paid_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC
```
Analysis of the most commonly used skills in the 10 highest paid jobs in the UK, in 2023.
- Excel and Python lead the way with 5 of the jobs using these fundamential skills.
- Tableau and SQL follow with 3 of the jobs using these skills.

![image alt](https://github.com/stephenjbarratt/SQL_Project_Data_Analysis_Jobs/blob/457a30239c9190c1abfc7166f9624f01ebd48014/Charts/Bar%20Graph%20For%20Skills.png)

*Figure 2: Bar Graph visualising the count of skills for the 10 highest paid jobs in data analysis in the UK, 2023.*


## 3. In Demand Skills for Data Analysis
The next query helped to identify the skills with the highest demand, according to job postings in the United Kingdom (2023).

``` SQL
SELECT
skills,
COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postingS_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_country = 'United Kingdom'
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5
```

Analysis of results of the 5 most in demand skills for data analysis roles in the UK (2023)
- SQL (4480) and Excel (4281) are first and second respecively, emphasising the need for foundational skills in data processing and spreadsheet manipulation.
- Programming and Visualisation tools such as Python, Power BI and Tableau are also important, showing the need for technical skills to carry out data storytelling.

![image alt](https://github.com/stephenjbarratt/SQL_Project_Data_Analysis_Jobs/blob/c3a3a39a8da18d8fe5065b775f614882cdcdd49e/Charts/Table%20Of%20Skills.png)

*Figure 3: Table displaying the 5 most in demand skills for data analysis jobs in the UK (2023)*

## 4. Months with the highest job postings
The following query shows the months of the year where job postings for data analysis jobs are at there highest and lowest, helping job seekers effectively time appliactions.
``` SQL
SELECT 
    EXTRACT(MONTH FROM job_posted_date) AS month,
    COUNT(CASE WHEN job_country = 'United Kingdom' THEN job_id END) AS number_of_postings_UK
FROM job_postings_fact
WHERE job_title_short = 'Data Analyst'
AND EXTRACT(YEAR FROM job_posted_date) = 2023
GROUP BY month
ORDER BY month;
```
Analysis of which months have the most data analysis job postings in the UK (2023)
- January stands out as the month with the highest amount of job postings, likely due to companies hiring for the new year.
- There is a sharp increase in the number of job postings in September and October, possibly influenced by organisational needs for Q4 and also an intake of graduates.

![image alt](https://github.com/stephenjbarratt/SQL_Project_Data_Analysis_Jobs/blob/d12e87e50861faccda67935daa8d6922f5989998/Charts/Line%20Graph%20For%20Job%20Frequency.png)

*Figure 4: Line Graph visualising the number of job postings are listed each month of the year for data analysis roles in the UK for the year 2023.*

# Troubleshooting
During the course of the project I did come across some issues that I had to troubleshoot. This section lists the main problem and the solution, the purpose is to be able to look back at this if I ever come across this error again.
## Problem:
When loading the data into the database, I ran the active connection and received the error messages, shown in Figure 5, in Visual Studio Code.
The reason for the error messages was because when I was trying to run a copy command to specify a file path, where I want the data to go in the database, PostgreSQL didn't have access to this file. The project also can't be stored in the Cloud or Onedrive when loading the data, so I moved it to a local storage on my machine.

![image alt](https://github.com/stephenjbarratt/SQL_Project_Data_Analysis_Jobs/blob/6ac3321a3a794b5d191b25900050efd80c7c7da9/Charts/Error%20messages.png)

*Figure 5: Error messages*

## Solution:
The solution to this problem:
1. Drop the database as it is most likely corrupted.
2. Repeat steps to create the database and load table schema.
3. Open pgAdmin, find the database, right click select PSQL Tool, this opens a terminal window to write the following code.
4. Find the absolute path of the csv files, right click on csv files in VS Code and select Copy Path.
5. Paste all of the paths into the PSQL Tool terminal, this will load the data into the database.


# What I Learnt
Throughout this project I have developed a number of skills and techniques to improve my SQL knowledge and ability:
- 🧩 **Complex Queries:** Mastering complex SQL, by merging tables using JOIN and WITH clauses for Common Table Expressions.
- 📊 **Data Aggregation:** Used commands such as GROUP BY and aggregate functions like COUNT() and AVG().
- 💡 **Analytical Skills:** Transformed questions into actonable SQL queries, levelling up my real world problem solving skills.
  
# Conclusions
## Insights
From the analysis, several insights emerged:
1. **Highest Paid Data Analyst Jobs:** The highest paid jobs in the UK as a Data Analyst in 2023 had a wide range of salaries with the highest being $180,000 around £135,000.
2. **Skills For Highest Paid Jobs:** Highest paying data analyst roles demand a need for Excel and Python, suggesting they are key skills for a top earning salary.
3. **Most In-Demand Skills:** SQL and Excel are the most in demand skills, suggesting they are essential for anyone wanting to get a job as a data analyst.
4. **When Are Data Analyst Job Postings Most Frequent:** The largest demand for data analyst jobs in the UK, in 2023 was in January. There was also a spike in September and October. These are key months for anyone looking to find the right time to apply for a role.

## Closing Thoughts
This project really helped to improve my SQL skills and overa
