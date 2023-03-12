# Data transformation & load module

![transform-architect](https://user-images.githubusercontent.com/43290363/224507989-f5822bc0-cefc-4f62-9529-f132477b1596.png)

## Data transformation module
raw -> data format -> album/artist/song

### EventBridge
when data format has changed and moved to processed folder

### Lambda
processed data -> album/artist/song


### S3
create or put into the designated folder

![s3-transformation](https://user-images.githubusercontent.com/43290363/224537445-07cb17cc-9615-41e9-a925-cf2f2158e49b.png)

## Data load module

### Glue
Amazon Glue is a serverless data integreation service that makes it easy to discover, prepare, and combine data for analytics, machine learning, and data application development. Glue crawls data sources - i.e. S3 - identifies data formats, and suggests schemas to store data. In this way, S3 can operate as database-like storage. 

As

crawl data from s3, catalog data into a db-like schema. Now we can explore data with SQL. For serverless pipeline in AWS, we use AWS Athena to explore data with SQL

### Athena
Amazon Athena is a serverless, interactive analytics service which provides a simplified, flexible way to analyze data or build applications from S3 data lake and 25+ data sources, using SQL or Python. Athena is build on open-source Trino and Presto engines and **Apache Spark** frameworks, with no provisioning or configuration effor required. 

Although Athena queries directly from S3, so no additional cost is charged, however, Glue and S3 pricing is still alive. Glue is free for the first million request per month, so keep in mind not to exceed free-tier level. If you are to store chronogical query result, consider liking to local DB server. 