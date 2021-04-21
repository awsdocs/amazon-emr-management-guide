# IAM Managed Policy for Read\-Only Access \(On Path to Deprecation\)<a name="emr-managed-policy-readonly"></a>

The `AmazonElasticMapReduceReadOnlyAccess` managed policy is on the path to deprecation\. You cannot attach this policy when launching new clusters\. `AmazonElasticMapReduceReadOnlyAccess` has been replaced with [`AmazonEMRReadOnlyAccessPolicy_v2`](emr-managed-policy-readonly-v2.md) as the Amazon EMR default managed policy\. The content of this policy statement is shown in the following snippet\. Wildcard characters for the `elasticmapreduce` element specify that only actions that begin with the specified strings are allowed\. Keep in mind that because this policy does not explicitly deny actions, a different policy statement may still be used to grant access to specified actions\.

**Note**  
You can also use the AWS Management Console to view the policy\.

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Effect": "Allow",
 6.       "Action": [
 7.         "elasticmapreduce:Describe*",
 8.         "elasticmapreduce:List*",
 9.         "elasticmapreduce:ViewEventsFromAllClustersInConsole"
10.         "s3:GetObject",
11.         "s3:ListAllMyBuckets",
12.         "s3:ListBucket",
13.         "sdb:Select",
14.         "cloudwatch:GetMetricStatistics"
15.       ],
16.       "Resource": "*"
17.     }
18.   ]
19. }
```