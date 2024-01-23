## Tableau Dashboard to Evaluate Digital Skills Lab Workshops

Summary:

In this project Tableau prep is used to design and implement the entity dependent diagram below

https://lucid.app/documents/view/cd93b7e4-ece8-4868-8101-f7a868b86377


Data was filtered, joined and cleaned. Two different databse sources were joined
a) Data for Qualtrics which containes student survey data and their feedback on having attended workshops. Each academeic year had to be combined with the another. 
b) Data for from Oracle database which contains all the booking data 


![Tableau Prep Diagram](/Tableauprepdiag.jpg)



Due to some of the load put on the Tableau server, an ALteryx solution was also considered. 


![Alteryx Diagram](/Alteryxdiagram.jpg)

Tableau Iteration 1.0
The following Tableau dashboard is based on randomised/incomplete data 



![Tableau Dashboard](/Tableaudashboard.jpg)


The issue was resolved by re-designing the Tableau flow and using a minimalist approach; taking only the fields which are essentially needed.

![Tableau Dashboard](/Tableaudashboard.jpg)


Tableau Prep Calculations

Term

IF MONTH([SESSION_SESSION_START_TIME])>=9 AND MONTH([SESSION_SESSION_START_TIME])<=12 THEN 'Autumn Term'
ELSEIF MONTH([SESSION_SESSION_START_TIME])>=1 AND MONTH([SESSION_SESSION_START_TIME])<=4 THEN 'Spring Term'
ELSEIF MONTH([SESSION_SESSION_START_TIME])>=5 AND MONTH([SESSION_SESSION_START_TIME])<=8 THEN 'Summer Term'
END

Academic Year

IF DATETRUNC('month', [SESSION_SESSION_START_TIME]) >= DATE('2022-09-01') 
   AND DATETRUNC('month', [SESSION_SESSION_START_TIME]) <= DATE('2023-08-31') 
THEN '2022-2023'
ELSEIF DATETRUNC('month', [SESSION_SESSION_START_TIME]) >= DATE('2023-09-01') 
   AND DATETRUNC('month', [SESSION_SESSION_START_TIME]) <= DATE('2024-08-31') 
THEN '2023-2024'
END



Calculations in Tableau

#attended

COUNTD (IF [SESSION_ATTENDANCE]='Attended' AND [SESSION_BOOKING_STATUS] = 'CONFIRMED'
THEN STR([BOOKING_EVENT_ID])+STR([BOOKING_PERSON_ID])
END)

#attended Unrecorded

COUNTD( IF (([BOOKING_STATUS]='CONFIRMED')AND([SESSION_ATTENDANCE]='Unrecorded')) 
THEN STR([EVENT_ID])+STR([BOOKING_PERSON_ID])
END)

#average attendance / session

[#attended]/SUM([Distinct Session Details])

Available places

{ FIXED [EVENT_ID]: MAX([EVENT_MAX_BOOKINGS])}

Distinct Session Details

{ FIXED [EVENT_ID], [SESSION_SESSION_START_TIME]:COUNTD([EVENT_ID])}

Individual students trained

COUNTD (IF [SESSION_ATTENDANCE]='Attended' THEN [BOOKING_PERSON_ID] END)

No Show

[Not Attended]/ [Places booked ]

Not Attended

COUNTD( IF (([BOOKING_STATUS]='CONFIRMED')AND([SESSION_ATTENDANCE]='Not Attended')) 
THEN STR([EVENT_ID])+STR([BOOKING_PERSON_ID] )
END)

Placed booked

COUNTD(IF [SESSION_PROGRAMME]='Workshops'
then [Workshop place check]
ELSE [Places Booked Check]
END)


Definitions used in Tableau

Places booked Check

{FIXED [EVENT_ID], [BOOKING_PERSON_ID],[SESSION_SESSION_START_TIME]: MIN(
IF [BOOKING_STATUS]='CONFIRMED' THEN STR([EVENT_ID])+ STR([BOOKING_PERSON_ID])+STR([SESSION_SESSION_START_TIME])
END)}

Programme breakdown 

IF ([SESSION_PROGRAMME]='Specialist research tools'
 OR [SESSION_PROGRAMME]='Data science tools and coding'
 OR [SESSION_PROGRAMME] ='Productivity tools' 
 OR  [SESSION_PROGRAMME]='Productivity tools for academic support'
 OR [SESSION_PROGRAMME]='Productivity tools for staff'  )
 THEN
 'Workshops'
ELSEIF [SESSION_PROGRAMME]='Events' THEN
'Events' 
ELSEIF ([SESSION_PROGRAMME]='Data science academic support' 
OR  [SESSION_PROGRAMME]='Data science tools for staff')
THEN 'Tailored'
ELSE 'DSL Supported'
END





