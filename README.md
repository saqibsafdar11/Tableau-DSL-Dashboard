## Tableau Dashboard to Evaluate Digital SKills Workshops

Summary:

In this project Tableau prep is used to design and implement the entity dependent diagram below

![Database ER Diagram](/Database ER diagram (crow's foot).jpeg)

Data was filtered, joined and cleaned. Two different databse sources were joined
a) Data for Qualtrics which containes student survey data and their feedback on having attended workshops. Each academeic year had to be combined with the another. 
b) Data for from Oracle database which contains all the booking data 


![Tableau Prep Diagram](/Tableauprepdiag.jpg)

Filtes: only bookings from 2018 and above were taken: YEAR([E_SESSION_START_TIME]) >= 2018
Clean: Pre-fix were added to each table e.g. e_session or event_
Key 
Pre-fix were

Term dates were defined as:
IF MONTH([Start Date])>=9 AND MONTH([Start Date])<=12 THEN 'michaelmas term'
ELSEIF MONTH([Start Date])>=1 and MONTH([Start Date])<=4 then 'lent term'
ELSEIF month([Start Date])>=5 and MONTH([Start Date])<=7 then 'summer term'
END

Coursname cleaned:
REGEXP_REPLACE([Coursename], "[^A-Z a-z 0-9 :]+", " ")

Lowercase to allow joining with other data sources: LOWER([course name cleaned])

Coursename arranged into programmes:
IF CONTAINS([course name cleaned], 'presessional') 
OR CONTAINS([course name cleaned], 'machine') 
OR CONTAINS([course name cleaned], 'methodology') 
OR CONTAINS([course name cleaned], 'python for accounting') 
OR CONTAINS([course name cleaned], 'python for finance') 
THEN 'data science academic support'


ELSEIF CONTAINS([course name cleaned], 'python') 
OR CONTAINS([course name cleaned], 'r fundamentals') 
OR CONTAINS([course name cleaned], 'r data ') 
OR CONTAINS([course name cleaned], 'tableau') 
OR CONTAINS([course name cleaned], 'working with tabular') 
OR CONTAINS([course name cleaned], 'sql') 
THEN 'data science tools and coding'

Academic Year defined as
IF LEN([year]) = 7 THEN
    LEFT([year], 4) + "-" + "20" + RIGHT([year], 2)
ELSE
    [year]
END

Key joinin the survey data and booking data defined as 
[Programme name]+' '+[Academic Year Survey]+' '+[Term]

Owing to some issues with tableau server, an ALteryx solution is also being considered. 


![Alteryx Diagram](/Alteryxdiagram.jpg)

Another option will be run an SQL code to process the data flow. 

The following Tableau dashboard is based on randomised/incomplete data 


![Tableau Dashboard](/Dashboard.png)


