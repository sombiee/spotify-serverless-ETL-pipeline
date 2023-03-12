# Data transformation & load module

## Data transformation module
![transform-architect](https://user-images.githubusercontent.com/43290363/224507989-f5822bc0-cefc-4f62-9529-f132477b1596.png)
### EventBridge
Extracted bulk data propertiess only have `str` datatype. However, from what I extracted, datetime object is included. To manually transform date datatype as `str` into `datetime` object and do further data transformation, we set a trigger when raw data is extracted into S3 `raw_data/to_process/` folder. This trigger is attached to Lambda.  

### Lambda
> ⚠️ Lambda uses python 3.8 with AWS pandas layer

Lambda is activated when data is extracted from Spotify Web API. Bulk data is not a proper form to analyse data. Therefore we transform bulk data into a proper form, which divide bulk data into properties: album, artist, song

Firstly, transform datatype based on their properties and store in `raw_data/processed/` folder. Bulk data is extracted from `.json` so all extracted data's datatype is set as string. For example, `added_date` should be `datetime object` instead of `str`.

Secondly, transform bulk data from `raw_data/processed/` into SQL-excutable form and store in `transformed/{designated folder}`. In this project, we are tracking `Top 50 Global` playlist alonf artists and albums. So divide bulk data into 3 pieces: album, artist, and song. Python Pandas package is used for easier data manupulation. 

Lastly, we do not want to excute the same transformation process on the same dataset. Therefore, delete transformed raw data object from `raw_data/to_process/` folder.

### S3
create or put into the designated folder

![s3-transformation](https://user-images.githubusercontent.com/43290363/224537445-07cb17cc-9615-41e9-a925-cf2f2158e49b.png)

Lambda function is triggered as datatype transformation is executed. During the transformation process, we actively GET data from S3 and CREATE/UPDATE objects in S3. Figure above shows how data is trnasformed on the pipeline. 


## Data load module

### Glue
Amazon Glue is a serverless data integreation service that makes it easy to discover, prepare, and combine data for analytics, machine learning, and data application development. Glue crawls data sources - i.e. S3 - identifies data formats, and suggests schemas to store data. In this way, S3 can operate as data lake. 

S3 is unstructured data storage. Glue will infer data schema for transformed data. Glue crawler will crawl data from S3, Glue data catalog will infer data schema from transformed datatype in transformation module. For serverless data pipeline on AWS, AWS Athena will offer access to explore S3 datalake with SQL.

### Athena
Amazon Athena is a serverless, interactive analytics service which provides a simplified, flexible way to analyze data or build applications from S3 data lake and 25+ data sources, using SQL or Python. Athena is build on open-source Trino and Presto engines and **Apache Spark** frameworks, with no provisioning or configuration effor required. 

Although Athena queries directly from S3, so no additional cost is charged, however, Glue and S3 pricing is still alive. Glue is free for the first million request per month, so keep in mind not to exceed free-tier level. If you are to store chronogical query result, consider linking to local DB server. 