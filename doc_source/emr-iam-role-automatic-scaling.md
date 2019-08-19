# Service Role for Automatic Scaling in EMR \(Auto Scaling Role\)<a name="emr-iam-role-automatic-scaling"></a>

The auto scaling role for EMR performs a similar function as the service role, but allows additional actions for dynamically scaling environments\.
+ The default role is `EMR_AutoScaling_DefaultRole`\.
+ The default managed policy attached to `EMR_AutoScaling_DefaultRole` is `AmazonElasticMapReduceforAutoScalingRole`\.

The contents of version 1 of `AmazonElasticMapReduceforAutoScalingRole` are shown below\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "cloudwatch:DescribeAlarms",
                "elasticmapreduce:ListInstanceGroups",
                "elasticmapreduce:ModifyInstanceGroups"
            ],
            "Effect": "Allow",
            "Resource": "*"
        }
    ]
}
```