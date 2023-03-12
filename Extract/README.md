# Data Extract Module
Data extract module 1) dumps data from spotify 2) extracts useful data into AWS S3. Highlighted area below is the architecture of Data extracted module.

![serverless-extract](https://user-images.githubusercontent.com/43290363/224507943-eeddc6ea-2616-4d23-bb96-f50ecebfc280.png)

## Fetch data from Spotify
> ⚠️ All AWS services must in the same region. Otherwise, some pipeline would not work properly. 

Instead of using python request, Spotipy provides an easy way to access Spotify Web API. Pick a playlist and fetch data on daily basis. [How to access API with python](https://github.com/sombiee/spotify-serverless-ETL-pipeline) I used. Or detailed description is on [Spotipy docs](https://spotipy.readthedocs.io/en/2.22.1/). Daily triggered event is set on AWS EventBridge.

## Data extraction on AWS
Since I am tracking `Top 50 Global` playlist which updates daily, I need to get data dumped daily. Chronical job or event-triggered job will be scheduled using AWS EventBridge under CloudWatch. Once jobs are set, AWS Lambda will be triggered and extract the data in need. Extracted data will be stored in AWS S3 as a raw data for analysis. 

### CloudWatch/ EventBridge
Amazon Web Service(AWS) offers a broad set of global cloud-based products on-demand, available in seconds, with pay-as-you-go pricing. Despite of its accessibility, AWS is often considered as pricey option due to the 'pay-as-you-go' pricing policy. Security policy always comes first but to bridgade unexpected bills, CloudWatch budget limit is used. This is just a toy project, so set the buget as `Zero spend budget`, or `Monthly cost budget` and set the budget less than how much you expect to pay.

AWS EventBridge offers two ways to set chronical jobs: `cronjob` and `rate`. Choose a command in your favor and set as Lambda trigger configuration.

### Lambda
> ⚠️ Spotipy package should be ready as .zip format in order to be used on AWS Lambda

AWS Lambda is a serverless, event-driven compute service that lets your code run without provisioning or managing servers. Combining Lambda with EventBridge, you can run your code on a set period.

In this project, we are using `python 3.8` with additional packages that AWS offers: `Spotipy`. 

### S3
> ⚠️ Remind that S3 does not charge upto first 1 million objects as of Feb, 2023. Policy could be changed in the future. 

AWS Simple Storage Service(S3) is an object storage service offering industry-leading scalability, data availiability, security and performance. In this module, S3 is used to store extracted data. 

![s3-extraction](https://user-images.githubusercontent.com/43290363/224534458-9e6eb3fa-16da-4a1e-b8de-7744f3c9d631.png)

S3 Stores data in a `bucket`. Bucket is consisted of objects aka data file. Bucket can divided into folders, which let S3 act as database system. (Detailed DB Schema/ cataloging is infered on AWS Glue). 

To process raw data only once, separate raw data storage folder into `to_process` and `processed`. Once the data is extracted and stored in S3 `raw_data/to_processed/`, raw data will be transformed in transformation module and loaded into `raw_data/processed/` folder.