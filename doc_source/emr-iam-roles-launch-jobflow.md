# Specify Custom IAM Roles When You Create a Cluster<a name="emr-iam-roles-launch-jobflow"></a>

You specify the service role for Amazon EMR and the role for the Amazon EC2 instance profile when you create a cluster\. The user creating clusters needs permissions to retrieve and assign roles to Amazon EMR and EC2 instances\. Otherwise, a **User account is not authorized to call EC2** error occurs\. For more information, see [Allow Users and Groups to Create and Modify Roles](emr-iam-roles-create-permissions.md)\.

## Use the Console to Specify Custom Roles<a name="emr-iam-roles-launch-console"></a>

You can specify a custom service role for Amazon EMR, a custom role for the EC2 instance profile, and a custom autoscaling role using **Advanced options** when you create a cluster\. When you use **Quick options**, the default service role and the default role for the EC2 instance profile are specified\. For more information, see [Use Default IAM Roles and Managed Policies](emr-iam-roles-defaultroles.md)\.

**To specify custom IAM roles using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**, **Go to advanced options**\.

1. Choose the cluster settings appropriate for your application until you reach **Security Options**\.

   Under **Permissions**, the **Default** roles for Amazon EMR are selected\.

1. Choose **Custom**\.

1. For each role type, select a role from the list\. Only roles within your account that have the appropriate trust policy for that role type are listed\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/custom_role_console.png)

1. Choose other options as appropriate for your cluster and then choose **Create Cluster**\.

## Use the AWS CLI to Specify Custom Roles<a name="emr-iam-roles-launch-cli"></a>

You can specify a service role and a role for the EC2 instance profile using options with the `create-cluster` command from the AWS CLI\. Use the `--service-role` option to specify the service role\. Use the `InstanceProfile` argument of the `--ec2-attributes` option to specify the role for the EC2 instance profile\.

You can use these options to specify default roles explicitly rather than using the `--use-default-roles` option\. The `--use-default-roles` option will specify the service role and the role for the EC2 instance profile defined in the CLI configuration file, which you can update to reflect your custom roles\. For more information, see [Use Default IAM Roles and Managed Policies](emr-iam-roles-defaultroles.md)\.

The autoscaling role is specified using a separate option, `--auto-scaling-role`\. For more information, see [Using Automatic Scaling in Amazon EMR](emr-automatic-scaling.md)\.

**To specify custom IAM roles using the AWS CLI**

+ The following command specifies the custom service role, `MyEMRServiceRole`, and a custom role for the EC2 instance profile, `MyEC2RoleForEMR`, when launching a cluster\. This example uses the default Amazon EMR role\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

  ```
  aws emr create-cluster --name "Test cluster" --release-label emr-5.12.0 \
  --applications Name=Hive Name=Pig --service-role MyEMRServiceRole \
  --ec2-attributes InstanceProfile=MyEC2RoleForEMR,\
  KeyName=myKey --instance-type m3.xlarge --instance-count 3
  ```