# Configure IAM service roles for Amazon EMR permissions to AWS services and resources<a name="emr-iam-roles"></a>

Amazon EMR and applications such as Hadoop and Spark need permissions to access other AWS resources and perform actions when they run\. Each cluster in Amazon EMR must have a *service role* and a role for the Amazon EC2 *instance profile*\. For more information, see [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) and [Using instance profiles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html) in the *IAM User Guide*\. The IAM policies attached to these roles provide permissions for the cluster to interoperate with other AWS services on behalf of a user\.

An additional role, the Auto Scaling role, is required if your cluster uses automatic scaling in Amazon EMR\. The AWS service role for EMR Notebooks is required if you use EMR Notebooks\.

Amazon EMR provides default roles and default managed policies that determine permissions for each role\. Managed policies are created and maintained by AWS, so they are updated automatically if service requirements change\. See [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies.html) in the *IAM User Guide*\.

If you are creating a cluster or notebook for the first time in an account, roles for Amazon EMR do not yet exist\. After you create them, you can view the roles, the policies attached to them, and the permissions allowed or denied by the policies in the IAM console \([https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\)\. You can specify default roles for Amazon EMR to create and use, you can create your own roles and specify them individually when you create a cluster to customize permissions, and you can specify default roles to be used when you create a cluster using the AWS CLI\. For more information, see [Customize IAM roles](emr-iam-roles-custom.md)\.

## Modifying identity\-based policies for permissions to pass service roles for Amazon EMR<a name="emr-iam-roles-passrole"></a>

The Amazon EMR full permissions default managed policy \(`AmazonEMRFullAccessPolicy_v2`\) and Amazon EMR service policy \(`AmazonEMRServicePolicy_v2`\) are available to replace the soon\-to\-be\-deprecated policy\. The v2 policies incorporate new iam:PassRole security configurations, including the following:
+ `iam:PassRole` permissions only for specific default Amazon EMR roles\.
+ `iam:PassedToService` conditions that allow you to use the policy with only specified AWS services, such as `elasticmapreduce.amazonaws.com` and `ec2.amazonaws.com`\.

You can view the JSON version of the [AmazonEMRFullAccessPolicy\_v2](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/service-role/AmazonEMRFullAccessPolicy_v2) and [AmazonEMRServicePolicy\_v2](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/service-role/AmazonEMRServicePolicy_v2) policies in the IAM console\.

We recommend that you create new clusters using v2 managed policies\.

## Service role summary<a name="emr-iam-roles-summary"></a>

The following table lists the IAM service roles associated with Amazon EMR for quick reference\.


| Function | Default role | Description | Default managed policy | 
| --- | --- | --- | --- | 
|  [Service role for Amazon EMR \(EMR role\)](emr-iam-role.md)  |  `EMR_DefaultRole`  |  Allows Amazon EMR to call other AWS services on your behalf when provisioning resources and performing service\-level actions\. This role is required for all clusters\.  |  `AmazonElasticMapReduceRole` Requesting Spot Instances requires a service\-linked role\. If this role doesn't exist, the EMR service role must have permission to create it or a permission error occurs\. If you plan to request Spot Instances, you must update this policy to include a statement that allows the creation of this service\-linked role\. For more information, see [Service role for Amazon EMR \(EMR role\)](emr-iam-role.md) and [Service\-linked role for Spot Instance requests](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-requests.html#service-linked-roles-spot-instance-requests) in the *Amazon EC2 User Guide for Linux Instances*\.   | 
| [Service role for cluster EC2 instances \(EC2 instance profile\)](emr-iam-role-for-ec2.md) |  `EMR_EC2_DefaultRole`  |  Application processes that run on top of the Hadoop ecosystem on cluster instances use this role when they call other AWS services\. For accessing data in Amazon S3 using EMRFS, you can specify different roles to be assumed based on the location of data in Amazon S3\. For example, multiple teams can access a single Amazon S3 data "storage account\." For more information, see [Configure IAM roles for EMRFS requests to Amazon S3](emr-emrfs-iam-roles.md)\. This role is required for all clusters\.  |  `AmazonElasticMapReduceforEC2Role`\. For more information, see [Service role for cluster EC2 instances \(EC2 instance profile\)](emr-iam-role-for-ec2.md)\.  | 
| [Service role for automatic scaling in EMR \(Auto Scaling role\)](emr-iam-role-automatic-scaling.md) |  `EMR_AutoScaling_DefaultRole`  |  Allows additional actions for dynamically scaling environments\. Required only for clusters that use automatic scaling in Amazon EMR\. For more information, see [Using automatic scaling with a custom policy for instance groups ](emr-automatic-scaling.md)\.  |  `AmazonElasticMapReduceforAutoScalingRole`\. For more information, see [Service role for automatic scaling in EMR \(Auto Scaling role\)](emr-iam-role-automatic-scaling.md)\.  | 
| [Service role for EMR Notebooks](emr-managed-notebooks-service-role.md) |  `EMR_Notebooks_DefaultRole`  |  Provides permissions that an EMR notebook needs to access other AWS resources and perform actions\. Required only if EMR Notebooks is used\.  |  `AmazonElasticMapReduceEditorsRole`\. For more information, see [Service role for EMR Notebooks](emr-managed-notebooks-service-role.md)\. `S3FullAccessPolicy` is also attached by default\. Following is the contents of this policy\. <pre>{<br />    "Version": "2012-10-17",<br />    "Statement": [<br />        {<br />            "Effect": "Allow",<br />            "Action": "s3:*",<br />            "Resource": "*"<br />        }<br />    ]<br />}<br /></pre>  | 
| [Service\-Linked Role](using-service-linked-roles.md) | `AWSServiceRoleForEMRCleanup` | Amazon EMR automatically creates a service\-linked role\. If the service for Amazon EMR has lost the ability to clean up Amazon EC2 resources, Amazon EMR can use this role to clean up\. If a cluster uses Spot Instances, the permissions policy attached to the [Service role for Amazon EMR \(EMR role\)](emr-iam-role.md) must allow the creation of a service\-linked role\. For more information, see [Service\-linked role permissions for Amazon EMR](using-service-linked-roles.md#service-linked-role-permissions)\. | `AmazonEMRCleanupPolicy` | 

**Topics**
+ [Modifying identity\-based policies for permissions to pass service roles for Amazon EMR](#emr-iam-roles-passrole)
+ [Service role summary](#emr-iam-roles-summary)
+ [IAM service roles used by Amazon EMR](emr-iam-service-roles.md)
+ [Customize IAM roles](emr-iam-roles-custom.md)
+ [Configure IAM roles for EMRFS requests to Amazon S3](emr-emrfs-iam-roles.md)
+ [Use resource\-based policies for Amazon EMR access to AWS Glue Data Catalog](emr-iam-roles-glue.md)
+ [Use IAM roles with applications that call AWS services directly](emr-iam-roles-calling.md)
+ [Allow users and groups to create and modify roles](emr-iam-roles-create-permissions.md)