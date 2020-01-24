# Customize IAM Roles<a name="emr-iam-roles-custom"></a>

You may want to customize the IAM service roles and permissions to limit privileges according to your security requirements\. To customize permissions, we recommend that you create new roles and policies\. Begin with the permissions in the managed policies for the default roles \(for example, `AmazonElasticMapReduceforEC2Role` and `AmazonElasticMapReduceRole`\)\. Then, copy and paste the contents to new policy statements, modify the permissions as appropriate, and attach the modified permissions policies to the roles that you create\. You must have the appropriate IAM permissions to work with roles and policies\. For more information, see [Allow Users and Groups to Create and Modify Roles](emr-iam-roles-create-permissions.md)\.

If you create a custom EMR role for EC2, follow the basic work flow, which automatically creates an instance profile of the same name\. Amazon EC2 allows you to create instance profiles and roles with different names, but Amazon EMR does not support this configuration, and it results in an "invalid instance profile" error when you create the cluster\. 

**Important**  
Inline policies are not automatically updated when service requirements change\. If you create and attach inline policies, be aware that service updates might occur that suddenly cause permissions errors\. For more information, see [Managed Policies and Inline Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/policies_managed-vs-inline.html) in the *IAM User Guide* and [Specify Custom IAM Roles When You Create a Cluster](#emr-iam-roles-launch-jobflow)\.

For more information about working with IAM roles, see the following topics in the *IAM User Guide*:
+  [Creating a Role to Delegate Permissions to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) 
+  [Modifying a Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/modifying-role.html) 
+  [Deleting a Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/deleting-roles.html) 

## Specify Custom IAM Roles When You Create a Cluster<a name="emr-iam-roles-launch-jobflow"></a>

You specify the service role for Amazon EMR and the role for the Amazon EC2 instance profile when you create a cluster\. The user who is creating clusters needs permissions to retrieve and assign roles to Amazon EMR and EC2 instances\. Otherwise, a **User account is not authorized to call EC2** error occurs\. For more information, see [Allow Users and Groups to Create and Modify Roles](emr-iam-roles-create-permissions.md)\.

### Use the Console to Specify Custom Roles<a name="emr-iam-roles-launch-console"></a>

When you create a cluster, you can specify a custom service role for Amazon EMR, a custom role for the EC2 instance profile, and a custom Auto Scaling role using **Advanced options**\. When you use **Quick options**, the default service role and the default role for the EC2 instance profile are specified\. For more information, see [IAM Service Roles Used By Amazon EMR](emr-iam-service-roles.md)\.

**To specify custom IAM roles using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**, **Go to advanced options**\.

1. Choose the cluster settings appropriate for your application until you reach **Security Options**\.

   Under **Permissions**, the **Default** roles for Amazon EMR are selected\.

1. Choose **Custom**\.

1. For each role type, select a role from the list\. Only roles within your account that have the appropriate trust policy for that role type are listed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/custom_role_console.png)

1. Choose other options as appropriate for your cluster and then choose **Create Cluster**\.

### Use the AWS CLI to Specify Custom Roles<a name="emr-iam-roles-launch-cli"></a>

You can specify a service role for EMR and a service role for cluster EC2 instances explicitly using options with the `create-cluster` command from the AWS CLI\. Use the `--service-role` option to specify the service role\. Use the `InstanceProfile` argument of the `--ec2-attributes` option to specify the role for the EC2 instance profile\.

The Auto Scaling role is specified using a separate option, `--auto-scaling-role`\. For more information, see [Using Automatic Scaling in Amazon EMR](emr-automatic-scaling.md)\.

**To specify custom IAM roles using the AWS CLI**
+ The following command specifies the custom service role, *MyCustomServiceRoleForEMR*, and a custom role for the EC2 instance profile, *MyCustomServiceRoleForClusterEC2Instances*, when launching a cluster\. This example uses the default Amazon EMR role\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  aws emr create-cluster --name "Test cluster" --release-label emr-5.29.0 \
  --applications Name=Hive Name=Pig --service-role MyCustomServiceRoleForEMR \
  --ec2-attributes InstanceProfile=MyCustomServiceRoleForClusterEC2Instances,\
  KeyName=myKey --instance-type m5.xlarge --instance-count 3
  ```

You can use these options to specify default roles explicitly rather than using the `--use-default-roles` option\. The `--use-default-roles` option specifies the service role and the role for the EC2 instance profile defined in the `config` file for the AWS CLI\.

The following example demonstrates the contents of a `config` file for the AWS CLI the specifies custom roles for Amazon EMR\. With this configuration file, when the `--use-default-roles` option is specified, the cluster is created using the *MyCustomServiceRoleForEMR* and *MyCustomServiceRoleForClusterEC2Instances*\. By default, the `config` file specifies the default `service_role` as `AmazonElasticMapReduceRole` and the default `instance_profile` as `EMR_EC2_DefaultRole`\.

```
[default]
output = json
region = us-west-1
aws_access_key_id = myAccessKeyID
aws_secret_access_key = mySecretAccessKey
emr =
     service_role = MyCustomServiceRoleForEMR
     instance_profile = MyCustomServiceRoleForClusterEC2Instances
```