# Amazon EMR managed policies<a name="emr-managed-iam-policies"></a>

The easiest way to grant full access or read\-only access to required Amazon EMR actions is to use the IAM managed policies for Amazon EMR\. Managed policies offer the benefit of updating automatically if permission requirements change\. If you use inline policies, service changes may occur that cause permission errors to appear\. 

Amazon EMR will be deprecating existing managed policies in favor of new managed policies \(v2 policies\)\. The new managed policies have been scoped\-down to align with AWS best practices\. After the existing managed policies are deprecated, you will not be able to attach these policies to any new IAM roles or users\. Existing roles and users that use deprecated policies can continue to use them\. The v2 managed policies allow only specified Amazon EMR actions and require cluster resources that are tagged with an EMR\-specific key\. We recommend that you carefully review the documentation before using the new v2 policies\.

The v1 policies will be marked deprecated with a warning icon next to them in the **Policies** list in the IAM console\. The deprecated policies will have the following characteristics:
+ They will continue to work for all currently attached users, groups, and roles\. Nothing breaks\.
+ They cannot be attached to new users, groups, or roles\. If you detach one of the policies from a current entity, you cannot reattach it\.
+ After you detach a v1 policy from all current entities, the policy will no longer be visible and can no longer be used\.

The following table summarizes the changes between current policies and v2 policies\.


**EMR managed policy changes**  

| Policy type | Policy names | Policy purpose | Changes to v2 policy | 
| --- | --- | --- | --- | 
|  Default IAM managed policy for full EMR access by attached user, role, or group  |  Deprecated policy name: [`AmazonElasticMapReduceFullAccess`](emr-managed-policy-fullaccess.md)  v2 \(scoped\) policy name: [`AmazonEMRFullAccessPolicy_v2`](emr-managed-policy-fullaccess-v2.md)  |  Allows users full permissions for EMR actions\. Includes iam:PassRole permissions for resources\.  |  Policy adds a prerequisite that users must add user tags to resources before they can use this policy\. See [Tagging resources to use managed policies](#manually-tagged-resources)\. iam:PassRole action requires iam:PassedToService condition set to specified service\. Access to Amazon EC2, Amazon S3, and other services is not allowed by default\. See [IAM Managed Policy for Full Access \(v2 Managed Default Policy\)](emr-managed-policy-fullaccess-v2.md)\.  | 
|  IAM managed policy for read\-only access by attached user, role, or group  |  Deprecated policy name: [`AmazonElasticMapReduceReadOnlyAccess`](emr-managed-policy-readonly.md)  v2 \(scoped\) policy name: [`AmazonEMRReadOnlyAccessPolicy_v2`](emr-managed-policy-readonly-v2.md)  |  Allows users read\-only permissions for Amazon EMR actions\.  |  Permissions allow only specified elasticmapreduce read\-only actions\. Access to Amazon S3 is access not allowed by default\. See [IAM Managed Policy for Read\-Only Access \(v2 Managed Default Policy\)](emr-managed-policy-readonly-v2.md)\.  | 
|  Default EMR service role and attached managed policy  |   Role name: **EMR\_DefaultRole** Deprecated policy name: **AmazonElasticMapReduceRole** \(EMR Service Role\)  V2 \(scoped\-down\) policy name: [`AmazonEMRServicePolicy_v2`](emr-iam-role.md)  |  Allows Amazon EMR to call other AWS services on your behalf when provisioning resources and performing service\-level actions\. This role is required for all clusters\.  |  The v2 service role and v2 default policy replace the deprecated role and policy\. The policy adds a prerequisite that users must add user tags to resources before they can use this policy\. See [Tagging resources to use managed policies](#manually-tagged-resources)\. See [Service role for Amazon EMR \(EMR role\)](emr-iam-role.md)\.  | 
|  Service role for cluster EC2 instances \(EC2 instance profile\)  |  Deprecated role name: **EMR\_EC2\_DefaultRole** \(instance profile\) Deprecated policy name: **AmazonElasticMapReduceforEC2Role**  |  Allows applications running on an EMR cluster to access other AWS resources, such as Amazon S3\. For example, if you run Apache Spark jobs that process data from Amazon S3, the policy needs to allow access to such resources\.  |  Both default role and default policy are on the path to deprecation\. There is no replacement AWS default managed role or policy\. You need to provide a resource\-based or identity\-based policy\. This means that, by default, applications running on an EMR cluster do not have access to Amazon S3 or other resource unless you manually add these to the policy\. See [Default role and managed policy](emr-iam-role-for-ec2.md#emr-ec2-role-default)\.  | 
|  Other EC2 service role policies  |  Current policy names: **AmazonElasticMapReduceforAutoScalingRole, AmazonElasticMapReduceEditorsRole, AmazonEMRCleanupPolicy**  |  Provides permissions that EMR needs to access other AWS resources and perform actions if using auto scaling, notebooks, or to clean up EC2 resources\.  |  No changes for v2\.  | 

## Securing iam:PassRole<a name="securing-iampassrole"></a>

The Amazon EMR full permissions default managed policy \(`AmazonEMRFullAccessPolicy_v2`\) and Amazon EMR service policy \(`AmazonEMRServicePolicy_v2`\) are available to replace the soon\-to\-be\-deprecated policy\. The v2 policies incorporate new iam:PassRole security configurations, including the following:
+ `iam:PassRole` permissions only for specific default Amazon EMR roles\.
+ `iam:PassedToService` conditions that allow you to use the policy with only specified AWS services, such as `elasticmapreduce.amazonaws.com` and `ec2.amazonaws.com`\.

You can view the JSON version of the [AmazonEMRFullAccessPolicy\_v2](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/service-role/AmazonEMRFullAccessPolicy_v2) and [AmazonEMRServicePolicy\_v2](https://console.aws.amazon.com/iam/home#policies/arn:aws:iam::aws:policy/service-role/AmazonEMRServicePolicy_v2) policies in the IAM console\.

We recommend that you create new clusters using v2 managed policies\.

To create custom policies, we recommend that you begin with the managed policies and edit them according to your requirements\.

For information about how to attach policies to IAM users \(principals\), see [Working with managed policies using the AWS Management Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-using.html#policies_using-managed-console) in the *IAM User Guide*\.

## Tagging resources to use managed policies<a name="manually-tagged-resources"></a>

AmazonEMRServicePolicy\_v2 and AmazonEMRFullAccessPolicy\_v2 depend on scoped\-down access to resources that Amazon EMR provisions or uses\. When you use either of these two policies, you need to pass the user tag `for-use-with-amazon-emr-managed-policies = true` when provisioning the cluster\. Amazon EMR will then automatically propagate that tag\. Additionally, you need to manually add a user tag to resources listed in the following section\.

Before you can use managed policies, you must do the following:
+ Pass the user tag `for-use-with-amazon-emr-managed-policies = true` when provisioning a cluster using the CLI, SDK, or another method\.

  When you pass the tag, Amazon EMR propagates the tag to private subnet ENI, EC2 instance, and EBS volumes that it creates\. Amazon EMR also automatically tags security groups that it creates\. However, if you want Amazon EMR to launch with a certain security group, you must tag it\. 
+ Tag certain resources manually\.

  You must tag EC2 subnets, EC2 security groups \(if not created by Amazon EMR\), and VPCs \(if you want EMR to create security groups\)\.

**Propagated user\-specified tagging**  
Amazon EMR tags resources that it creates using the Amazon EMR tags that you specify when creating a cluster\. Amazon EMR applies tags to the resources it creates during the lifetime of the cluster\.

Amazon EMR propagates user tags for the following resources:
+ Private Subnet ENI \(service access elastic network interfaces\)
+ EC2 Instances
+ EBS Volumes
+ EC2 Launch Template

**Automatically\-tagged security groups**  
Amazon EMR tags EC2 security groups that it creates with the tag that is required for v2 managed policies for Amazon EMR, `for-use-with-amazon-emr-managed-policies`, regardless of which tags you specify in the create cluster command\. For a security group that was created before the introduction of v2 managed policies, Amazon EMR does not automatically tag the security group\. If you want to use v2 managed policies with the default security groups that already exist in the account, you need to tag the security groups manually with `for-use-with-amazon-emr-managed-policies = true`\.

**Manually\-tagged cluster resources**  
You must manually tag some cluster resources so that they can be accessed by Amazon EMR default roles\.
+ You must manually tag EC2 security groups and EC2 subnets with the Amazon EMR managed policy tag `for-use-with-amazon-emr-managed-policies`\.
+ You must manually tag a VPC if you want Amazon EMR to create default security groups\. EMR will try to create a security group with the specific tag if the default security group doesn't already exist\.

Amazon EMR automatically tags the following resources:
+ EMR\-created EC2 Security Groups

You must manually tag the following resources:
+ EC2 Subnet
+ EC2 Security Groups

Optionally, you can manually tag the following resources:
+ VPC \- only when you want Amazon EMR to create security groups

## AWS managed policies for Amazon EMR<a name="security-iam-awsmanpol"></a>







To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the **ReadOnlyAccess** AWS managed policy provides read\-only access to all AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.













### Amazon EMR updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>



View details about updates to AWS managed policies for Amazon EMR since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the Amazon EMR Document history page\.




| Change | Description | Date | 
| --- | --- | --- | 
|  [`AmazonEMRFullAccessPolicy_v2`](emr-managed-policy-fullaccess-v2.md) – Update to an existing policy  |  Removed invalid action s3:ListBuckets and replaced with s3:ListAllMyBuckets action\. Updated service\-linked role \(SLR\) creation to be explicitly scoped\-down to the only SLR that Amazon EMR has with explicit Service Principles\. The SLRs that can be created are exactly the same as before this change\.  | March 23, 2021 | 
|  [`AmazonEMRFullAccessPolicy_v2`](emr-managed-policy-fullaccess-v2.md) – New policy  |  Amazon EMR added new permissions to scope access to resources and to add a prerequisite that users must add predefined user tag to resources before they can use Amazon EMR managed policies\. iam:PassRole action requires iam:PassedToService condition set to specified service\. Access to Amazon EC2, Amazon S3, and other services is not allowed by default\.   | March 11, 2021 | 
| [`AmazonEMRServicePolicy_v2`](emr-iam-role.md) – New policy |  Adds a prerequisite that users must add user tags to resources before they can use this policy\.  | March 11, 2021 | 
| [`AmazonEMRReadOnlyAccessPolicy_v2`](emr-managed-policy-readonly-v2.md) – New policy |  Permissions allow only specified elasticmapreduce read\-only actions\. Access to Amazon S3 is access not allowed by default\.  | March 11, 2021 | 
|  Amazon EMR started tracking changes  |  Amazon EMR started tracking changes for its AWS managed policies\.  | March 11, 2021 | 