# Use Resource\-Based Policies for Amazon EMR Access to AWS Glue Data Catalog<a name="emr-iam-roles-glue"></a>

If you use AWS Glue in conjunction with Hive, Spark, or Presto in Amazon EMR, AWS Glue supports resource\-based policies to control access to Data Catalog resources\. These resources include databases, tables, connections, and user\-defined functions\. For more information, see [AWS Glue Resource Policies](https://docs.aws.amazon.com/glue/latest/dg/glue-resource-policies.html) in the *AWS Glue Developer Guide*\.

When using resource\-based policies to limit access to AWS Glue from within Amazon EMR, the principal that you specify in the permissions policy must be the role ARN associated with the EC2 instance profile that is specified when a cluster is created\. For example, for a resource\-based policy attached to a catalog, you can specify the role ARN for the default service role for cluster EC2 instances, *EMR\_EC2\_DefaultRole* as the `Principal`, using the format shown in the following example:

```
arn:aws:iam::acct-id:role/EMR_EC2_DefaultRole
```

The *acct\-id* can be different from the AWS Glue account ID\. This enables access from EMR clusters in different accounts\. You can specify multiple principals, each from a different account\.