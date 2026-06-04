## S3 bucket setup
- Open aws account open s3 bucket **dataengineersportsbar** and upload the child data folders.
## databricks s3 connection
- go to catalog click on connect choose external location
- click on create external connection
- aws quick start and add s3://dataengineersportsbar and generate token
- lauch quick start this will open cloudformation stack under databricks account creds add the token, click on acknowledge, click on stack 
- open cloudformation --> stack --> wait untill available
- now open databricks check the external data it is sync to s3 bucket.