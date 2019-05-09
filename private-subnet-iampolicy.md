# Minimum Amazon S3 Policy for Private Subnet<a name="private-subnet-iampolicy"></a>

For private subnets, at a minimum you must provide the ability for Amazon EMR to access Amazon Linux repositories and Amazon EMR service support log buckets\. The following policy provides these permissions\. Replace *MyRegion* with the region where your log buckets reside, for example `us-east-1`:

```
{
   "Version": "2008-10-17",
   "Statement": [
       {
           "Sid": "AmazonLinuxAMIRepositoryAccess",
           "Effect": "Allow",
           "Principal": "*",
           "Action": "s3:GetObject",
           "Resource": [
               "arn:aws:s3:::packages.*.amazonaws.com/*",
               "arn:aws:s3:::repo.*.amazonaws.com/*"
           ]
       },
       {
           "Sid": "AccessToEMRLogBucketsForSupport",
           "Effect": "Allow",
           "Principal": "*",
           "Action": [
               "s3:Put*",
               "s3:Get*",
               "s3:Create*",
               "s3:Abort*",
               "s3:List*"
           ],
           "Resource": [
               "arn:aws:s3:::aws157-logs-prod-MyRegion/*",
               "arn:aws:s3:::aws157-logs-prod/*"
           ]
       }
   ]
}
```