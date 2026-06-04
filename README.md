# Introduction
Welcome to my SQL Portfolio Project, where I explore the data job market with a focus on Data Analyst roles. Using SQL, I analyzed job posting data to identify:

Top-paying data analyst jobs Most in-demand skills Skills linked to higher salaries The best skills for both demand and pay check them out here: project_sql folder

## Background
The demand for data analysts has grown rapidly as companies rely more on data to make business decisions. This project explores the data job market using SQL to better understand which skills are most valuable, which roles offer the highest salaries, and how skill demand affects pay in the data analytics industry.

## Tools I Used
The following tools and technologies were used throughout this project:

- SQL (Structured Query Language): Used to query, filter, and analyze the job posting dataset to uncover meaningful insights.
PostgreSQL : Served as the relational database management system for storing and managing the data.
- Visual Studio Code : Used as the primary development environment for writing and executing SQL queries.
- Git & GitHub : Used for version control and project management.
## The Analysis
Each query in this project was designed to investigate a specific aspect of the data analyst job market. Through SQL analysis, I explored salary trends, skill demand, and the relationship between technical skills and earning potential.

The project focused on identifying high-paying data analyst roles, the skills required for those positions, the most in-demand technologies, and the skills that provide the best combination of market demand and salary potential.

### Top Paying Data Analyst Job
To identify the highest-paying data analyst roles, I filtered job postings based on average yearly salary and focused specifically on remote positions. This analysis highlighted the top-paying opportunities available within the data analytics field.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS comapny_name
FROM
    job_postings_fact
    LEFT JOIN company_dim ON company_dim.company_id = job_postings_fact.company_id
WHERE
     job_title = 'Data Analyst'
     AND job_location = 'United Kingdom'
     AND salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
    LIMIT 10;
```
| job_id  | job_title    | job_location   | job_schedule_type       | salary_year_avg | job_posted_date     | company_name |
| ------- | ------------ | -------------- | ----------------------- | --------------- | ------------------- | ------------ |
| 227038  | Data Analyst | United Kingdom | Full-time and Temp work | 77,017.50       | 2023-12-11 07:47:42 | Nominet      |
| 166362  | Data Analyst | United Kingdom | Full-time               | 53,014.00       | 2023-05-15 19:53:53 | GWI          |
| 1185135 | Data Analyst | United Kingdom | Full-time               | 30,000.00       | 2023-10-24 16:14:26 | Humanity     |

### Skills for Top Paying Jobs
To identify the skills required for top-paying data analyst roles, I joined the job postings data with the skills dataset. This analysis provided insight into the technical skills and tools most valued by employers offering high-compensation positions.

```sql
SELECT
    skills_dim.skills,
    ROUND(avg(job_postings_fact.salary_year_avg)),2 AS avg_salary
FROM
    job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
GROUP BY
    skills_dim.skills
ORDER BY
    avg_salary DESC;
```
| job_id  | job_title    | job_location | salary_year_avg | company_name                            | skills   |
|---------|--------------|--------------|------------------|-----------------------------------------|----------|
| 712473  | Data Analyst | Anywhere     | 165,000.00       | Get It Recruit - Information Technology | sql      |
| 712473  | Data Analyst | Anywhere     | 165,000.00       | Get It Recruit - Information Technology | python   |
| 712473  | Data Analyst | Anywhere     | 165,000.00       | Get It Recruit - Information Technology | r        |
| 712473  | Data Analyst | Anywhere     | 165,000.00       | Get It Recruit - Information Technology | sas      |
| 712473  | Data Analyst | Anywhere     | 165,000.00       | Get It Recruit - Information Technology | matlab   |
| 712473  | Data Analyst | Anywhere     | 165,000.00       | Get It Recruit - Information Technology | pandas   |
| 712473  | Data Analyst | Anywhere     | 165,000.00       | Get It Recruit - Information Technology | tableau  |
| 712473  | Data Analyst | Anywhere     | 165,000.00       | Get It Recruit - Information Technology | looker   |
| 712473  | Data Analyst | Anywhere     | 165,000.00       | Get It Recruit - Information Technology | sas      |
| 1246069 | Data Analyst | Anywhere     | 165,000.00       | Plexus Resource Solutions               | python   |

### In-Demand Skills for Data Analysts
This query was used to identify the skills most frequently requested in data analyst job postings, helping highlight the technologies and tools that are currently in highest demand within the job market.

```sql

SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS skill_count
FROM
    job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = True
GROUP BY
    skills
ORDER BY
    skill_count DESC
LIMIT 5;
```
| skills    | skill_count |
|-----------|-------------|
| sql       | 7,291       |
| excel     | 4,611       |
| python    | 4,330       |
| tableau   | 3,745       |
| power bi  | 2,609       |

### Skills Based on Salary
This analysis explored the average salaries associated with different skills, helping identify the technologies and tools linked to higher-paying data analyst roles.

```sql
WITH top_paying_job AS(
    SELECT
        job_id,
        job_title,
        job_location,
        salary_year_avg,
        name AS comapny_name
    FROM
        job_postings_fact
        LEFT JOIN company_dim ON company_dim.company_id = job_postings_fact.company_id
    WHERE
        job_title = 'Data Analyst'
        AND job_location = 'Anywhere'
        AND salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
        LIMIT 10
)
SELECT
    top_paying_job.*,
    skills
FROM
    top_paying_job
    INNER JOIN skills_job_dim ON top_paying_job.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```

### Most Optimal Skills to Learn
By combining insights from both salary and demand analysis, this query identified the skills that offer the best balance between high market demand and strong earning potential, providing valuable guidance for strategic skill development.

```sql

 WITH skills_demand AS(
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(job_postings_fact.job_id) AS job_count
    FROM
        job_postings_fact
        INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
        INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst' AND
        salary_year_avg IS NOT NULL
        AND job_work_from_home = true
    GROUP BY
        skills_dim.skill_id
 ),

  average_salary AS(
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        AVG(job_postings_fact.salary_year_avg) AS avg_salary
    FROM
        job_postings_fact
        INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
        INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = true 
    GROUP BY
        skills_dim.skill_id,
        skills_dim.skills
 )

 SELECT
    skills_demand.skills,
    skills_demand.job_count,
    average_salary.avg_salary
FROM
    skills_demand
    INNER JOIN average_salary ON average_salary.skill_id = skills_demand.skill_id
ORDER BY
    skills_demand.job_count DESC,
    average_salary.avg_salary DESC
LIMIT 10;
```

| Skill      | Job Count | Avg Salary |
| ---------- | --------: | ---------: |
| SQL        |       398 |     97,237 |
| Excel      |       256 |     87,288 |
| Python     |       236 |    101,397 |
| Tableau    |       230 |     99,288 |
| R          |       148 |    100,499 |
| Power BI   |       110 |     97,431 |
| SAS        |        63 |     98,902 |
| SAS        |        63 |     98,902 |
| PowerPoint |        58 |     88,701 |
| Looker     |        49 |    103,795 |


## What I Learned
Throughout this project, I strengthened several important SQL and analytical skills, including:

- Complex Query Construction: Writing advanced SQL queries using multiple table joins and Common Table Expressions (CTEs) to solve analytical problems
- Data Aggregation and Analysis: Using functions such as COUNT(), AVG(), and GROUP BY to summarize and analyze large datasets effectively. 
- Analytical Thinking: Translating business and career-related questions into SQL queries to uncover meaningful insights from data. Data-Driven Decision Making: Identifying trends in salaries, skill demand, and job opportunities through structured data analysis.

## **Conclusions**

This project strengthened my SQL capabilities while providing meaningful insights into the data analyst job market. The analysis highlights which skills are most in demand and how they relate to salary levels, offering a practical foundation for prioritizing skill development and guiding job search strategies.

For aspiring data analysts, these findings emphasize the importance of focusing on high-impact, in-demand tools and technologies to remain competitive in the field. Overall, this work underscores the value of continuous learning and adaptability in response to evolving industry trends within data analytics.
