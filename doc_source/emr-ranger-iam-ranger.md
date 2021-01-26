# IAM Role for Apache Ranger<a name="emr-ranger-iam-ranger"></a>

This role provides credentials for trusted execution engines, such as Apache Hive and Amazon EMR Record Server [Amazon EMR Components](emr-lf-components.md), to access Amazon S3 data\. Use only this role to access Amazon S3 data, including any KMS keys, if you are using S3 SSE\-KMS\.

This role must be created with the minimum policy stated in the following example\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "CloudwatchLogsPermissions",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Effect": "Allow",
      "Resource": [
          "arn:aws:logs:<REGION>:<AWS_ACCOUNT_ID>:<CLOUDWATCH_LOG_GROUP_NAME_IN_SECURITY_CONFIGURATION>:*"
      ]
    },
    {
      "Sid": "BucketPermissionsInS3Buckets",
      "Action": [
        "s3:CreateBucket",
        "s3:DeleteBucket",
        "s3:ListAllMyBuckets",
        "s3:ListBucket"
      ],
      "Effect": "Allow",
      "Resource": [
 *"arn:aws:s3:::bucket1",
        "arn:aws:s3:::bucket2"*
        ]
    },
    {
      "Sid": "ObjectPermissionsInS3Objects",
      "Action": [
        "s3:GetObject",
        "s3:DeleteObject",
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": [ 
 *"arn:aws:s3:::bucket1/*",
          "arn:aws:s3:::bucket2/*"
*        ]
    }
  ]
}
```

**Important**  
The asterisk "\*" at the end of the CloudWatch Log Resource must be included to provide permission to write to the log streams\.

**Note**  
If you are using EMRFS consistency view or S3\-SSE encryption, add permissions to the DynamoDB tables and KMS keys so that execution engines can interact with those engines\.

The IAM role for Apache Ranger is assumed by the EC2 Instance Profile Role\. Use the following example to create a trust policy that allows the IAM role for Apache Ranger to be assumed by the EC2 instance profile role\.

```
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<AWS_ACCOUNT_ID>:role/<EC2 INSTANCE PROFILE ROLE NAME eg. EMR_EC2_DefaultRole>"
      },
      "Action": ["sts:AssumeRole", "sts:TagSession"]
    }
```