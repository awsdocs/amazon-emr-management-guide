# Configure IAM Roles for Amazon EMR Permissions to AWS Services<a name="emr-iam-roles"></a>

Amazon EMR and applications such as Hadoop and Spark need permissions to access other AWS resources and perform actions when they run\. Each cluster in Amazon EMR must have a *service role* and a role for the Amazon EC2 *instance profile*\. The IAM policies attached to these roles provide permissions for the cluster to interoperate with other AWS services on behalf of a user\.

All clusters in all regions require a service role and a role for the EC2 instance profile\. An additional role, the Auto Scaling role, is required if your cluster uses automatic scaling\. For more information, see [IAM Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) and [Using Instance Profiles](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html) in the *IAM User Guide*\.

If you are creating a cluster for the first time in an account, roles for Amazon EMR do not yet exist\. You can specify default roles for Amazon EMR to create, or you can create custom roles and specify them when you create a cluster\. For more information, see [Use Default IAM Roles and Managed Policies](emr-iam-roles-defaultroles.md), and [Customize IAM Roles](emr-iam-roles-custom.md)\.

**Summary of Amazon EMR Roles and Their Purposes**

+ The **service role **defines the allowable actions for Amazon EMR when provisioning resources and performing other service\-level tasks that are not performed in the context of a particular EC2 instance\.

+ The **Amazon EC2 instance profile role** is assumed by EC2 instances within the cluster\. The permissions associated with this role apply to processes that run on cluster instances\. As long as an application process runs on top of the Hadoop ecosystem, the application assumes the role\. If you have application code that calls AWS services directly, you may need to use the SDK to specify roles\. For more information, see [Use IAM Roles with Applications That Call AWS Services Directly](emr-iam-roles-calling.md)\.

  Clusters typically read and write data to Amazon S3 using EMRFS\. When you have multiple cluster users and multiple data stores, you may want users to have different permissions to EMRFS data in Amazon S3\. Use *EMRFS authorization* to do this, which allows EMRFS to assume a different, customized role based on the user or group making the request or the location of EMRFS data in Amazon S3\. For more information, see [Configure EMRFS Authorization for Data in Amazon S3](emr-emrfs-authz.md)\.

+ The **Auto Scaling role **performs a similar function as the service role, but allows additional actions for dynamically scaling environments\.

+ A **service\-linked role** is created automatically by Amazon EMR and allows Amazon EMR to clean up Amazon EC2 resources if the service for Amazon EMR has lost that ability\. For more information, see [Using the Service\-Linked Role for Amazon EMR](using-service-linked-roles.md)\. Amazon EMR must also be allowed to create a service\-linked role for Spot Instances\.

The `AmazonElasticMapReduceFullAccess`, which is the default managed policy for users to have full permissions for Amazon EMR, includes a statement that allows the `iam:PassRole` permission for all resources\. This statement allows the user to pass any role to other AWS services so that Amazon EMR can interact with those services on behalf of the user\.

To implement a more restrictive policy, attach an inline policy to appropriate users or groups that allows `iam:PassRole` only for roles specific to Amazon EMR\. The following example demonstrates a statement that allows `iam:PassRole` permissions only for the default Amazon EMR roles: `EMR_DefaultRole`, `EMR_EC2_DefaultRole`, and `EMR_AutoScalingDefaultRole`\. If you use custom roles, replace the default role names with your custom role names\.

```
{
    "Action": "iam:PassRole",
    "Effect": "Allow",
    "Resource": [
        "arn:aws:iam::*:role/EMR_DefaultRole",
        "arn:aws:iam::*:role/EMR_EC2_DefaultRole",
        "arn:aws:iam::*:role/EMR_AutoScaling_DefaultRole"
    ]
}
```


+ [Use Default IAM Roles and Managed Policies](emr-iam-roles-defaultroles.md)
+ [Allow Users and Groups to Create and Modify Roles](emr-iam-roles-create-permissions.md)
+ [Customize IAM Roles](emr-iam-roles-custom.md)
+ [Specify Custom IAM Roles When You Create a Cluster](emr-iam-roles-launch-jobflow.md)
+ [Use IAM Roles with Applications That Call AWS Services Directly](emr-iam-roles-calling.md)
+ [Using the Service\-Linked Role for Amazon EMR](using-service-linked-roles.md)