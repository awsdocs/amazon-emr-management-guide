# EMRFS CLI Reference<a name="emrfs-cli-reference"></a>

The EMRFS CLI is installed by default on all cluster master nodes created using Amazon EMR release version 3\.2\.1 or later\. You can use the EMRFS CLI to manage the metadata for consistent view\. 

**Note**  
The emrfs command is only supported with VT100 terminal emulation\. However, it may work with other terminal emulator modes\.

## emrfs top\-level command<a name="emrfs-top-level"></a>

The emrfs top\-level command supports the following structure\.

```
emrfs [describe-metadata | set-metadata-capacity | delete-metadata | create-metadata | \
list-metadata-stores | diff | delete | sync | import ] [options] [arguments]
```

Specify \[options\], with or without \[arguments\] as described in the following table\. For \[options\] specific to sub\-commands \(`describe-metadata`, `set-metadata-capacity`, etc\.\), see each sub\-command below\.


**\[options\] for emrfs**  

| Option  | Description  | Required  | 
| --- | --- | --- | 
|  `-a AWS_ACCESS_KEY_ID \| --access-key AWS_ACCESS_KEY_ID`  |  The AWS access key you use to write objects to Amazon S3 and to create or access a metadata store in DynamoDB\. By default, *AWS\_ACCESS\_KEY\_ID* is set to the access key used to create the cluster\.  |  No  | 
|  `-s AWS_SECRET_ACCESS_KEY \| --secret-key AWS_SECRET_ACCESS_KEY`  |  The AWS secret key associated with the access key you use to write objects to Amazon S3 and to create or access a metadata store in DynamoDB\. By default, *AWS\_SECRET\_ACCESS\_KEY* is set to the secret key associated with the access key used to create the cluster\.  |  No  | 
|  `-v \| --verbose`  |  Makes output verbose\.  |  No  | 
|  `-h \| --help`  |  Displays the help message for the `emrfs` command with a usage statement\.  |  No  | 

## emrfs describe\-metadata sub\-command<a name="emrfs-describe-metadata"></a>


**\[options\] for emrfs describe\-metadata**  

| Option  | Description  | Required  | 
| --- | --- | --- | 
|  `-m METADATA_NAME \| --metadata-name METADATA_NAME`  |  *METADATA\_NAME* is the name of the DynamoDB metadata table\. If the *METADATA\_NAME* argument is not supplied, the default value is `EmrFSMetadata`\.  |  No  | 

**Example emrfs describe\-metadata example**  <a name="emrfs-describe-metadata"></a>
The following example describes the default metadata table\.  

```
$ emrfs describe-metadata
EmrFSMetadata
  read-capacity: 400
  write-capacity: 100
  status: ACTIVE
  approximate-item-count (6 hour delay): 12
```

## emrfs set\-metadata\-capacity sub\-command<a name="emrfs-set-metadata-capacity"></a>


**\[options\] for emrfs set\-metadata\-capacity**  

| Option  | Description  | Required  | 
| --- | --- | --- | 
|  `-m METADATA_NAME \| --metadata-name METADATA_NAME`  |  *METADATA\_NAME* is the name of the DynamoDB metadata table\. If the *METADATA\_NAME* argument is not supplied, the default value is `EmrFSMetadata`\.  |  No  | 
|  `-r READ_CAPACITY \| --read-capacity READ_CAPACITY`  |  The requested read throughput capacity for the metadata table\. If the *READ\_CAPACITY* argument is not supplied, the default value is `400`\.  |  No  | 
|  `-w WRITE_CAPACITY \| --write-capacity WRITE_CAPACITY`  |  The requested write throughput capacity for the metadata table\. If the *WRITE\_CAPACITY* argument is not supplied, the default value is `100`\.  |  No  | 

**Example emrfs set\-metadata\-capacity example**  
The following example sets the read throughput capacity to `600` and the write capacity to `150` for a metadata table named `EmrMetadataAlt`\.  

```
$ emrfs set-metadata-capacity --metadata-name EmrMetadataAlt  --read-capacity 600 --write-capacity 150
  read-capacity: 400
  write-capacity: 100
  status: UPDATING
  approximate-item-count (6 hour delay): 0
```

## emrfs delete\-metadata sub\-command<a name="emrfs-delete-metadata"></a>


**\[options\] for emrfs delete\-metadata**  

| Option  | Description  | Required  | 
| --- | --- | --- | 
|  `-m METADATA_NAME \| --metadata-name METADATA_NAME`  |  *METADATA\_NAME* is the name of the DynamoDB metadata table\. If the *METADATA\_NAME* argument is not supplied, the default value is `EmrFSMetadata`\.  |  No  | 

**Example emrfs delete\-metadata example**  
The following example deletes the default metadata table\.  

```
$ emrfs delete-metadata
```

## emrfs create\-metadata sub\-command<a name="emrfs-create-metadata"></a>


**\[options\] for emrfs create\-metadata**  

| Option  | Description  | Required  | 
| --- | --- | --- | 
|  `-m METADATA_NAME \| --metadata-name METADATA_NAME`  |  *METADATA\_NAME* is the name of the DynamoDB metadata table\. If the *METADATA\_NAME* argument is not supplied, the default value is `EmrFSMetadata`\.  |  No  | 
|  `-r READ_CAPACITY \| --read-capacity READ_CAPACITY`  |  The requested read throughput capacity for the metadata table\. If the *READ\_CAPACITY* argument is not supplied, the default value is `400`\.  |  No  | 
|  `-w WRITE_CAPACITY \| --write-capacity WRITE_CAPACITY`  |  The requested write throughput capacity for the metadata table\. If the *WRITE\_CAPACITY* argument is not supplied, the default value is `100`\.  |  No  | 

**Example emrfs create\-metadata example**  
The following example creates a metadata table named `EmrFSMetadataAlt`\.  

```
$ emrfs create-metadata -m EmrFSMetadataAlt
Creating metadata: EmrFSMetadataAlt
EmrFSMetadataAlt
  read-capacity: 400
  write-capacity: 100
  status: ACTIVE
  approximate-item-count (6 hour delay): 0
```

## emrfs list\-metadata\-stores sub\-command<a name="emrfs-list-metadata-stores"></a>

The emrfs list\-metadata\-stores sub\-command has no \[options\]\. 

**Example list\-metadata\-stores example**  
The following example lists your metadata tables\.  

```
$ emrfs list-metadata-stores
  EmrFSMetadata
```

## emrfs diff sub\-command<a name="emrfs-diff"></a>


**\[options\] for emrfs diff**  

| Option  | Description  | Required  | 
| --- | --- | --- | 
|  `-m METADATA_NAME \| --metadata-name METADATA_NAME`  |  *METADATA\_NAME* is the name of the DynamoDB metadata table\. If the *METADATA\_NAME* argument is not supplied, the default value is `EmrFSMetadata`\.  |  No  | 
|  *s3://s3Path*  |  The path to the Amazon S3 bucket to compare with the metadata table\. Buckets sync recursively\.  |  Yes  | 

**Example emrfs diff example**  
The following example compares the default metadata table to an Amazon S3 bucket\.  

```
$ emrfs diff s3://elasticmapreduce/samples/cloudfront
BOTH | MANIFEST ONLY | S3 ONLY
DIR elasticmapreduce/samples/cloudfront
DIR elasticmapreduce/samples/cloudfront/code/
DIR elasticmapreduce/samples/cloudfront/input/
DIR elasticmapreduce/samples/cloudfront/logprocessor.jar
DIR elasticmapreduce/samples/cloudfront/input/XABCD12345678.2009-05-05-14.WxYz1234
DIR elasticmapreduce/samples/cloudfront/input/XABCD12345678.2009-05-05-15.WxYz1234
DIR elasticmapreduce/samples/cloudfront/input/XABCD12345678.2009-05-05-16.WxYz1234
DIR elasticmapreduce/samples/cloudfront/input/XABCD12345678.2009-05-05-17.WxYz1234
DIR elasticmapreduce/samples/cloudfront/input/XABCD12345678.2009-05-05-18.WxYz1234
DIR elasticmapreduce/samples/cloudfront/input/XABCD12345678.2009-05-05-19.WxYz1234
DIR elasticmapreduce/samples/cloudfront/input/XABCD12345678.2009-05-05-20.WxYz1234
DIR elasticmapreduce/samples/cloudfront/code/cloudfront-loganalyzer.tgz
```

## emrfs delete sub\-command<a name="emrfs-delete"></a>


**\[options\] for emrfs delete**  

|  Option  |  Description  |  Required  | 
| --- | --- | --- | 
|  `-m METADATA_NAME \| --metadata-name METADATA_NAME`  |  *METADATA\_NAME* is the name of the DynamoDB metadata table\. If the *METADATA\_NAME* argument is not supplied, the default value is `EmrFSMetadata`\.  |  No  | 
|  *s3://s3Path*  |  The path to the Amazon S3 bucket you are tracking for consistent view\. Buckets sync recursively\.  |  Yes  | 
| \-t TIME \| \-\-time TIME |  The expiration time \(interpreted using the time unit argument\)\. All metadata entries older than the *TIME* argument are deleted for the specified bucket\.  |  | 
|  `-u UNIT \| --time-unit UNIT`  |  The measure used to interpret the time argument \(nanoseconds, microseconds, milliseconds, seconds, minutes, hours, or days\)\. If no argument is specified, the default value is `days`\.  |  | 
|  `--read-consumption READ_CONSUMPTION`   |  The requested amount of available read throughput used for the delete operation\. If the *READ\_CONSUMPTION* argument is not specified, the default value is `400`\.  |  No   | 
|  `--write-consumption WRITE_CONSUMPTION`  |  The requested amount of available write throughput used for the delete operation\. If the *WRITE\_CONSUMPTION* argument is not specified, the default value is `100`\.  |  No  | 

**Example emrfs delete example**  
The following example removes all objects in an Amazon S3 bucket from the tracking metadata for consistent view\.  

```
$ emrfs delete s3://elasticmapreduce/samples/cloudfront
entries deleted: 11
```

## emrfs import sub\-command<a name="emrfs-import"></a>


**\[options\] for emrfs import**  

| Option  | Description  | Required  | 
| --- | --- | --- | 
|  `-m METADATA_NAME \| --metadata-name METADATA_NAME`  |  *METADATA\_NAME* is the name of the DynamoDB metadata table\. If the *METADATA\_NAME* argument is not supplied, the default value is `EmrFSMetadata`\.  |  No  | 
|  *s3://s3Path*  |  The path to the Amazon S3 bucket you are tracking for consistent view\. Buckets sync recursively\.  |  Yes  | 
|  `--read-consumption READ_CONSUMPTION`   |  The requested amount of available read throughput used for the delete operation\. If the *READ\_CONSUMPTION* argument is not specified, the default value is `400`\.  |  No  | 
|  `--write-consumption WRITE_CONSUMPTION`  |  The requested amount of available write throughput used for the delete operation\. If the *WRITE\_CONSUMPTION* argument is not specified, the default value is `100`\.  |  No  | 

**Example emrfs import example**  
The following example imports all objects in an Amazon S3 bucket with the tracking metadata for consistent view\. All unknown keys are ignored\.  

```
$ emrfs import s3://elasticmapreduce/samples/cloudfront
```

## emrfs sync sub\-command<a name="emrfs-sync"></a>


**\[options\] for emrfs sync**  

| Option  | Description  | Required  | 
| --- | --- | --- | 
|  `-m METADATA_NAME \| --metadata-name METADATA_NAME`  |  *METADATA\_NAME* is the name of the DynamoDB metadata table\. If the *METADATA\_NAME* argument is not supplied, the default value is `EmrFSMetadata`\.  |  No  | 
|  *s3://s3Path*  |  The path to the Amazon S3 bucket you are tracking for consistent view\. Buckets sync recursively\.  |  Yes  | 
|  `--read-consumption READ_CONSUMPTION`   |  The requested amount of available read throughput used for the delete operation\. If the *READ\_CONSUMPTION* argument is not specified, the default value is `400`\.  |  No  | 
|  `--write-consumption WRITE_CONSUMPTION`  |  The requested amount of available write throughput used for the delete operation\. If the *WRITE\_CONSUMPTION* argument is not specified, the default value is `100`\.  |  No  | 

**Example emrfs sync command example**  
The following example imports all objects in an Amazon S3 bucket with the tracking metadata for consistent view\. All unknown keys are deleted\.   

```
$ emrfs sync s3://elasticmapreduce/samples/cloudfront
Synching samples/cloudfront                                       0 added | 0 updated | 0 removed | 0 unchanged
Synching samples/cloudfront/code/                                 1 added | 0 updated | 0 removed | 0 unchanged
Synching samples/cloudfront/                                      2 added | 0 updated | 0 removed | 0 unchanged
Synching samples/cloudfront/input/                                9 added | 0 updated | 0 removed | 0 unchanged
Done synching s3://elasticmapreduce/samples/cloudfront            9 added | 0 updated | 1 removed | 0 unchanged
creating 3 folder key(s)
folders written: 3
```

## emrfs read\-sqs sub\-command<a name="emrfs-read-sqs"></a>


**\[options\] for emrfs read\-sqs**  

| Option  | Description  | Required  | 
| --- | --- | --- | 
|  `-q QUEUE_NAME \| --queue-name QUEUE_NAME`  |  *QUEUE\_NAME* is the name of the Amazon SQS queue configured in `emrfs-site.xml`\. The default value is **EMRFS\-Inconsistency\-<jobFlowId>**\.  |  Yes  | 
|  `-o OUTPUT_FILE \| --output-file OUTPUT_FILE`  |  *OUTPUT\_FILE* is the path to the output file on the master node's local file system\. Messages read from the queue are written to this file\.   |  Yes  | 

## emrfs delete\-sqs sub\-command<a name="emrfs-delete-sqs"></a>


**\[options\] for emrfs delete\-sqs**  

| Option  | Description  | Required  | 
| --- | --- | --- | 
|  `-q QUEUE_NAME \| --queue-name QUEUE_NAME`  |  *QUEUE\_NAME* is the name of the Amazon SQS queue configured in `emrfs-site.xml`\. The default value is **EMRFS\-Inconsistency\-<jobFlowId>**\.  |  Yes  | 

## Submitting EMRFS CLI Commands as Steps<a name="emrfs-submit-steps-as-cli"></a>

The following example shows how to use the `emrfs` utility on the master node by leveraging the AWS CLI or API and the `script-runner.jar` to run the `emrfs` command as a step\. The example uses the AWS SDK for Python \(Boto\) to add a step to a cluster which adds objects in an Amazon S3 bucket to the default EMRFS metadata table\. Replace *`j-2AL4XXXXXX5T9` with the ID of the cluster\.*

```
import json
import boto3
from botocore.exceptions import ClientError

# Assign the ID of an existing cluster to the following variable
job_flow_id = 'CLUSTER_ID'

# Define a job flow step. Assign appropriate values as desired.
job_flow_step_01 = {
    'Name': 'Example EMRFS Sync Step',
    'ActionOnFailure': 'CONTINUE',
    'HadoopJarStep': {
        'Jar': 's3://elasticmapreduce/libs/script-runner/script-runner.jar',
        'Args': [
            '/home/hadoop/bin/emrfs',
            'sync',
            's3://elasticmapreduce/samples/cloudfront'
        ]
    }
}

# Add the step(s)
emr_client = boto3.client('emr')
try:
    response = emr_client.add_job_flow_steps(JobFlowId=job_flow_id,
                                             Steps=[json.dumps(job_flow_step_01)])
except ClientError as e:
    print(e.response['Error']['Message'])
    exit(1)

# Output the IDs of the added steps
print('Step IDs:')
for stepId in response['StepIds']:
    print(stepId)
```

You can use the stepId value returned to check the logs for the result of the operation\.