# Spark Challenge

## Introduction

You have just been hired at an e-commerce company as their first data engineer.  The company sells a variety of products on their website and thus far has relied solely on google analytics. Fortunately, they were smart enough to capture their data on S3 for future analysis.

In particular, their data is stored as tab-delimited text files including users, products and clickstream data from their website containing things like which urls a particular user visited on their website.

The team has been hearing about Spark and has requested that you use it to generate some reports and prep some data for loading into Elasticsearch so that the customer support team can quickly look at the actions of a particular user.

## Submission Process

You should provide all source code as well as the output files (TSV and JSON) as a zip and submit to colin@datascience.com along with contact info, resume or linkedIn and github account if not previously provided.

# Data

All data can be found in the public ds12-education S3 bucket:
  ```
  s3://ds12-education/clickstream-challenge/data/
  ```

## Users
The users dataset is a single, tab-delimited file with the following schema and found at:
  ```
  s3://ds12-education/clickstream-challenge/data/users.tsv.gz
  ```

SWID | BIRTH_DT | GENDER_CD
--- | --- | ---
(string) | (string) | (string)
... | ... | ...

## Products
The products dataset is a single, tab-delimited file with the following schema and found at:
  ```
  s3://ds12-education/clickstream-challenge/data/products.tsv.gz
  ```

url | category
--- | ---
(string) | (string)
... | ...

## Clickstream

The clickstream dataset is comprised of several very wide, tab-delimited gzipped files without headers. There are too many columns to 
enumerate here. Instead, we provide a mapping of the relevant columns to their purpose.  The files can be found at:

```
  s3://ds12-education/clickstream-challenge/data/Omniture*
```

Column | Field 
--- | ---
 C1 | timestamp 
 C7 | ip address 
 C12 | url from products table
 C13 | SWID from users table 
 C49 | city 
 C50 | country 
 C52 | state 

Hint: You can use an anonymous function to select the desired columns and cast 
them to the appropriate types (see final schema). The fields in the 
**clickstream** table may need to be cleaned in order to perform 
joins with the **user** and **products** table.

## Part 1 - Calculate KPIs

The company's board of directors is looking for some basic KPIs to better understand the business. They've asked for daily numbers for distinct users and total number of click events.

You've been tasked with using the above data sources to generate the following reports output as two seperate TSV files.

- **Total number of clicks and distinct users (SWID) per day**

YEAR | MONTH | DAY | DISTINCT_USERS | CLICKCOUNT
--- | --- | --- | --- | ---
2015 | 10 | 15 | 25 | 100
...|...|...|...|...


- **Total Number of clicks per product category**

CATEGORY | CLICKCOUNT 
--- | ---
Shoes | 1015
...|...

## Part 2 (Prep JSON data for Customer Service)

Your task is to produce a line-delimited JSON file that has the 
following schema:

    root
    |-- userId: string (nullable = true)
    |-- birthDate: string (nullable = true)
    |-- genderCd: string (nullable = true)
    |-- clicks: array (nullable = false)
    |    |-- element: struct (containsNull = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- timestamp: timestamp (nullable = true)
    |    |    |-- ipAddress: string (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- state: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- category: string (nullable = true)
    
As line-delimited JSON:

```
{"UserId":"0001BDD9-EABF-4D0D-81BD-D9EABFCD0D7D", "birthDate":"8-Apr-84", "genderCd":"F", "clicks":[{"url":"http://www.acme.com/SH55126545/VD55149415","timestamp":"2012-03-12 13:56:55","ipAddress":"98.219.102.151", "city":"San Francisco", "state":"CA", "country":"US", "category":"movies"}]}
...
```

The order of the columns within levels in the final dataset need not match the 
order given above. What is most important is producing the nested hierarchical 
structure.
