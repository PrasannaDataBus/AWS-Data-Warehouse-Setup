# AWS-Data-Warehouse-Setup

## Objectives

- Create a data warehouse with Amazon Redshift Serverless.
- Create a schema.
- Create a table.
- Load the table with sample data.
- Ingest and query REAL WORLD data.

## Objective 1: Create a data warehouse with Amazon Redshift Serverless

### Step 1: In the left navigation pane, choose Redshift Serverless.

<img width="1256" height="221" alt="{AD3EB00D-0C92-44CF-8D1B-643EC1186469}" src="https://github.com/user-attachments/assets/b00f4950-c3ad-476a-93d3-ca4497853741" />

### Step 2: Setup configuration

<img width="721" height="1654" alt="{849D3E99-09C0-472B-B07A-E25755E5B53E}" src="https://github.com/user-attachments/assets/9aab7b45-6002-40fa-b3e3-a04a730f0b31" />

***Note: You can choose either default settings or customise settings - I have chosen customise settings.

- 2.1 - You will need to name your "namespace"
- 2.2 - Admin user credentials -> Customize admin user credentials
- 2.3 - You will need to enter "Admin user name"
- 2.4 - Admin password -> Manually add the admin password
- 2.5 - Admin user password -> Provide your password
- 2.6 - Choose Associate IAM Role -> And select the role "RedshiftAccessRole" -> Choose to add it
- 2.7 - Workgroup -> Enter Workgroup name -> Choose Base capacity
- 2.8 - Network and security -> Choose your Virtual provate cloud (VPC) from the drop down
- 2.9 - Network and security -> VPC security groups -> Choose your security group from the drop down (I have chosen "Enable access to redshift"
- 2.10 - Network and security -> Subnet -> Choose your Subnet from the dropdown (I have chosen Private Subnet 1, Private Subnet 2, Private Subnet 3)
- 2.11 - Save configuration
- 2.12 - Wait for the status to complete 100% and then click continue

### Step 3: Workgroup configuration

<img width="1056" height="465" alt="{2300611C-0557-48AF-AD0E-32DD7846F84C}" src="https://github.com/user-attachments/assets/ec6aca71-6c5a-4ed7-8903-7e7a1019dfeb" />

3.1 - Click on the lab-workgroup link
3.2 - Copy your Endpoint value

### Task Complete: You have successfully created an Amazon Redshift Serverless data warehouse.

## Objective 2: Create a schema

A schema is a namespace that contains named database objects such as tables, views, and user-defined functions (UDFs). A database can contain one or multiple schemas, and each schema belongs to only one database. Two schemas can have different objects that share the same name.

***Note: Amazon Redshift automatically creates a schema called public for every new database. When you don’t specify the schema name while creating database objects, the objects go into the public schema.

### Step 1: Open your command host terminal

- 1.1 - 

bash
`
cd ~
export RedshiftEndpoint=REDSHIFT_ENDPOINT
echo $RedshiftEndpoint
`
*** Replace REDSHIFT_ENDPOINT with the endpoint you have copeid earlier

- 1.2 - o connect to Redshift Serverless endpoint using psql, run the following commands on the command host terminal

bash
`
echo $RedshiftDB
psql -U dbadmin -h $RedshiftEndpoint -d $RedshiftDB -p 5439
`

- 1.3 - To create a new schema named US_SALES, run the following query using the psql prompt:

bash

`
create schema if not exists eu_sales;
`

- 1.4 - To view the list of schemas in dev database, run the following query:

bash

`
select * from pg_namespace;
`

** Output:

<img width="576" height="240" alt="{8E3CEB45-7A2F-4AD9-9D1E-2FEF831D9355}" src="https://github.com/user-attachments/assets/41fd48b8-da3a-4817-b167-95db3a7876a8" />

** Task Complete: You have successfully created a schema in dev database.

## Objective 3: Create Table

You can also specify any column information when you create the table.

You can also create a table using the schema_name.object_name notation to create the table in the specified schema.

- 3.1 - To create a table in us_sales schema, run the following query:

bash

`
CREATE TABLE us_sales.supplier (
  SupplierID int,
  City varchar (255)
);
`

- 3.2 - To insert data into a table, run the following query:

bash

`
INSERT INTO us_sales.supplier VALUES (781, 'San Jose'), (990, 'Palo Alto');
`

- 3.3 - To view the data inserted into a table, run the following query:

bash

`
SELECT * from us_sales.supplier;
`

** Output:

<img width="227" height="95" alt="{ABB169B8-BF35-4352-9ABB-61C1409D1C7B}" src="https://github.com/user-attachments/assets/5fc95bc0-ac5c-4e9f-a492-98abd9cc064a" />

** You have successfully created a table in a schema in the current database and inserted data into the table.

## Objective 4: Load sample data from Amazon S3

The COPY command uses the Amazon Redshift massively parallel processing (MPP) architecture to read and load data in parallel from files in Amazon S3, from an Amazon DynamoDB table, or from text output from one or more remote hosts.

*** Note: The COPY command appends the new input data to any existing rows in the table. The maximum size of a single input row from any source is 4 MB.

 bash

 `
 COPY us_sales.supplier
FROM 'S3BUCKETPATH/supplier.csv'
iam_role 'INSERT_REDSHIFT_ROLE'
region 'REGION' csv;
`

*** you need to replace the above S3BUCKETPATH, INSERT_REDSHIFT_ROLE, REGION with your respective values.

*** Output

<img width="725" height="66" alt="{E8CD59DB-9518-43F8-800F-B547C9088197}" src="https://github.com/user-attachments/assets/033c2100-a38c-48fc-8757-9456ec4e43c4" />

- 4.1 - To query the supplier table, run the following query:

bash

`
SELECT * FROM us_sales.supplier;
`

*** Output

<img width="276" height="182" alt="{B9A285DE-7BF1-4EF2-B494-A8B720F4EB84}" src="https://github.com/user-attachments/assets/cc0df2d0-7d9c-4d26-9037-e534b0348be1" />


*** You have successfully loaded sample data from Amazon S3 by using the COPY command.

## Objective 5: Ingest and query REAL WORLD data

DataSeine Events wants to build database application that helps analysts track sales activity from their Ticketing website where users buy and sell tickets online for sporting events, shows, and concerts.

In this task, you load the Ticket dataset from Amazon S3 to Redshift Serverless database and build queries to help analysts to use this information to provide incentives to buyers and sellers who frequent the site, to attract new users, and to drive advertising and promotions.

### Entity Relationship Diagram:

<img width="1101" height="672" alt="{4562A508-B4A1-42DC-969C-0A26E9C03252}" src="https://github.com/user-attachments/assets/eb5425d5-8be9-421d-93dd-4c458b3acbb0" />

*** Image description: The preceding diagram depicts the Entity Relationship diagram for TICKETING database which consists of seven tables: two fact tables and five dimensions.

- 5.1 - CREATE TABLES

### Table 1: users

bash

`
create table eu_sales.users(
	userid integer not null distkey sortkey,
	username char(8),
	firstname varchar(30),
	lastname varchar(30),
	city varchar(30),
	state char(2),
	email varchar(100),
	phone char(14),
	likesports boolean,
	liketheatre boolean,
	likeconcerts boolean,
	likejazz boolean,
	likeclassical boolean,
	likeopera boolean,
	likerock boolean,
	likevegas boolean,
	likebroadway boolean,
	likemusicals boolean);
`

### Table 2: venue

bash

`
create table us_sales.venue(
	venueid smallint not null distkey sortkey,
	venuename varchar(100),
	venuecity varchar(30),
	venuestate char(2),
	venueseats integer);
`

### Table 3: category

bash

`
create table us_sales.category(
	catid smallint not null distkey sortkey,
	catgroup varchar(10),
	catname varchar(10),
	catdesc varchar(50));
`

### Table 4: date

bash

`
create table us_sales.date(
	dateid smallint not null distkey sortkey,
	caldate date not null,
	day character(3) not null,
	week smallint not null,
	month character(5) not null,
	qtr character(5) not null,
	year smallint not null,
	holiday boolean default('N'));
`

### Table 5: event

bash

`
create table us_sales.event(
	eventid integer not null distkey,
	venueid smallint not null,
	catid smallint not null,
	dateid smallint not null sortkey,
	eventname varchar(200),
	starttime timestamp);
`

### Table 6: listing

bash

`
create table us_sales.listing(
	listid integer not null distkey,
	sellerid integer not null,
	eventid integer not null,
	dateid smallint not null  sortkey,
	numtickets smallint not null,
	priceperticket decimal(8,2),
	totalprice decimal(8,2),
	listtime timestamp);
`

### Table 7: sales

bash

`
create table us_sales.sales(
	salesid integer not null,
	listid integer not null distkey,
	sellerid integer not null,
	buyerid integer not null,
	eventid integer not null,
	dateid smallint not null sortkey,
	qtysold smallint not null,
	pricepaid decimal(8,2),
	commission decimal(8,2),
	saletime timestamp);
`

- 5.2 - Load data set from Amazon S3 by using the COPY command

### Load 1: users

bash

`
copy us_sales.users from 'S3BUCKETPATH/allusers.csv' iam_role 'INSERT_REDSHIFT_ROLE' region 'REGION' csv;
`

*** Output:

<img width="755" height="62" alt="{62B8F93C-51BF-4D4A-8DD8-6C0E8D29F029}" src="https://github.com/user-attachments/assets/52fd45a8-2788-44e7-9f3f-432f7b627f77" />

*** Replace S3BUCKETPATH, INSERT_REDSHIFT_ROLE, REGION placeholder with yours respectively

### Load 2: venue

bash

`
copy us_sales.venue from 'S3BUCKETPATH/venue_pipe.txt' iam_role 'INSERT_REDSHIFT_ROLE' delimiter '|' region 'REGION';
`

*** Output:

<img width="728" height="64" alt="{E6DDB794-8EFD-4479-ADED-D467C40789B2}" src="https://github.com/user-attachments/assets/a876697e-d532-4d1f-86dd-9e963f55c049" />

*** Replace S3BUCKETPATH, INSERT_REDSHIFT_ROLE, REGION placeholder with yours respectively

### Load 3: category

bash

`
copy us_sales.category from 'S3BUCKETPATH/category_pipe.txt' iam_role 'INSERT_REDSHIFT_ROLE' delimiter '|' region 'REGION';
`

*** Output:

<img width="736" height="59" alt="{05FEC706-1689-41C1-8E1E-75D32E54E083}" src="https://github.com/user-attachments/assets/0794934d-7fad-4df0-a1fd-2ad99289d3a0" />

*** Replace S3BUCKETPATH, INSERT_REDSHIFT_ROLE, REGION placeholder with yours respectively

### Load 4: date

bash

`
copy us_sales.date from 'S3BUCKETPATH/date2008.csv' iam_role 'INSERT_REDSHIFT_ROLE' region 'REGION' csv;
`

*** Output:

<img width="711" height="58" alt="{06404BCF-4449-41E5-A0A4-7375DC3A1869}" src="https://github.com/user-attachments/assets/c16ad795-9123-41ee-a8c6-93a298a0c314" />

*** Replace S3BUCKETPATH, INSERT_REDSHIFT_ROLE, REGION placeholder with yours respectively

### Load 5: event

bash

`
copy us_sales.event from 'S3BUCKETPATH/allevents_pipe.txt' iam_role 'INSERT_REDSHIFT_ROLE' delimiter '|' timeformat 'YYYY-MM-DD HH:MI:SS' region 'REGION';
`

*** Output:

<img width="709" height="57" alt="{F82B5F4E-BF46-4889-A576-FE29FB339B0B}" src="https://github.com/user-attachments/assets/68eba13b-dcbe-4098-8283-6f9f29c3da7a" />

*** Replace S3BUCKETPATH, INSERT_REDSHIFT_ROLE, REGION placeholder with yours respectively

### Load 6: listing

bash

`
copy us_sales.listing from 'S3BUCKETPATH/listings_pipe.txt' iam_role 'INSERT_REDSHIFT_ROLE' delimiter '|' region 'REGION';
`

*** Output:

<img width="770" height="62" alt="{56589920-C996-4CAD-8175-2DCD1BAF20B1}" src="https://github.com/user-attachments/assets/433e878e-69d0-4663-ac06-5a6f55670904" />

*** Replace S3BUCKETPATH, INSERT_REDSHIFT_ROLE, REGION placeholder with yours respectively

### Load 7: sales

bash

`
copy us_sales.sales from 'S3BUCKETPATH/sales_tab.txt' iam_role 'INSERT_REDSHIFT_ROLE' delimiter '\t' timeformat 'MM/DD/YYYY HH:MI:SS' region 'REGION';
`

*** Output:

<img width="746" height="62" alt="{BA035742-B883-404D-8175-236AB3E63CF1}" src="https://github.com/user-attachments/assets/3be0e071-bb75-4ffa-95a4-79224a352e72" />

*** Replace S3BUCKETPATH, INSERT_REDSHIFT_ROLE, REGION placeholder with yours respectively

*** Load has been completed

- 5.3 - Analysis

To know what are the top five venues that most events happened on a given calendar date to choose the best venue for their next concert.

bash

`
SELECT caldate,venuename,count(event.eventid) total_events 
FROM us_sales.sales as sales, 
     us_sales.event as event, 
     us_sales.venue as venue, 
     us_sales.date as date 
WHERE sales.eventid=event.eventid 
AND event.dateid=date.dateid 
AND event.venueid=venue.venueid 
GROUP BY caldate,venuename 
ORDER BY total_events DESC 
LIMIT 5;
`

*** Output:

<img width="610" height="333" alt="{05373009-B4C5-40FD-AECE-06C9DA30DDB9}" src="https://github.com/user-attachments/assets/c81c461d-d981-49c9-b31d-5bf2f5031225" />

🛡️ Copyright & Compliance Notice

© 2026 PrasannaDataBus. All rights reserved. This repository and its contents are intended solely for self learning and portfolio demonstration purposes.

All screenshots, configuration snippets, and architecture diagrams related to Amazon Web Services (AWS) are used under fair use for illustrative and informational purposes only. AWS, the AWS logo, AWS Services and all related marks are trademarks of Amazon Web Services, Inc. or its affiliates.

No proprietary AWS data, credentials, or customer information are included. Do not reuse or redistribute any part of this repository without explicit permission from the author.







