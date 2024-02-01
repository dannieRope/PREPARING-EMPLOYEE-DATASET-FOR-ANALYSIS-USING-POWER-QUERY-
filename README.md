# PREPARING EMPLOYEE DATASET FOR ANALYSIS USING POWER QUERY 

Knowing how to tidy up messy data with Power Query is important, but using M-code to transform the data is like having a superpower. 

M-codes make the process of cleaning data simpler and reduce the number of steps needed to transform it.

Having grasped the concept of Power Query folding, I've chosen to take on a small data cleaning project. My goal is to enhance my skills by delving into M-code and applying it to the task at hand.


# THE DATASET 

The dataset comprises 8 columns with 276 rows, representing an employee dataset containing details of individuals within a company. The columns are as follows:

1. EmpID – A unique identifier for each employee.

2. Name – The full name of each employee.

3. Gender – The gender of each employee.

4. Department – The department to which each employee belongs.

5. Salary – The monthly pay of each employee.

6. Start Date – The date of employment.

7. FTE - An employee's scheduled hours divided by the employer's hours for a full-time workweek. A value of 1 indicates full-time, while less than 1 signifies part-time.

8. Work Location – The location of each employee.

   


   ![Screenshot (448)](https://github.com/dannieRope/PREPARING-EMPLOYEE-DATASET-FOR-ANALYSIS-USING-POWER-QUERY-/assets/132214828/bcaa4df0-e11d-487c-af69-e3a7599a00d3)



###  Issues identified in the dataset from the data provider:

1. Eliminate duplicate entries.
2. Replace null values in the gender column with "non-binary". 
3. Replace null values in the salary column with the average salary.
4. Divide the "World location" column into separate columns for city and country.
5. A value of 1 should indicate full-time and less than 1 should indicate part-time
6. Replace NULL values in the Department column with IT 

# DATA PREPARATION WITH POWER QUERY 

After bringing the data into Power Query, I checked each column, starting with the EmpID column. 
The EmpID is a special number assigned to each person working for the company. 
It's unique, meaning no two employees should have the same ID, and thus, each ID should only appear once in the dataset. 
However, upon checking, I found some duplicate IDs in the column that need to be removed.

To eliminate duplicate entries, I employed the following code:

```m
RemoveDuplicates = Table.Distinct(#"Changed Type", {"Emp ID"})
```

![before and after duplicates](https://github.com/dannieRope/PREPARING-EMPLOYEE-DATASET-FOR-ANALYSIS-USING-POWER-QUERY-/assets/132214828/940262e7-a774-40de-9c4b-f104ab0117c7)


This code utilizes the `Table.Distinct` function to ensure unique values in the specified column ("Emp ID") within the dataset named #"Changed Type".
And this was performed in the Advanced Editor. 

The next step is to separate the Work Location column into City and Country. I accomplished this task using the following code:

```m
= Table.SplitColumn(#"Removed Duplicates", "Work location", Splitter.SplitTextByDelimiter(",", QuoteStyle.Csv), {"City", "Country"})
```

![splite column](https://github.com/dannieRope/PREPARING-EMPLOYEE-DATASET-FOR-ANALYSIS-USING-POWER-QUERY-/assets/132214828/cadeed42-e69a-4438-9161-bf713f0ffa63)


This code utilizes `Table.SplitColumn` to split the "Work location" column by the comma delimiter, resulting in two new columns named "City" and "Country" in the dataset named #"Removed Duplicates"(previous step).


To calculate the average salary, which will be used to replace null values in the Salary column, I used the following M code:

```m
= Number.Round(List.Average(#"Split Column by Delimiter"[Salary]), 2)
```

This code utilizes `List.Average` to calculate the average of the Salary column in the dataset named #"Split Column by Delimiter"(previous step) rounded to two decimal places. The result is 72315.33 as the average salary.

![AverageSalary](https://github.com/dannieRope/PREPARING-EMPLOYEE-DATASET-FOR-ANALYSIS-USING-POWER-QUERY-/assets/132214828/6dfd560a-50d2-48a9-9027-1155abd22bbd)


The remaining columns were transformed using the following M code to address the issues presented by the data provider:

```m
= Table.TransformColumns(#"Split Column by Delimiter",{
    {"Gender", each if _ = null then "Non Binary" else _, type text},
    {"Department", each if _ = "NULL" then "IT" else _, type text},
    {"Salary", each if _ = null then AverageSalary else _, Currency.Type},
    {"FTE", each if _ = 1 then "Full Time" else "Part Time", type text},
    {"Country", each if _ = null then "Remote" else _, type text},
    {"Start Date", each Date.From(_, "en-US"), Date.Type}
})
```

This code utilizes `Table.TransformColumns` to apply specific transformations to each of the mentioned columns. 

It handles cases such as replacing null values in "Gender" with "Non Binary,"

changing "Department" to "IT" if it is "NULL," replacing null values in "Salary" with the calculated `AverageSalary`, 

transforming "FTE" to "Full Time" or "Part Time" based on the condition,

setting "Country" to "Remote" if null, and converting "Start Date" to a date type.

The below images shows the data before and after the transformation. 

Before
![beforeempdata](https://github.com/dannieRope/PREPARING-EMPLOYEE-DATASET-FOR-ANALYSIS-USING-POWER-QUERY-/assets/132214828/c08cbc42-b55d-4ff9-84fc-61a6bcabe0f1)


After
![empdataclean](https://github.com/dannieRope/PREPARING-EMPLOYEE-DATASET-FOR-ANALYSIS-USING-POWER-QUERY-/assets/132214828/cd4da3b2-ba62-4f8e-8c73-a3f3e5574968)


Advance Editor and Steps applied 
![mcode and steps applied](https://github.com/dannieRope/PREPARING-EMPLOYEE-DATASET-FOR-ANALYSIS-USING-POWER-QUERY-/assets/132214828/694b945d-a5a4-4490-80cd-09905feab695)


Find the clean data [here]()


![image](https://github.com/dannieRope/PREPARING-EMPLOYEE-DATASET-FOR-ANALYSIS-USING-POWER-QUERY-/assets/132214828/a05c5128-e4b1-4a71-86e5-e73671fc7995)





