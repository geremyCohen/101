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

1. 
