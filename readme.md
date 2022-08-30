# SQL For AWS Athena

## Table of contents

- [What's included](#whats-included)
- [Overview](#Overview)
- [Data](#data)
- [Set up](#set-up)
- [Main Tutorial](#main-tutorial)
- [Useful Links](#useful-links)
- [Creators](#creators)

## What's included
Three CSVs containing all data for the tutorial. 
All code and queries which are ran during the tutorial. 
link to [youtube video](https://youtu.be/V21xjnHMOyk) video. 
 

## Overview 
1. We create an S3 bucket and upload data for the tutorial
2. We look at some of the theory behind AWS Athena and Presto 
3. We run a series of queries against the data we uploaded to S3

## Data
Below are the schemas for the tables created in the Glue Data Catalog by the cloudformation template. They also include a small sampke of data to aid the explaination of the coding syntax.

**Customers**
| Customerid      | Firstname | Lastname| Fullname |
| ----------- | ----------- |-----------|-----------|
|  293 | Catherine                | Abel                   | Catherine Abel                 |
|  295 | Kim                      | Abercrombie            | Kim Abercrombie                |
|  297 | Humberto                 | Acevedo                | Humberto Acevedo               |

**Orders**

|  SalesOrderID |  SalesOrderDetailID |  OrderDate |  DueDate  | ShipDate | EmployeeID | CustomerID | SubTotal | TaxAmt | Freight | TotalDue | ProductID | OrderQty | UnitPrice | UnitPriceDiscount | LineTotal |
|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|---------------|
| 71782 | 110667 | 5/1/2014   | 5/13/2014  | 5/8/2014  | 276 |  293 |   33319.986 |  3182.8264 |  994.6333 | 37497.4457 | 714 |  3 |    29.994 |    0 |      89.982 |
| 44110 |   1732 | 8/1/2011   | 8/13/2011  | 8/8/2011  | 277 |  295 |  16667.3077 |  1600.6864 |  500.2145 |  18768.2086 | 765 |  2 |  419.4589 |    0 |    838.9178 |
| 44131 |   2005 | 8/1/2011   | 8/13/2011  | 8/8/2011  | 275 |  297 |  20514.2859 |  1966.5222 |  614.5382 |  23095.3463 | 709 |  6 |       5.7 |    0 |        34.2 |

**Employees**

| EmployeeID | ManagerID | FirstName | LastName | FullName  | JobTitle | OrganizationLevel | MaritalStatus  | Gender | Territory | Country | Group |      
|------------|------------|------------|------------|------------|------------|------------|------------|------------|------------|------------|------------|
| 276 |  274 | Linda   | Mitchell          | Linda Mitchell           | Sales Representative         | 3 | M | F | Southwest      | US   | North America |
| 277 |  274 | Jillian | Carson            | Jillian Carson           | Sales Representative         | 3 | S | F | Central        | US   | North America |
| 275 |  274 | Michael | Blythe            | Michael Blythe           | Sales Representative         | 3 | S | M | Northeast      | US   | North America |

## Set Up 
1. Create S3 bucket to hold data and results 
2. Create S3 folder `results`
3. Upload `Data` folder from GitHuib repo
4. Set up Athena workgroup
5. Console overview
6. Create Database
```
CREATE DATABASE demo_data; 
```

## Main Tutorial 
1. Create Customer Table
```
CREATE EXTERNAL TABLE IF NOT EXISTS customers(
  customerid BIGINT, 
  fistname STRING,
  lastname STRING,
  fullname STRING
  )
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION 's3://table-data-location/'; 
```
2. Select all rows from cusomters table 
```
SELECT * FROM customers;
```
3. select a column from customer table 
```
SELECT Firstname FROM customers;
```
4. column alais 
```
SELECT Firstname as f_name FROM customers;
```
5. Concat two columns of the same data type and alais 
```
SELECT CONCAT(firstname,lastname) AS full_name FROM customer;
```
6. Use a WHERE clause to filter data 
```
SELECT Firstname FROM customers WHERE firstname = 'John' ;
```
7. Use an AND/OR clause to filter data 
```
SELECT * FROM customers WHERE firstname = 'John'  AND lastname = 'Arthur';

SELECT * FROM customers WHERE firstname = 'John' or lastname = 'Arthur';
```
8. Use an in clause to filter data 
```
SELECT * FROM customers WHERE Customerid in (371) 

SELECT * FROM customers WHERE Customerid in (371, 377);
```
9.  Wild Cards
```
SELECT * FROM customers WHERE Fullname like 'J%'; 
```
10. Union 
```
SELECT Firstname FROM customers WHERE Customerid in (371) 
UNION
SELECT Firstname FROM customers WHERE Customerid in (371, 377);
```
11. INSERT A ROW
```
INSERT INTO customers (customerid, firstname, lastname, fullname) values (1221,'John', 'Doe', 'John Doe'); 
```
12. DISTINCT 
```
SELECT DISTINCT firstname FROM customers WHERE firstname like 'J%';
```
13. COUNT 
```
SELECT count(firstname) FROM customers WHERE firstname like 'J%';
```
14. COUNT DISTINCT
```
SELECT count(DISTINCT firstname) FROM customers WHERE firstname like 'J%';
```
15. GROUP BY 
```
SELECT firstname FROM customers WHERE firstname like 'J%' group by firstname;
```
16. NESTED QUERIES
```
SELECT * FROM customers WHERE customerid in (SELECT customerid from customers);
```
17. COMMON TABLE EXPRESSIONS (cte's)
```
with cte as 
(
SELECT firstname, lastname, CONCAT(firstname,' ',lastname)
FROM customers
)
SELECT * 
FROM customers; 
```
18. INNER JOIN
```
CREATE EXTERNAL TABLE IF NOT EXISTS employees(
employeeid  bigint,
managerid bigint,
firstname string,
lastname string , 
fullname  string,
jobtitle string,    
organizationlevel int   , 
maritalstatus string, 
gender string,
territory string,
country string,
group string 
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION 's3://table-data-location/'; 
```
```
CREATE EXTERNAL TABLE IF NOT EXISTS orders(
salesorderid bigint,
salesorderdetailid int,
orderdate string,
duedate string,
shipdate string,
employeeid bigint,
customerid bigint,
subtotal decimal(17,4),
taxamt decimal(17,4),
freight decimal(17,4),
totaldue decimal(17,4),
productid  int,
orderqty int,
unitprice decimal(17,4),
unitpricediscount decimal(17,4),
linetotal decimal(17,4)
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION 's3://table-data-location/'; 
```
```
SELECT * FROM customers INNER JOIN orders on customers.customerid = orders.customerid; 
```
19. LEFT JOIN 
```
SELECT * FROM order LEFT JOIN customers on customers.customerid = orders.customerid; 
```

## Creators

**Johnny Chivers**

- <https://github.com/johnny-chivers/>

## Useful Links

- [youtube video](https://youtu.be/V21xjnHMOyk) 
- [website](https://www.johnnychivers.co.uk)
- [buy me a coffee](https://www.buymeacoffee.com/johnnychivers)


Enjoy :metal: