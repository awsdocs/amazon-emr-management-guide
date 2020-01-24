# Configure IAM Service Roles for Amazon EMR Permissions to AWS Services and Resources<a name="emr-iam-roles"></a>

Amazon EMR and applications such as Hadoop and Spark need permissions to access other AWS resources and perform actions when they run\. Each cluster in Amazon EMR must have a *service role* and a role for the Amazon EC2 *instance profile*\. The IAM policies attached to these roles provide permissions for the cluster to interoperate with other AWS services on behalf of a user\.

An additional role, the Auto Scaling role, is required if your cluster uses automatic scaling in Amazon EMR\. The AWS service role for EMR Notebooks is required if you use EMR Notebooks\. For more information, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) and [Using Instance Profiles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html) in the *IAM User Guide*\.

Amazon EMR provides default roles and default managed policies for each role that determine permissions\. Managed policies are created and maintained by AWS, so they are updated automatically if service requirements change\. 

If you are creating a cluster or notebook for the first time in an account, roles for Amazon EMR do not yet exist\. After you create them, you can view the roles, the policies attached to them, and the permissions allowed or denied by the policies in the IAM console \([https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\)\. You can specify default roles for Amazon EMR to create and use, you can create your own roles and specify them individually when you create a cluster to customize permissions, and you can specify default roles to be used when you create a cluster using the AWS CLI\. For more information, see [Customize IAM Roles](emr-iam-roles-custom.md)\.

## Modifying Identity\-Based Policies for Permissions to Pass Service Roles for Amazon EMR<a name="emr-iam-roles-passrole"></a>

Cluster users require permissions for applications to pass the service roles for Amazon EMR on their behalf\. The `AmazonElasticMapReduceFullAccess` permissions policy, which is the default managed policy for users to have full permissions for Amazon EMR, includes a statement that allows the `iam:PassRole` permissions for all resources\. This statement allows the user to pass any role to other AWS services so that Amazon EMR can interact with those services on behalf of the user\.

To implement a more restrictive policy, attach an inline policy to appropriate users or groups that allows `iam:PassRole` only for roles specific to Amazon EMR\. The following example demonstrates a statement that allows `iam:PassRole` permissions only for the default Amazon EMR roles: `EMR_DefaultRole`, `EMR_EC2_DefaultRole`, and `EMR_AutoScalingDefaultRole`\. If you use custom roles, replace the default role names with your custom role names\.

```
{
    "Action": "iam:PassRole",
    "Effect": "Allow",
    "Resource": [
        "arn:aws:iam::*:role/EMR_DefaultRole",
        "arn:aws:iam::*:role/EMR_EC2_DefaultRole",
        "arn:aws:iam::*:role/EMR_AutoScaling_DefaultRole",
        "arn:aws:iam::*:role/EMR_Notebooks_DefaultRole"
    ]
}
```

## Service Role Summary<a name="emr-iam-roles-summary"></a>

The following table lists the IAM service roles associated with Amazon EMR for quick reference\.


| Function | Default Role | Description | Default Managed Policy | 
| --- | --- | --- | --- | 
|  [Service Role for Amazon EMR \(EMR Role\)](emr-iam-role.md)  |  `EMR_DefaultRole`  |  Allows Amazon EMR to call other AWS services on your behalf when provisioning resources and performing service\-level actions\. This role is required for all clusters\.  |  `AmazonElasticMapReduceRole` Requesting Spot Instances requires a service\-linked role\. If this role doesn't exist, the EMR role must have permissions to create it or a permission error occurs\. The managed policy includes a statement to allow this action\. If you customize this role or policy, be sure to include a statement that allows the creation of this service\-linked role\. For more information, see [Service Role for Amazon EMR \(EMR Role\)](emr-iam-role.md) and [Service\-Linked Role for Spot Instance Requests](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-requests.html#service-linked-roles-spot-instance-requests) in the *Amazon EC2 User Guide for Linux Instances*\.   | 
| [Service Role for Cluster EC2 Instances \(EC2 Instance Profile\)](emr-iam-role-for-ec2.md) |  `EMR_EC2_DefaultRole`  |  Application processes that run on top of the Hadoop ecosystem on cluster instances use this role when they call other AWS services\. For accessing data in Amazon S3 using EMRFS, you can specify different roles to be assumed based on the user or group making the request, or on the location of data in Amazon S3\. For more information, see [Configure IAM Roles for EMRFS Requests to Amazon S3](emr-emrfs-iam-roles.md)\. This role is required for all clusters\.  |  `AmazonElasticMapReduceforEC2Role`\. For more information, see [Service Role for Cluster EC2 Instances \(EC2 Instance Profile\)](emr-iam-role-for-ec2.md)\.  | 
| [Service Role for Automatic Scaling in EMR \(Auto Scaling Role\)](emr-iam-role-automatic-scaling.md) |  `EMR_AutoScaling_DefaultRole`  |  Allows additional actions for dynamically scaling environments\. Required only for clusters that use automatic scaling in Amazon EMR\. For more information, see [Using Automatic Scaling in Amazon EMR](emr-automatic-scaling.md)\.  |  `AmazonElasticMapReduceforAutoScalingRole`\. For more information, see [Service Role for Automatic Scaling in EMR \(Auto Scaling Role\)](emr-iam-role-automatic-scaling.md)\.  | 
| [Service Role for EMR Notebooks](emr-managed-notebooks-service-role.md) |  `EMR_Notebooks_DefaultRole`  |  Provides permissions that an EMR notebook needs to access other AWS resources and perform actions\. Required only if EMR Notebooks is used\.  |  `AmazonElasticMapReduceEditorsRole`\. For more information, see [Service Role for EMR Notebooks](emr-managed-notebooks-service-role.md)\. `S3FullAccessPolicy` is also attached by default\. The contents of this policy are shown below\. <pre>{<br />    "Version": "2012-10-17",<br />    "Statement": [<br />        {<br />            "Effect": "Allow",<br />            "Action": "s3:*",<br />            "Resource": "*"<br />        }<br />    ]<br />}<br /></pre>  | 
| [Service\-Linked Role](using-service-linked-roles.md) | `AWSServiceRoleForEMRCleanup` | Amazon EMR automatically creates a service\-linked role\. If the service for Amazon EMR has lost the ability to clean up Amazon EC2 resources, Amazon EMR can use this role to clean up\. If a cluster uses Spot Instances, the permissions policy attached to the [Service Role for Amazon EMR \(EMR Role\)](emr-iam-role.md) must allow the creation of a service\-linked role\. For more information, see [Service\-Linked Role Permissions for Amazon EMR](using-service-linked-roles.md#service-linked-role-permissions)\. | `AmazonEMRCleanupPolicy` | 

**Topics**
+ [Modifying Identity\-Based Policies for Permissions to Pass Service Roles for Amazon EMR](#emr-iam-roles-passrole)
+ [Service Role Summary](#emr-iam-roles-summary)
+ [IAM Service Roles Used By Amazon EMR](emr-iam-service-roles.md)
+ [Customize IAM Roles](emr-iam-roles-custom.md)
+ [Configure IAM Roles for EMRFS Requests to Amazon S3](emr-emrfs-iam-roles.md)
+ [Use Resource\-Based Policies for Amazon EMR Access to AWS Glue Data Catalog](emr-iam-roles-glue.md)
+ [Use IAM Roles with Applications That Call AWS Services Directly](emr-iam-roles-calling.md)
+ [Allow Users and Groups to Create and Modify Roles](emr-iam-roles-create-permissions.md)