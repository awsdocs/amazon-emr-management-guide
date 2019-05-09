# Configure IAM Roles for Amazon EMR Permissions to AWS Services<a name="emr-iam-roles"></a>

Amazon EMR and applications such as Hadoop and Spark need permissions to access other AWS resources and perform actions when they run\. Each cluster in Amazon EMR must have a *service role* and a role for the Amazon EC2 *instance profile*\. The IAM policies attached to these roles provide permissions for the cluster to interoperate with other AWS services on behalf of a user\.

All clusters in all regions require a service role and a role for the EC2 instance profile\. An additional role, the Auto Scaling role, is required if your cluster uses automatic scaling\. For more information, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) and [Using Instance Profiles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html) in the *IAM User Guide*\.

If you are creating a cluster for the first time in an account, roles for Amazon EMR do not yet exist\. You can specify default roles for Amazon EMR to create, or you can create custom roles and specify them when you create a cluster\. For more information, see [Use Default IAM Roles and Managed Policies](emr-iam-roles-defaultroles.md), and [Customize IAM Roles](emr-iam-roles-custom.md)\.

Amazon EMR uses the following roles when interacting with other AWS services\.

## EMR Role<a name="emr-iam-role"></a>

The EMR role defines the allowable actions for Amazon EMR when provisioning resources and performing other service\-level tasks that are not performed in the context of an EC2 instance running within a cluster\. The default role is `EMR_DefaultRole`\.

## EMR Role for EC2<a name="emr-iam-role-for-ec2"></a>

The EMR role for EC2 is used by EC2 instances within the cluster\. In other words, this is the role associated with the EC2 instance profile for cluster instances\. The permissions associated with this role apply to processes that run on cluster instances\. As long as an application process runs on top of the Hadoop ecosystem, the application assumes this role to interact with other AWS services\. The default role is `EMR_EC2_DefaultRole`\.

**Note**  
If you create a custom EMR role for EC2, follow the basic work flow, which automatically creates an instance profile of the same name\. Amazon EC2 allows you to create instance profiles and roles with different names, but Amazon EMR does not support this configuration, and it results in an "invalid instance profile" error when you create the cluster\. 

Clusters typically read and write data to Amazon S3 using EMRFS\. EMRFS uses this role by default\. When you have multiple cluster users and multiple data stores, you may want users to have different permissions to EMRFS data in Amazon S3\. To do this, you can set up a security configuration with IAM roles for EMRFS requests to Amazon S3\. EMRFS can assume different roles with different permissions policies, based on the user or group making the request or the location of EMRFS data in Amazon S3\. For requests that match the criteria that you specify, EMRFS uses the corresponding roles instead of the EMR role for EC2\. If no match is found, EMRFS falls back to the EMR role for EC2\. For more information, see [Configure IAM Roles for EMRFS Requests to Amazon S3](emr-emrfs-iam-roles.md)\.

If you have application code on your cluster that calls AWS services directly, you may need to use the SDK to specify roles\. For more information, see [Use IAM Roles with Applications That Call AWS Services Directly](emr-iam-roles-calling.md)\.

## Automatic Scaling Role<a name="emr-automatic-scaling-role"></a>

The automatic scaling role for EMR performs a similar function as the service role, but allows additional actions for dynamically scaling environments\. The default role is `EMR_AutoScaling_DefaultRole`\.

## Service\-Linked Role<a name="emr-iam-role-slr"></a>

EMR automatically creates a service\-linked role\. If the service for Amazon EMR has lost the ability to clean up Amazon EC2 resources, Amazon EMR can use this role to do so \. For more information, see [Using the Service\-Linked Role for Amazon EMR](using-service-linked-roles.md)\. The EMR role must be allowed to create a service\-linked role for Spot Instances\.

The `AmazonElasticMapReduceFullAccess`, which is the default managed policy for users to have full permissions for Amazon EMR, includes a statement that allows the `iam:PassRole` permissions for all resources\. This statement allows the user to pass any role to other AWS services so that Amazon EMR can interact with those services on behalf of the user\.

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

**Topics**
+ [EMR Role](#emr-iam-role)
+ [EMR Role for EC2](#emr-iam-role-for-ec2)
+ [Automatic Scaling Role](#emr-automatic-scaling-role)
+ [Service\-Linked Role](#emr-iam-role-slr)
+ [Use Default IAM Roles and Managed Policies](emr-iam-roles-defaultroles.md)
+ [Allow Users and Groups to Create and Modify Roles](emr-iam-roles-create-permissions.md)
+ [Customize IAM Roles](emr-iam-roles-custom.md)
+ [Use Resource\-Based Policies for Amazon EMR Access to AWS Glue Data Catalog](emr-iam-roles-glue.md)
+ [Use IAM Roles with Applications That Call AWS Services Directly](emr-iam-roles-calling.md)
+ [Using the Service\-Linked Role for Amazon EMR](using-service-linked-roles.md)