# Capstone Project

## Summary of the project

The purpose of this project is to identify and gather the dataset (at least two sources and more than 1 million rows) and use it to combine what I have learned throughout the Data Engineer Nanodegree program.
This project uses the Yelp and Tripadvisor dataset with 2 data sources: review datasource and business datasource to model Apache Cassandra database, build ETL pipeline and run data quality checks to ensure the pipeline ran as expected.

The project follows the follow steps:
Step 1: Scope the Project and Gather Data

Step 2: Explore and Assess the Data

Step 3: Define the Data Model

Step 4: Run ETL to Model the Data

Step 5: Complete Project Write Up


## Step 1: Scope the Project and Gather Data

A startup wants to analyze the data they are collecting from different sources. The data will come in various formats and in different models but it will concern one area, it will be data collected from websites allowing users to post reviews on restaurants, hotels and other facilities. The analysis team is particularly interested in understanding what are the opinions on different businesses. Currently, there is no easy way to query the data to generate the results.

The startup needs a data engineer to create an Apache Cassandra database for the analysis. The analytics team has provided queries they would like to run of different data sets.

## Step 2: Explore and Assess the Data

There are 2 different sources to analyze as for now:
The Yelp dataset - “it is a subset of our businesses, reviews, and user data for use in personal, educational, and academic purposes. Available as JSON files, use it to teach students about databases, to learn NLP, or for sample production data while you learn how to make mobile apps.”
Source: https://www.yelp.com/dataset
The Yelp dataset is 5,34 GB dataset divided into several files in JSON format. I will use file yelp_reviews.json and yelp_business.json that are about 1GB  and have more than 1 million of rows
Hotel Rviews - “a list of 1,000 hotels and their reviews provided by Datafiniti's Business Database. The dataset includes hotel location, name, rating, review data, title, username, and more”.
Source: https://www.kaggle.com/datasets/datafiniti/hotel-reviews
This datasource is a 238MB file in CSV format.

To explore the dataset I imported it to Jupyter Notebook to get an overview on these data sets . I cleaned it: new lines are removed from json, postal code’s are changed to be 5 character long and not numeric values are removed, date format is unified.

## Step 3: Define the Data Model

Diagram with sources and outcome tables:

![etl_schema.png](assets/etl_schema.png)

Result tables:

![table3.png](assets/table3.png)

![table2.png](assets/table2.png)

![table1.png](assets/table1.png)

## Step 4: Run ETL to Model the Data

Steps for running ETL model can be found in jupyter notebook.

Data Dictionary:

### table: reviews_by_id_source

| column      | type | description                                          |
| ------------- | ------ | ------------------------------------------------------ |
| review_id   | text | the unique review id                                 |
| source      | text | the source of the data, currently yelp or datafiniti |
| review_text | text | the review itself                                    |
| rating      | int  | the rating of the place associated with review       |
| review_date | date | the date of the review, formatted YYYY-MM-DD         |


### table: places_by_city

| column      | type | description                                          |
| ------------- | ------ | ------------------------------------------------------ |
| city        | text | the city of the reviewed place                       |
| review_id   | text | the unique review id                                 |
| place       | text | The reviewed place’s name                            |
| review_text | text | the review itself                                    |
| rating      | int  | the rating of the place associated with review       |
| source      | text | the source of the data, currently yelp or datafiniti |

### table: place_by_review

| column      | type | description                                          |
| ------------- | ------ | ------------------------------------------------------ |
| review_id   | text | the unique review id                                 |
| source      | text | the source of the data, currently yelp or datafiniti |
| place       | text | The reviewed place’s name                           |
| city        | text | the city of the reviewed place                       |
| address     | text | the full address of the place                        |
| postal_code | int  | the postal code of the reviewed place                |


## Step 5: Complete Project Write Up

### Tools and Technologies
Programming language - Python. It was chosen because it provides a huge number of libraries to work on Big Data, it is easy to use 

Library - Pandas was used because it can handle missing data, cleaning up the data and it supports multiple file formats.

Database - Cassandra - was chosen as it is a perfect choise for handling a high volume of data with ease. As for the project we need fast wrties and reads only and the queries are known before modeling database - we do not need relational database.

### Data Update Frequency
Tables can be updated whenever a new source is identified. All tables should be update in an append-only mode.

### Data Model Justification
The database needed to be model in such way that it will answear on following three questions of the data the data model:

1. Give me the review text, rating and date of review in the reviews history having review_id and source.
2. Give me the name of the reviewed place, review text, rating, source of review in the reviews history having city and review_id.
3. Give me name of the place, city, address and postal_code having name of the place review_id and source.

Therefore in the project I am using flexible schema which Cassandra database is providing. Data in Cassandra is modeled around specific queries. It is optimal solution when data engineers know what queries exactly need to be supported. Cassandra queries can be performed more rapidly because the database uses a single table approach, in contrary to the relational database model where data is stored in multiple tables. In this project we use Yelp dataset that is a snapshot of relational database. In order to prepare it for Cassandra I needed to denormalize 2 tables by joining them with foreign key to be able to store needed by queries data in one table instead of two. 

### Quality test 1

The first quality test is checking the total number of rows that existed in the source and compering it to count of rows in final tables. Results are presented below:

![quality_test1](assets/quality_test1.png)

### Quality test 2

The second quality test is checking if both of the sources are present in final set and also checking if they are correctly distributed (meaning by number of rows). Results are presented below:

![quality_test2](assets/quality_test2.png)

### Quality test 3 - output of sample queries

Last quality check I performed  is ensuring that the final dataset(s) contains or does not contain some values that should be in the final set.

#### sample query on table: reviews_by_id_source

![reviews_by_id_source](assets/sample_query2.png)

#### sample query on table: places_by_city

![places_by_city](assets/sample_query3.png)

#### sample query on table: place_by_review

![place_by_review](assets/sample_query1.png)

### Future Design Considerations

If the data was increased by 100x.
Using Spark for data processing or AWS EMR distributed data cluster is needed. Also for inserting I would use dsbulk.

If the pipelines were run on a daily basis by 7am.
Using Apache Airflow for running ETL data pipeline to regularly to append data.

The database needed to be accessed by 100+ people.
As cassandra has linear scalability, it is easy to add more nodes to the system so performance will increase linearly.
