# IAM managed policy for read\-only access \(v2 managed default policy\)<a name="emr-managed-policy-readonly-v2"></a>

To grant read\-only privileges to Amazon EMR, attach the **AmazonEMRReadOnlyAccessPolicy\_v2** managed policy\. This default managed policy replaces the [`AmazonElasticMapReduceReadOnlyAccess`](emr-managed-policy-readonly.md) managed policy\. The content of this policy statement is shown in the following snippet\. Compared with the `AmazonElasticMapReduceReadOnlyAccess` policy, the `AmazonEMRReadOnlyAccessPolicy_v2` policy does not use wildcard characters for the `elasticmapreduce` element\. Instead, the default v2 policy scopes the specific `elasticmapreduce` actions that are allowed\.

**Note**  
You can also use the AWS Management Console link [https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AmazonEMRReadOnlyAccessPolicy_v2](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/AmazonEMRReadOnlyAccessPolicy_v2) to view the policy\.

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Sid": "ElasticMapReduceActions",
 6.       "Action": [
 7.         "elasticmapreduce:DescribeCluster",
 8.         "elasticmapreduce:DescribeEditor",
 9.         "elasticmapreduce:DescribeJobFlows",
10.         "elasticmapreduce:DescribeSecurityConfiguration",
11.         "elasticmapreduce:DescribeStep",
12.         "elasticmapreduce:GetBlockPublicAccessConfiguration",
13.         "elasticmapreduce:GetManagedScalingPolicy",
14.         "elasticmapreduce:ListBootstrapActions",
15.         "elasticmapreduce:ListClusters",
16.         "elasticmapreduce:ListEditors",
17.         "elasticmapreduce:ListInstanceFleets",
18.         "elasticmapreduce:ListInstanceGroups",
19.         "elasticmapreduce:ListInstances",
20.         "elasticmapreduce:ListSecurityConfigurations",
21.         "elasticmapreduce:ListSteps",
22.         "elasticmapreduce:ViewEventsFromAllClustersInConsole"
23.       ],
24.       "Effect": "Allow",
25.       "Resource": "*"
26.     },
27.     {
28.       "Sid": "ViewMetricsInEMRConsole",
29.       "Action": [
30.         "cloudwatch:GetMetricStatistics"
31.       ],
32.       "Effect": "Allow",
33.       "Resource": "*"
34.     }
35.   ]
36. }
```