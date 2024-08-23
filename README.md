# Dallas City of Learning data analysis
![Screenshot 2024-07-17 013556](https://github.com/user-attachments/assets/a01b6785-e338-43df-a5ff-f95633f5168e)

## Dashboard Link :
https://tinyurl.com/2d6sj2ta

## Problem Statement
- Objective: The purpose of this analysis is to create a dashboard in Power BI for Dallas City of Learning that reflects all relevant metrics to:
  * Top Organizations by program count & capacity
  * Program count by quarter & month
  * Program distribution by participants' age
  * Program distribution by duration
  * Program distribution by multiple factors
  * Program distribution by City
  * Program count by capacity

## Data Sourcing
The Dataset used for this analysis was provided by https://www.dallascityoflearning.org/

## Data Preparation
The dataset was loaded into Microsoft Power BI Desktop for transformation in Power Query and modeling.

### Data Cleaning
Data Cleaning for the dataset was done in Power Query as follows:
- Unnecessary columns were removed
- Each of the columns in the table was validated to have the correct data type
- I left the null values as it ensures that they are excluded from numerical calculations, which might be necessary for accurate analysis numerical columns.
- ‘Zipcode’ column had 18% of errors. So, I changed its datatype to text to handle the errors. Some rows in ‘Zipcode’ column contained extra numbers and special characters. I extracted the valid Zipcode number from that column to make them error free.
- Standardize the column values in all the text columns.
### Data Transformation
- I calculated ‘Program_Duration(Days)’ column from Startdate and Enddate of the programs.
- I calculated ‘Prog_Dur(Months)’ calculated column by using ‘DAX’. I created bins using DAX formula to understand the programs sizes. I created a hierarchy here, so we could drill down. I have shown program count for each bin & user can drill down into each bin to know how many 1 day, 2 days, 3days… programs were conducted.


          Prog_Dur(Months) = SWITCH(TRUE(),('scheduled_programs Dallas'[Program_Duration(Days)]/30)<1,"0-1m",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<2,"1-2",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<3,"2-3m",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<4,"3-4m",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<5,"4-5m",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<6,"5-6m",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<7,"6-7m",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<8,"7-8m",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<9,"8-9m",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<10,"9-10m",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<11,"10-11m",('scheduled_programs Dallas'[Program_Duration(Days)]/30)<12,"11-12m",('scheduled_programs Dallas'[Program_Duration(Days)])<730,"1Year+",'scheduled_programs Dallas'[Program_Duration(Days)]<1095,"2Years+",'scheduled_programs Dallas'[Program_Duration(Days)]<1460,"3Years+","4Years+")
- I extracted StartTime from Start_DateTime format & EndTime for End_DateTime format for all the days to get the ‘Program_Duration’ for all the days. (Sunday_Prog_Duration, Monday_Prog_Duration etc). 
- I created the measure to calculate number of times transportation provided for the programs by the organizations by using DAX.

          No_Of_Times_Transportation_Provided = CALCULATE(COUNT('scheduled_programs Dallas'[Transport Provided]),'scheduled_programs Dallas'[Transport Provided]=1)
- Calculated ‘Program_Duration’ in days for each program from ‘Start_Time’ and ‘End_Time’ columns.
- Converted ‘Program_Duration(Days) in  days to ‘Program_Duration(Months)’ in months uding DAX.

- I understood on which hour/time of the day the programs are active from ‘Start_Time’ & ‘End_Time’ columns. (by using following M-Query formulas). For this, I duplicated the dataset, and removed all other columns except Start Date, End Date, Start Time, End Time, Program Name, Org Name. Then created custom columns for each hour of the day using M-Query language to understand ‘if the programs are active on particular hour’. I created 24-hour columns (for 24 hours). Then I unpivoted 24 columns, so that I can show the values on the graph in a better way.

          if [StartTime1] <= #time (0, 0, 0) and [EndTime1] >= #time (0, 0, 0) then 1 else 0 (for 12AM),
   [StartTime1] <= #time (1, 0, 0) and [EndTime1] >= #time (1, 0, 0) then 1 else 0 (for 1AM) etc.


- I understood what age groups the programs are most suitable for from ‘Min_Age’ & ‘Max_Age’ columns. (From the below M-Query formulas). For this, I duplicated the dataset, and removed all other columns except Program Name, Org Name, Min Age, Max Age. The I created custom columns for each age using M-Query language to understand ‘If the programs are suitable for particular age’. I created 100 columns (for 100 ages). Then I unpivoted 100 columns into 2 columns with variable names and it’s values. So that I can show the values on the graph in a better way.

          if [Min Age] <= 1 and 1 <= [Max Age] then 1 else 0 (1year old)
if [Min Age] <= 2 and 2 <= [Max Age] then 1 else 0 (for 2year old)
- I created ‘Date’ table by using DAX formulas, and added Date related columns like Year, Month, Month number, Quarter, Weekday, Weekday number, Type of Day (Weekday/Weekend) for time-series analysis.
- I have written DAX formula to know if the day is weekday or weekend, so that I could understand program count on week days & weekends.
- Whenever I had to show non-numeric data on x-axis, like Weekdays, Months, Program_Duration bins, Age, I created index column for custom sorting for each non-numeric column. So that I could sort the non-numeric data based on the corresponding index column when using them in visualizations. Otherwise, non-numeric data gets sorted alphabetically. 

- I have created Program_capacity bins to understand program count by capacity.

          SWITCH(TRUE(),'scheduled_programs Dallas'[Capacity]<100,"1-100",'scheduled_programs Dallas'[Capacity]<200,"100-200",'scheduled_programs Dallas'[Capacity]<400,"200-400",'scheduled_programs Dallas'[Capacity]<600,"400-600",'scheduled_programs Dallas'[Capacity]<1000,"600-1000","1000+")

- I created measure to calculate the number of programs that provided transportation.
          Programs_with_Transportation = CALCULATE(COUNT('scheduled_programs Dallas'[Transport Provided]),FILTER('scheduled_programs Dallas','scheduled_programs Dallas'[Transport Provided]=1))

- I created a measure to calculate the number of programs that paid the participants.
          Programs_Paid_Participants = CALCULATE(COUNT('scheduled_programs Dallas'[Participants Paid]),FILTER('scheduled_programs Dallas','scheduled_programs Dallas'[Participants Paid]=1))


## Data Modeling
After the dataset was cleaned and transformed, it was ready to be modeled. I created relationship between original cleaned dataset and the DATE table which allow me to perform time-series analysis.
![DataModeling-DallasCityofLearning](https://github.com/user-attachments/assets/fa14ca2d-a6be-463f-b19a-60c59f7aaa99)

## Data Visualization
https://tinyurl.com/2d6sj2ta

Data visualization for the datasets was done in Microsoft Power BI Desktop and designed in PowerPoint, the dashboard includes:
- One home page
- 10 analysis pages

### Dashboard type
Dashboard by the level of detail: **Tactical dashboard**

Dashboard by use-case: **Exploratory**

Target audience: **Team lead & Manager** (non-technical users)

## Analysis and Insights
The purpose of this dashboard is to serve as self-exploratory for managers, but I still note some highlighted points that I recognize below:
#### Insights:
- Program count for transportation provided is same as program count for participants paid. That means transportation is only provided for the programs which paid the participants.
- ‘Dallas Public Library’ stands in 1st place, in conducting highest number of programs. Dallas Symphony Orchestra’ stands in 1st place, in conducting overall highest capacity programs.
- All organizations conducted the greater number of programs towards the summer. Program count is picking up from March and going down from October.
- Greater number of programs were started on Monday, as it makes sense to start a program at the beginning of the week. Geater number of programs were conducted during the office hours like 9AM-6PM mostly.
- Most programs were suitable for the young people who are in between 4 and 20.
- Most of the programs conducted by all organizations were having the capacity of 1-100 participants. 
#### Recommendations:
- Study the strategies employed by these top organizations to understand how they manage to conduct so many programs successfully. Consider collaborations or partnerships with these organizations to leverage their expertise and possibly co-host programs to share resources and knowledge.
- Allocate more resources and plan more programs for the peak months to maximize impact and engagement. Develop special programs or incentives to attract participants during the off-peak months (November to February).
- Introduce programs that start on different days of the week and during evenings or weekends to accommodate different participant schedules. Actually, it is always better to Survey participants to understand their preferred days and times for programs, then adjust scheduling accordingly.
- Introduce a few long-term programs (more than a month) to provide deeper learning and sustained engagement for interested participants.
- Continue offering free programs to ensure accessibility for all participants. Introduce optional paid services or premium versions of programs for those who can afford it, using the additional revenue to enhance free offerings.
- Ensure effective management of program capacities to maintain quality and personalized attention. Consider scaling up the most successful programs to accommodate more participants without compromising quality.
