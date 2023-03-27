# Sentiment Analysis with Amazon Aurora

[![GitHub Issues](https://img.shields.io/github/issues/DanieleBocchino/Sentiment-Analysis-with-AuroraDB)](https://github.com/DanieleBocchino/Sentiment-Analysis-with-AuroraDB)
[![GitHub Stars](https://img.shields.io/github/stars/DanieleBocchino/Sentiment-Analysis-with-AuroraDB)](https://github.com/DanieleBocchino/Sentiment-Analysis-with-AuroraDB)
[![GitHub Forks](https://img.shields.io/github/forks/DanieleBocchino/Sentiment-Analysis-with-AuroraDB)](https://github.com/DanieleBocchino/Sentiment-Analysis-with-AuroraDB)
[![GitHub Contributors](https://img.shields.io/github/contributors/DanieleBocchino/Sentiment-Analysis-with-AuroraDB)](https://github.com/DanieleBocchino/Sentiment-Analysis-with-AuroraDB)
[![GitHub Last Commit](https://img.shields.io/github/last-commit/DanieleBocchino/Sentiment-Analysis-with-AuroraDB)](https://github.com/DanieleBocchino/Sentiment-Analysis-with-AuroraDB)

This is a mini project created using AWS Services - Amazon S3 and Amazon Quicksight. This project aims to provide instructions on how to use AWS services to create visualizations from large datasets. 

[![AWS](https://img.shields.io/badge/AWS-100000?style=flat&logo=amazon&logoColor=FFFFFF&labelColor=5C5C5C&color=FF7300)](https://docs.aws.amazon.com/quicksight/latest/user/signing-up.html)
[![IAM](https://img.shields.io/badge/AWS_IAM-100000?style=flat&logo=drone&logoColor=white&labelColor=494949&color=ED1C24)](https://aws.amazon.com/iam/)
[![AURORA](https://img.shields.io/badge/AWS_Aurora-100000?style=flat&logo=AmazonRDS&logoColor=white&labelColor=494949&color=527FFF)](https://aws.amazon.com/aurora/)
[![S3](https://img.shields.io/badge/AWS_Quicksight-100000?style=flat&logo=AmazonS3&logoColor=white&labelColor=494949&color=569A31)](https://aws.amazon.com/s3/)
[![Postgres](https://img.shields.io/badge/PosgreSQL-100000?style=flat&logo=postgresql&logoColor=white&labelColor=494949&color=4169E1)](https://aws.amazon.com/quicksight/)

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Project Overview](#projectoverview)
- [Resources](#resources)
- [Steps](#steps)
- [Conclusion](#conclusion)
- [License & Credits](#license&credits)
- [Contributors](#contributors)


## Introduction
This tutorial guides you through integrating Amazon Comprehend for sentiment analysis with an Amazon Aurora PostgreSQL database instance. Aurora machine learning enables developers to add machine learning capabilities to their applications using familiar SQL techniques, syntax, and interfaces, without prior machine learning experience or custom integrations.

<p align="center">
<img src="./assets/schema.png" alt="schema" align="center">
</p>

## Prerequisites

- An AWS account
- Basic knowledge of SQL and PostgreSQL
- IAM roles with permissions to access Amazon Comprehend and Amazon S3

## Project Overview

1. Create the IAM roles required to allow Amazon Aurora access to Amazon Comprehend and Amazon S3.
2. Create the Amazon Aurora PostgreSQL database instance
3. Download and install a PostgreSQL client
4. Connect to the Aurora DB instance with the PostgreSQL client
5. Install AWS ML and S3 extensions so you can query the database and analyze the sentiment of a sample table and customer reviews dataset
6. Clean up resources used in this tutorial

### Resources

The following resources were used in the development of this project:

- <img src="./assets/s3.png" alt="S3" width="25"> Amazon S3
- <img src="./assets/iam.png" alt="IAM" width="25"> Amazon IAM roles
- <img src="./assets/aurora.png" alt="Aurora" width="25"> Amazon Aurora
- <img src="./assets/postgresql.png" alt="Postgresql" width="25"> PostgreSQL
- <img src="./assets/sagemaker.png" alt="SageMaker" width="25"> Amazon SageMaker


## Contents
- IAM roles creation guide
- Aurora PostgreSQL database instance creation guide
- PostgreSQL client installation and connection guide
- AWS ML and S3 extensions installation guide
- Sentiment analysis guide with sample table and customer reviews dataset
- Cleanup guide for tutorial resources

## Steps

### Step 1: Create IAM roles

To enable Amazon Aurora to access Amazon Comprehend and Amazon S3, you need to create two IAM roles. One role grants Comprehend read-only access, while the other grants S3 read-only access. To create the roles, navigate to the IAM dashboard, select Roles, and choose Create Role. For each role, choose AWS service as the type of trusted entity, select RDS as the use case, and add the appropriate policy for Comprehend or S3 access. Name the roles TutorialAuroraComprehendRole and TutorialAuroraS3Role, respectively. Confirm that the roles are created.

### Step 2: Create Aurora database instance

To create an Aurora PostgreSQL database instance, open the Amazon RDS console and select "Create database." Choose "Standard Create" as the database creation method, and select "Amazon Aurora" as the engine type, "Amazon Aurora with PostgreSQL compatibility" as the edition, and "Provisioned" as the capacity type. Name the database "aurora-ml-db" and choose a password for your DB instance. Select the "db.t3.medium" instance type, and choose not to create an Aurora replica. Create a new VPC and subnet group, and select "Yes" for public access. Create a new VPC security group and select password authentication. When the Aurora DB instance is created, choose the "Connectivity & security" tab to add IAM roles to the cluster for Comprehend and S3Import features, and make a note of the instance endpoint DNS name for connecting to the database.

### Step 3: Download and install a PostgreSQL Client
To connect to your Aurora PostgreSQL database instance, you need to download and install a PostgreSQL client. You can use any preferred client, such as [pgAdmin](https://www.pgadmin.org/download/) or [SQL Workbench](https://aws.amazon.com/getting-started/tutorials/create-connect-postgresql-db/). The tutorial provides instructions for downloading and installing the pgAdmin client. Note that the client must be run on the same device and network where you created the DB instance, and the database security group is configured to allow connection only from that device. Once you have installed the client, you can proceed to Step 4.

### Step 4: Connect to the Aurora DB instance
In this step, you use the pgAdmin 4 PostgreSQL Client to connect to the Aurora PostgreSQL DB instance. You start the client, add a new server named TutorialServer, and enter the database cluster endpoint and password. Then, you expand the TutorialServer navigation tree and open a query tool to run queries against your DB instance.

### Step 5: Query database with Amazon Comprehend
In this step, you install extensions for machine learning and Amazon S3 access. Then, you set up and query a sample table. Finally, you load sample data from a customer review dataset and run queries on the customer reviews for sentiment analysis and confidence.

n the query editor, run the following statement to install the Amazon ML services extension for model inference.
```
CREATE EXTENSION IF NOT EXISTS aws_ml CASCADE;
```

Run the following statement to create your sample table named comments.
```
CREATE TABLE IF NOT EXISTS comments (
    comment_id serial PRIMARY KEY, 
    comment_text VARCHAR(255) NOT NULL
);
```

 Add data to your comments table using the following statement.
 ```
INSERT INTO comments (comment_text)
VALUES ('This is very useful, thank you for writing it!');
INSERT INTO comments (comment_text)
VALUES ('Awesome, I was waiting for this feature.');
INSERT INTO comments (comment_text)
VALUES ('An interesting write up, please add more details.');
INSERT INTO comments (comment_text)
VALUES ('I do not like how this was implemented.');
```

Run the following statement to call the aws_comprehend.detect sentiment function.
```
SELECT * FROM comments, aws_comprehend.detect_sentiment(comments.comment_text, 'en') as s
```

Run the following statement to install the Amazon S3 service extension. This extension allows you to load data from Amazon S3 into the Aurora DB instance from SQL.
```
CREATE EXTENSION IF NOT EXISTS aws_s3 CASCADE;
```

Copy and paste the following code to create your table named review_simple.
```
create table review_simple
    (
        marketplace char(2),
        customer_id varchar(20),
        review_id varchar(20) primary key,
        product_id varchar(20),
        product_parent varchar(20),
        product_title text,
        product_category varchar(20),
        star_rating int,
        helpful_votes int,
        total_votes int,
        vine char,
        verified_purchase char,
        review_headline varchar(255),
        review_body text,
        review_date date,
        scored_sentiment varchar(20),
        scored_confidence float4
)
```

Run the following statement to load the data directly from Aurora PostgreSQL.

Note: In production, you may choose to use AWS Glue or another ETL process to load the data.
```
select aws_s3.table_import_from_s3(
   'review_simple', 'marketplace,  customer_id,review_id,product_id, 
    product_parent, product_title, product_category, star_rating,  
    helpful_votes, total_votes, vine, verified_purchase,review_headline,    
    review_body, review_date',
    '(FORMAT CSV, HEADER true, DELIMITER E''\t'', QUOTE ''|'')',
    'amazon-reviews-pds',
    'tsv/sample_us.tsv',
    'us-east-1'
)
```

When you loaded the data, the scored_sentiment and scored_confidence columns in the table were ignored; the data set loaded from S3 didn’t contain those columns. Now, you'll use Comprehend to evaluate the sentiment, and use the result to update those columns in the table. Run the following statement to call Comprehend and update the table.
```
update review_simple
   set scored_sentiment = s.sentiment, scored_confidence = s.confidence
  from review_simple as src, 
       aws_comprehend.detect_sentiment( src.review_body, 'en') as s
 where src.review_id = review_simple.review_id
   and src.scored_sentiment is null
```
 Run the following statement to see the returned data.
 ```
 select customer_id, review_id, review_body, scored_sentiment, scored_confidence from review_simple
```

Run the following statement to see the data summarized based on the sentiment returned by Comprehend.
```
select scored_sentiment,count(*) as nReviews from review_simple group by scored_sentiment
```
Run the following statement to query the data based on a confidence threshold of > .9.

```
select scored_sentiment,count(*) as nReviews from review_simple where scored_confidence > .9
group by scored_sentiment 
```
In this query, you're leveraging the score sentiment and confidence values you saved in the table. This is an example of the flexibility and performance gained by saving the sentiment directly in the database. The ability to do this directly in the database can give individuals more comfortable with SQL easier access to the data.

## Conclusion
Amazon Aurora machine learning provides an easy and efficient way to integrate machine learning capabilities into your applications without having prior machine learning experience or building custom integrations. By following this tutorial, you learned how to create an Aurora PostgreSQL database instance, integrate with Amazon Comprehend for sentiment analysis, and analyze sentiments of records in the database table using a customer reviews dataset.

With Aurora machine learning, developers can run low-latency, real-time use cases such as fraud detection, ad targeting, and product recommendations by calling Amazon SageMaker or Amazon Comprehend for a wide variety of ML algorithms directly from Aurora.

We hope this tutorial has been helpful in getting started with Aurora machine learning and exploring its capabilities. Please feel free to provide feedback and suggestions for improving this tutorial.


## License & Credits
This project is based on the AWS Hands-on Project tutorial called [Perform sentiment analysis with Amazon Aurora ML integration](https://aws.amazon.com/it/getting-started/hands-on/sentiment-analysis-amazon-aurora-ml-integration/?trk=gs_card) available on [AWS Hands-on official resurces](https://aws.amazon.com/it/getting-started/hands-on/) . 

[![MIT License](https://img.shields.io/badge/license-MIT-blue)](https://opensource.org/license/mit/)


## Contributors

[Daniele Bocchino](https://danielebocchino.github.io/)

[![GitHub Followers](https://img.shields.io/github/followers/DanieleBocchino?style=social)](https://github.com/DanieleBocchino)  
[![LinkedIn Connect](https://img.shields.io/badge/LinkedIn-Connect-blue?style=social&logo=linkedin)](https://www.linkedin.com/in/daniele-bocchino-aa602a20b/)
