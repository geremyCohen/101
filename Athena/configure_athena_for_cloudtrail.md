# Configure Athena for CloudTrail

## props

Portions of this tutorial are taken and condensed from https://aws.amazon.com/blogs/big-data/aws-cloudtrail-and-amazon-athena-dive-deep-to-analyze-security-compliance-and-operational-activity/.

## CloudTrail Refresh

AWS CloudTrail and Amazon Athena help make it easier by combining the detailed CloudTrail log files with the power of the Athena SQL engine to easily find, analyze, and respond to changes and activities in an AWS account.

As seen in the previous CloudTrail section, CloudTrail records API calls and account activities and publishes these events to Amazon S3.  Each event carries information such as who performed the action, when the action was done, which resources were impacted, and many more details.

## CloudTrail + Athena

Amazon Athena uses Apache Hive’s data definition language (DDL) to create tables and Presto, a distributed SQL engine, to run queries. Apache Hive does not natively support files in JSON, so we’ll have to use a SerDe to help Hive understand how the records should be processed. A SerDe interface is a combination of a serializer and deserializer.

With that said, lets see how we can use Athena to query S3 CloudTrail data.


## Configuring Athena

1. Navigate to the [Athena Console](https://us-west-2.console.aws.amazon.com/athena/home?region=us-west-2)

1. Click *Get Started*

1. Paste the following into the Query Window to create a table that is backed by CloudTrail's S3 gzip data.  Be sure to replace <Your CloudTrail s3 bucket name> with the actual name of the S3 bucket CloudTrail is storing its logs in:

```
CREATE EXTERNAL TABLE cloudtrail_logs (
eventversion STRING,
userIdentity STRUCT<
  type:STRING,
  principalid:STRING,
  arn:STRING,
  accountid:STRING,
  invokedby:STRING,
  accesskeyid:STRING,
  userName:STRING,
  sessioncontext:STRUCT<
    attributes:STRUCT<
      mfaauthenticated:STRING,
      creationdate:STRING>,
    sessionIssuer:STRUCT<
      type:STRING,
      principalId:STRING,
      arn:STRING,
      accountId:STRING,
      userName:STRING>>>,
eventTime STRING,
eventSource STRING,
eventName STRING,
awsRegion STRING,
sourceIpAddress STRING,
userAgent STRING,
errorCode STRING,
errorMessage STRING,
requestParameters STRING,
responseElements STRING,
additionalEventData STRING,
requestId STRING,
eventId STRING,
resources ARRAY<STRUCT<
  ARN:STRING,accountId:
  STRING,type:STRING>>,
eventType STRING,
apiVersion STRING,
readOnly STRING,
recipientAccountId STRING,
serviceEventDetails STRING,
sharedEventID STRING,
vpcEndpointId STRING
)
ROW FORMAT SERDE 'com.amazon.emr.hive.serde.CloudTrailSerde'
STORED AS INPUTFORMAT 'com.amazon.emr.cloudtrail.CloudTrailInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
LOCATION 's3://<Your CloudTrail s3 bucket name>/AWSLogs/';
```

1. Click *Run Query* to create the table.

1. Refresh the page, and notice there is a new table called cloudtrail_logs.  We'll query against this table next.
