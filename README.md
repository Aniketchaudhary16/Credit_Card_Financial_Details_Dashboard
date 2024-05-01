# Credit_Card_Financial_Details_Dashboard
Power Bi Dashboard

Project Objective :- Making a weekly report for credit cards that shows important info in real-time. This helps people keep track of how credit cards are doing and understand any trends.

Steps to import data to SQL database
1. Prepare csv file
2. Create tables in SQL
3. Import csv file into SQL


SQL Query to create and import data from csv files:

1. Create a database 
CREATE DATABASE ccdb;


2. Create cc_detail table

CREATE TABLE cc_detail (
    Client_Num INT,
    Card_Category VARCHAR(20),
    Annual_Fees INT,
    Activation_30_Days INT,
    Customer_Acq_Cost INT,
    Week_Start_Date DATE,
    Week_Num VARCHAR(20),
    Qtr VARCHAR(10),
    current_year INT,
    Credit_Limit DECIMAL(10,2),
    Total_Revolving_Bal INT,
    Total_Trans_Amt INT,
    Total_Trans_Ct INT,
    Avg_Utilization_Ratio DECIMAL(10,3),
    Use_Chip VARCHAR(10),
    Exp_Type VARCHAR(50),
    Interest_Earned DECIMAL(10,3),
    Delinquent_Acc VARCHAR(5)
);


3. Create cc_detail table

CREATE TABLE cust_detail (
    Client_Num INT,
    Customer_Age INT,
    Gender VARCHAR(5),
    Dependent_Count INT,
    Education_Level VARCHAR(50),
    Marital_Status VARCHAR(20),
    State_cd VARCHAR(50),
    Zipcode VARCHAR(20),
    Car_Owner VARCHAR(5),
    House_Owner VARCHAR(5),
    Personal_Loan VARCHAR(5),
    Contact VARCHAR(50),
    Customer_Job VARCHAR(50),
    Income INT,
    Cust_Satisfaction_Score INT
);


4. Copy csv data into SQL (remember to update the file name and file location in below query)

--> copy cc_detail table

COPY cc_detail
FROM 'C:\user\aniket chaudhary\3. Credit Card Financial Dashboard\credit_card.csv' 
DELIMITER ',' 
CSV HEADER;

--> copy cust_detail table

COPY cust_detail
FROM 'C:\user\aniket chaudhary\3. Credit Card Financial Dashboard\customer.csv' 
DELIMITER ',' 
CSV HEADER;


5. Insert additional data into SQL, using same COPY function

--> copy additional data (week-53) in cc_detail table
COPY cc_detail
FROM 'C:\user\aniket chaudhary\3. Credit Card Financial Dashboard\cc_add.csv' 
DELIMITER ',' 
CSV HEADER;

--> Copy additional data (week-53) in cust_detail table (remember to update the file name and file location in below query)
COPY cust_detail
FROM 'C:\user\aniket chaudhary\3. Credit Card Financial Dashboard\cust_add.csv' 
DELIMITER ',' 
CSV HEADER;

NOTE :- If the SQL query isn't working, try moving your files to the "D" drive.  


Power Bi DAX Queries for create colums 

--> Create a Age group column for understandable visuals

AgeGroup = SWITCH(
TRUE(),
'public cust_detail'[customer_age] < 30, "20-30",
'public cust_detail'[customer_age] >= 30 && 'public cust_detail'[customer_age] < 40, "30-40",
'public cust_detail'[customer_age] >= 40 && 'public cust_detail'[customer_age] < 50, "40-50",
'public cust_detail'[customer_age] >= 50 && 'public cust_detail'[customer_age] < 60, "50-60",
'public cust_detail'[customer_age] >= 60, "60+",
"unknown"
)

--> Create a Income group column for sort out 3 different categories

IncomeGroup = SWITCH(
TRUE(),
'public cust_detail'[income] < 35000, "Low",
'public cust_detail'[income] >= 35000 && 'public cust_detail'[income] <70000, "Med",
'public cust_detail'[income] >= 70000, "High",
"unknown"
)

week_num2 = WEEKNUM('public cc_detail'[week_start_date])

Revenue = 'public cc_detail'[annual_fees] + 'public cc_detail'[total_trans_amt] + 'public cc_detail'[interest_earned]

Current_week_Reveneue = CALCULATE(
  SUM('public cc_detail'[Revenue]),
  FILTER(
    ALL('public cc_detail'),
    'public cc_detail'[week_num2] = MAX('public cc_detail'[week_num2])))

Previous_week_Reveneue = CALCULATE(
  SUM('public cc_detail'[Revenue]),
  FILTER(
    ALL('public cc_detail'),
    'public cc_detail'[week_num2] = MAX('public cc_detail'[week_num2])-1))

