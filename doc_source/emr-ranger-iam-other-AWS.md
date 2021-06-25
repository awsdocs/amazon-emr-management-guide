# IAM role for other AWS services<a name="emr-ranger-iam-other-AWS"></a>

This role provides users who are not trusted execution engines with credentials to interact with AWS services, if needed\. Do not use this IAM role to allow access to Amazon S3 data, unless it's data that should be accessible by all users\.

This role will be assumed by the EC2 Instance Profile Role\. Use the following example to create a trust policy that allows the IAM role for Apache Ranger to be assumed by the EC2 instance profile role\.

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