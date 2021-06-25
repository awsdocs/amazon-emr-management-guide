# EC2 instance profile<a name="emr-ranger-iam-ec2"></a>

Amazon EMR uses an IAM service roles to perform actions on your behalf to provision and manage clusters\. The service role for cluster EC2 instances, also called the EC2 instance profile for Amazon EMR, is a special type of service role assigned to every EC2 instance in a cluster at launch\.

To define permissions for EMR cluster interaction with Amazon S3 data and with Hive metastore protected by Apache Ranger and other AWS services, define a custom EC2 instance profile to use instead of the EMR\_EC2\_DefaultRole when you launch your cluster\.

For more information, see [Service role for cluster EC2 instances \(EC2 instance profile\)](emr-iam-role-for-ec2.md) and [Customize IAM roles](emr-iam-roles-custom.md)\.

You need to add the following statements to the default EC2 Instance Profile for Amazon EMR to be able to tag sessions and access the AWS Secrets Manager that stores TLS certificates\.

```
    {
      "Sid": "AllowAssumeOfRolesAndTagging",
      "Effect": "Allow",
      "Action": ["sts:TagSession", "sts:AssumeRole"],
      "Resource": [
        "arn:aws:iam::<AWS_ACCOUNT_ID>:role/<RANGER_ENGINE-PLUGIN_DATA_ACCESS_ROLE_NAME>",
        "arn:aws:iam::<AWS_ACCOUNT_ID>:role/<RANGER_USER_ACCESS_ROLE_NAME>"
      ]
    },
    {
        "Sid": "AllowSecretsRetrieval",
        "Effect": "Allow",
        "Action": "secretsmanager:GetSecretValue",
        "Resource": [
            "arn:aws:secretsmanager:<REGION>:<AWS_ACCOUNT_ID>:secret:<PLUGIN_TLS_SECRET_NAME>*",
            "arn:aws:secretsmanager:<REGION>:<AWS_ACCOUNT_ID>:secret:<ADMIN_RANGER_SERVER_TLS_SECRET_NAME>*"
        ]
    }
```

**Note**  
For the Secrets Manager permissions, do not forget the wildcard \("\*"\) at the end of the secret name or your requests will fail\. The wildcard is for secret versions\.

**Note**  
Limit the scope of the AWS Secrets Manager policy to only the certificates that are required for provisioning\.