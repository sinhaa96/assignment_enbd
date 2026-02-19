# Data Discovery

## Schema Overview:

Total Row Count    : 2946
Total Column Count : 30

 |-------------------------------|-------------|----------------|----------------|----------|--------------|-------------------------------------------------|
 |         Column Name           | Source Type |   Target Type  | Null/NaN Count | Distinct |   Category   |                 Description                     | 
 |-------------------------------|-------------|----------------|----------------|----------|--------------|-------------------------------------------------|
 | Job ID                        | long        |  integer       |        0       |   1661   | Identifier   | All numeric values. Unique job identifier.      |
 | Agency                        | string      |  string        |        0       |     52   | Categorical  | Agency/Department the job is for.               |
 | Posting Type                  | string      |  string        |        0       |      2   | Categorical  | Inter/External Job posting.                     |
 | # Of Positions                | long        |  integer       |        0       |     34   | Numerical    | Number of open positions.                       |
 | Business Title                | string      |  string        |        0       |   1244   | Text         | Agency specific job title/designation.          |
 | Civil Service Title           | string      |  string        |        0       |    312   | Text         | Government classified job title/designation.    |
 | Title Code No                 | string      |  string        |        0       |    323   | Identifier   | Unique code to identify Civil Services Title.   |
 | Level                         | string      |  string        |        0       |     14   | Categorical  | Job experience level.                           |
 | Job Category                  | string      |  string        |        2       |    131   | Categorical  | Job domain. eg. Finance, Health, etc            |
 | Full-Time/Part-Time indicator | string      |  string        |      195       |      3   | Categorical  | F/P indicator for Full and Part time jobs.      |
 | Salary Range From             | double      |  decimal(10,2) |        0       |    519   | Numerical    | Lower bound/starting salary range for job.      |
 | Salary Range To               | double      |  decimal(10,2) |        0       |    658   | Numerical    | Upper bound/final salary range for job.         |
 | Salary Frequency              | string      |  string        |        0       |      3   | Categorical  | Payment frequency (Annual/Daily/Hourly).        |
 | Work Location                 | string      |  string        |        0       |    226   | Text         | Job/Office location.                            |
 | Division/Work Unit            | string      |  string        |        0       |    678   | Categorical  | Division under which the job falls.             |
 | Job Description               | string      |  string        |        0       |   1608   | Text         | Description of the job.                         |
 | Minimum Qual Requirements     | string      |  string        |       20       |    337   | Text         | Required Qualifications.                        |
 | Preferred Skills              | string      |  string        |      393       |   1283   | Text         | Skill set preferred.                            |
 | Additional Information        | string      |  string        |     1092       |    682   | Text         | Additional job info on the skill/qualification. |
 | To Apply                      | string      |  string        |        1       |    894   | Text         | Instructions on how to apply for the job.       |
 | Hours/Shift                   | string      |  string        |     2062       |    182   | Text         | Shift/Job time details.                         |
 | Work Location 1               | string      |  string        |     1588       |    228   | Text         | Office Location.                                |
 | Recruitment Contact           | double      |  DROP          |     2946       |      1   | NO VALUE     | All blank/null values. DROP COLUMN.             |
 | Residency Requirement         | string      |  string        |        4       |     51   | Text         | Residency requirement for the job.              |
 | Posting Date                  | string      |  timestamp     |        4       |    494   | Date Field   | Job posting timestamp.                          |
 | Post Until                    | string      |  timestamp     |     2075       |    106   | Date Field   | Job posting close timestamp.                    |
 | Posting Updated               | string      |  timestamp     |        4       |    488   | Date Field   | Job posting update timestamp.                   |
 | Process Date                  | string      |  timestamp     |        4       |      2   | Date Field   | Job posting processign timestamp.               |
 |-------------------------------|-------------|----------------|----------------|----------|--------------|-------------------------------------------------|

Observations & Suggestions/Assumptions:
     
     1. Job ID uniquely identifies a job posting, combined with ordering on Posting Date can be used to identify and eliminate duplicates.
     2. **nan** values converted to **Unknown** for: [Job Category, Full-Time/Part-Time indicator, Minimum Qual Requirements, Preferred Skills, Additional Information, To Apply, Hours/Shift, Work Location 1, Residency Requirement].
     3. Drop column "Recruitment Contact" as it has only null values hence ads no value to our analysis.
     4. Columns **Job ID** and **# Of Positions** inferred type is long but safe to keep integer.
     5. Columns **Salary Range From** and **Salary Range To** inferred type is double but safe to keep decimal(10,2).
     6. Posting Date, Post Until, Process Date have null values, using coalesce to try and fill the null values:
             a. Posting Date    -> Coalesce(Posting Date, Process Date, Posting Updated)
             b. Posting Updated -> Coalesce(Posting Updated, Posting Date, Process Date)
             c. Process Date    -> Coalesce(Process Date, Posting Date, Posting Updated)
    7. Columns renamed as per below mapping.

 Column mapping:

 |-------------------------------|-----------------------|
 |         Column Name           |        Renamed        |
 |-------------------------------|-----------------------|
 | Job ID                        | job_id                |
 | Agency                        | agency                |
 | Posting Type                  | posting_type          |
 | # Of Positions                | open_positions        |
 | Business Title                | business_title        |
 | Civil Service Title           | civil_service_title   |
 | Title Code No                 | title_code_no         |
 | Level                         | level                 |
 | Job Category                  | job_category          |
 | Full-Time/Part-Time indicator | employment_type       |
 | Salary Range From             | min_salary            |
 | Salary Range To               | max_salary            |
 | Salary Frequency              | salary_frequency      |
 | Work Location                 | work_location         |
 | Division/Work Unit            | division              |
 | Job Description               | job_description       |
 | Minimum Qual Requirements     | min_qualification     |
 | Preferred Skills              | preferred_skills      |
 | Additional Information        | additional_info       |
 | To Apply                      | how_to_apply          |
 | Hours/Shift                   | job_timings           |
 | Work Location 1               | work_location_ext     |
 | Recruitment Contact           | DROPPED               |
 | Residency Requirement         | residency_requirement |
 | Posting Date                  | posting_date          |
 | Post Until                    | closing_date          |
 | Posting Updated               | updated_on            |
 | Process Date                  | process_date          |
 |-------------------------------|-----------------------|
 

Data Wrangling:

    1. Deduplication of data based on Job Id as the unique key.
    2. Casting of columns to appropriate types -> Converting the long type to integer || Converting double salary field to decimal(10,2).
    3. Convert/Flag "nan" values as "Unknown".
    4. Rename of columns to meaningful names.
    5. Modifying categorical values to reflect meaningful terms -> F: Full_Time, P: Part_Time.