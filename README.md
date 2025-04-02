# walmart_analytics
This project is an end-to-end data analysis solution designed to extract critical business insights from Walmart sales data. I utilize Python for data processing and analysis, SQL for advanced querying, and structured problem-solving techniques to solve key business questions. The project develop skills in data manipulation, SQL querying, and data pipeline creation.

## Project Steps
### 1. Set Up the Environment
Tools Used: Visual Studio Code (VS Code), Python, SQL (MySQL and PostgreSQL)
Goal: Create a structured workspace within VS Code and organize project folders for smooth development and data handling.
### 2. Set Up Kaggle API
API Setup: Obtain your Kaggle API token from Kaggle by navigating to profile settings and downloading the JSON file.
Configure Kaggle:
Place the downloaded kaggle.json file in local .kaggle folder.
Use the command kaggle datasets download -d <dataset-path> to pull datasets directly into your project.
### 3. Download Walmart Sales Data
Data Source: Use the Kaggle API to download the Walmart sales datasets from Kaggle.
Dataset Link: Walmart Sales Dataset
Storage: Save the data in the data/ folder for easy reference and access.
### 4. Install Required Libraries and Load Data
Libraries: Install necessary Python libraries using:
pip install pandas numpy sqlalchemy  psycopg2
Loading Data: Read the data into a Pandas DataFrame for initial analysis and transformations.
### 5. Explore the Data
Goal: Conduct an initial data exploration to understand data distribution, check column names, types, and identify potential issues.
Analysis: Use functions like .info(), .describe(), and .head() to get a quick overview of the data structure and statistics.
### 6. Data Cleaning
Remove Duplicates: Identify and remove duplicate entries to avoid skewed results.
Handle Missing Values: Drop rows or columns with missing values if they are insignificant; fill values where essential.
Fix Data Types: Ensure all columns have consistent data types (e.g., dates as datetime, prices as float).
Currency Formatting: Use .replace() to handle and format currency values for analysis.
Validation: Check for any remaining inconsistencies and verify the cleaned data.
### 7. Feature Engineering
Create New Columns: Calculate the Total Amount for each transaction by multiplying unit_price by quantity and adding this as a new column.
Enhance Dataset: Adding this calculated field will streamline further SQL analysis and aggregation tasks.
### 8. Load Data into PostgreSQL
Set Up Connections: Connect to PostgreSQL using sqlalchemy and load the cleaned data into each database.
Table Creation: Set up tables in both PostgreSQL using Python SQLAlchemy to automate table creation and data insertion.
Verification: Run initial SQL queries to confirm that the data has been loaded accurately.
### 9. SQL Analysis: Complex Queries and Business Problem Solving
Business Problem-Solving: Write and execute complex SQL queries to answer critical business questions, such as:
1. find the different payment method, no of transaction and number of qty sold
```sql
select 
	payment_method, 
	count(*) as paycount,
	sum(quantity) as qtysold 
from walmart
group by 1
```
 2. Identify the highest rated category in each branch, displaying the branch, category, avg rating 
```sql
select * from (select 
		branch, 
		category, 
		avg(rating) as avg_rating,
		rank() over(partition by branch order by avg(rating) desc) as rating
	from walmart
	group by 1,2 )
where rating =1
```
3. Identify the busiest day for each branch based on the number of transaction
```sql
select * from (select 
		branch, 
		to_char(to_date(date,'DD/MM/YY'),'day'),
		count(*) as no_of_transaction,
		rank() over(partition by branch order by count(*) desc) as ranking
	from walmart
group by 1,2)
where ranking =1 
```
4. calculate the total quantity of items sold per payment method.
 list payment method and total quantity
```sql
select 
	payment_method, 
	sum(quantity) as Total_quantity 
from walmart
group by 1 
```
5. Determine the avg, min, and max rating of product for each city.
select 
```sql
	"City", 
	avg(rating) as avg_rating, 
	min(rating) as min_rating,
	max(rating) as max_rating 
from walmart
group by 1
```
6. calculate the total profit for each category by considering total profit as (unit * qty * profit_margin])
```sql
select 
	category, 
	round(sum("Total")::numeric,2) 
from walmart
group by 1
```
7. Determine the most common payment method for each branch.Display branch and preferred payment method
```sql
select 
	branch, payment_method  
	from (select 
				branch, 
				payment_method, 
				count(*),
				RANK() over(partition by branch order by count(*) desc) as ranking
		from walmart
		group by 1,2)
where ranking =1
```
8. Categories sales into three group morning, evening, afternoon.find out each of the shift and number of transcation
```sql
select  
	(case when extract(hour from(time::time)) < 12 then 'Morning'
	when extract(hour from(time::time)) between 12 and 17 then 'Afternoon'
	Else 'Evening' 
	end) as shift,
	count(*)
from walmart
group by 1

```
9. identify 5 branch with highest decrease ratio in revenue compare to last year (current year 2023 vs last year 2022)
```sql
select branch, ((revenue2022-revenue2023)::numeric/revenue2022::numeric*100) as rev_ratio 
from (select branch,
			sum(case when extract(year from to_date(date,'dd-mm-yy'))='2022' then "Total" end) as revenue2022,
			sum(case when extract(year from to_date(date,'dd-mm-yy'))='2023' then "Total" end) as revenue2023
	from walmart
group by 1)
order by 2 desc
limit 5
```
10. which product category generates the highest revenue and profit margin
```sql
select 
	category, 
	round(sum("Total")::numeric,2) as Revenue, 
	round(sum(profit_margin)::numeric,2) as profitmargin
from walmart
group by 1
order by 2,3 desc

```
11. How do customer purchasing pattern changes based on time of day and branch location
```sql
select branch,
				case
				when extract(hour from time::time) between 6 and 12 then 'Morning'
				when extract(hour from time::time) between 12 and 16 then 'Afternoon'
				when extract(hour from time::time) between 16 and 20 then 'Evening'
				else 'Night' end as Time_of_day,
				count(*) as total_orders,
				sum("Total") as total_sales
from walmart
group by 1,2
order by 1,2 desc;
```
12. Is there any relationship between payment method and customer rating
```sql
select payment_method, round(avg(rating)::numeric,2) as average_rating from walmart
group by 1 
order by 2 desc
```
13. which branch are underperforming in terms of total sales and profit
```sql
select branch, count(*) as total_orders, sum("Total") as profit from walmart
group by 1
order by 2,3 asc

```
14. identifying which Year,month have most sales
```sql
select 
	extract(year from to_date(date,'dd-mm-yy')), 
	to_char(to_date(date,'dd-mm-yy'),'Month'),
	sum(invoice_id) as total_order from walmart
group by 1,2
order by 3 desc

```
  
### 10. Project Publishing and Documentation
Documentation: Maintain well-structured documentation of the entire process in Markdown or a Jupyter Notebook.
Project Publishing: Publish the completed project on GitHub or any other version control platform, including:
The README.md file (this document).
Jupyter Notebooks 
SQL query scripts.
Data files (if possible) or steps to access them.
