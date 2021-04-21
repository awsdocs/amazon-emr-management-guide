# Create a Customized EC2 Instance Profile<a name="emr-lf-create-instance-profile"></a>

To orchestrate EMR cluster access to Lake Formation, you should define a customized EC2 instance profile \(service role\) that you will specify when you create your cluster\. For more information about creating a customized role, see [Creating a Service Role for Cluster EC2 Instances With Least\-Privilege Permissions](emr-iam-role-for-ec2.md#emr-ec2-role-least-privilege)\.

## Public Subnet<a name="emr-lf-iam-role-EC2-public"></a>

If your cluster will be in a public subnet, use the following policy for your customized EC2 instance profile\. Replace *My\-S3\-Bucket\-for\-IdP\-Metadata* with the S3 bucket containing your identify provider \(IdP\) metadata\. Replace the *MyEMRClusterLogs* with an S3 bucket for storing EMR logs\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Resource": {
                "arn:aws:s3:::My-S3-Bucket-for-IdP-Metadata/*",
            },
            "Action": "s3:GetObject"
        },
        {
            "Effect": "Allow",
            "Resource": {
                "arn:aws:s3:::My-S3-Bucket-for-IdP-Metadata"
            },
            "Action": "s3:ListBucket"
        },
        {
            "Effect": "Allow",
            "Resource": {
                "arn:aws:s3:::MyEMRClusterLogs/*"
            },
            "Action": "s3:Put*"
        }
    ]
}
```

## Private Subnet<a name="emr-lf-iam-role-EC2-private"></a>

If your cluster will be in a private subnet, use the following policy for your customized EC2 instance profile\. Replace *My\-S3\-Bucket\-for\-IdP\-Metadata* with the S3 bucket containing your identify provider \(IdP\) metadata\. Replace the *MyEMRClusterLogs* with the S3 bucket for storing EMR logs\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Resource": {
        "arn:aws:s3:::My-S3-Bucket-for-IdP-Metadata/*",
        "arn:aws:s3:::packages.*.amazonaws.com/*",
        "arn:aws:s3:::repo.*.amazonaws.com/*",
      },
      "Action": "s3:GetObject"
    }, {
      "Effect": "Allow",
      "Resource": {
        "arn:aws:s3:::My-S3-Bucket-for-IdP-Metadata"
      },
      "Action": "s3:ListBucket"
    }, {
      "Effect": "Allow",
      "Resource": {
        "arn:aws:s3:::MyEMRClusterLogs/*"
      },
      "Action": "s3:Put*"
    }
  ]
}
```