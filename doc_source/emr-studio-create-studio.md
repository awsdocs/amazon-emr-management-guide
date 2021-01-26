# Create an EMR Studio<a name="emr-studio-create-studio"></a>

For Amazon EMR Studio \(preview\), use the `create-studio` AWS CLI command\.

**Prerequisites**

To create an EMR Studio using the AWS CLI, you must have the following items:
+ The AWS Command Line Interface **version 1\.18\.184** or later, or **version 2\.1\.4** or later\. Use credentials for the AWS account \(a member account\) which you have designated for your Studio\. Make sure you use an IAM principal \(user or role\) that has [the required permissions to create a Studio](emr-studio-admin-permissions.md)\.
+ An Amazon Virtual Private Cloud \(VPC\) designated for your Studio\. If you plan to use Amazon EMR on EKS with EMR Studio, choose the same VPC to which your Amazon EKS cluster worker nodes belong\.
+ A maximum of five subnets that belong to your designated VPC to associate with the Studio\. At least one of the subnets must be private to enable Git repository linking and connections to Amazon EMR on EKS managed endpoints\. 
**Note**  
There must be *at least one private subnet in common* between your EMR Studio and the Amazon EKS cluster that you use to register your virtual cluster\. Otherwise, your managed endpoint will not appear as an option in your Studio Workspaces\. You can create an Amazon EKS cluster and associate it with a subnet that belongs to your Studio\. Alternatively, you can create a Studio and specify your EKS cluster's private subnets\.
+ AWS Single Sign\-On enabled\. For more information, see [Enable AWS Single Sign\-On for Amazon EMR Studio](emr-studio-enable-sso.md)\.
+ An IAM service role, IAM user role, IAM session policies, and security groups set up for Amazon EMR Studio\. For more information, see [EMR Studio Security and Access Control](emr-studio-security.md)\.

**To create an EMR Studio using the AWS CLI**

Insert your own values for the following options\. For more information about the `create-studio` command, see [https://docs.aws.amazon.com/cli/latest/reference/emr/create-studio.html](https://docs.aws.amazon.com/cli/latest/reference/emr/create-studio.html)\.
+ **`--name` **– A descriptive name for your EMR Studio to help you identify it when you manage multiple Studios\.
+ **`--auth-mode`** – Specifies whether the Studio uses secure sign\-on \(SSO\) or IAM to authenticate users\. EMR Studio currently supports SSO only\.
+ **`--vpc-id`** – The ID of the VPC that you want to associate with the Studio\. 
+ **`--subnet-ids`** – A list of IDs of the subnets that you want to associate with the Studio\. The subnets must be in the VPC specified by `vpc-id`\.
+ **`--service-role`** – The name of the IAM role that EMR Studio assumes so that it can interoperate with other AWS services\. For more information, see [Create an EMR Studio Service Role](emr-studio-service-role.md)\.
+ **`--user-role`** – The name of the IAM role that users or groups assume when logged in to the Studio\. For more information, see [Create an EMR Studio User Role with Session Policies](emr-studio-user-role.md)\.
+ **`--workspace-security-group-id` **– The ID of your Workspace security group for EMR Studio\. For more information, see [Define Security Groups to Control EMR Studio Network Traffic](emr-studio-security-groups.md)\.
+ **`--engine-security-group-id`** – The ID of your Engine security group for EMR Studio\. For more information, see [Define Security Groups to Control EMR Studio Network Traffic](emr-studio-security-groups.md)\.
+ **\(Optional\) `--default-s3-location`** – The Amazon S3 URI of a default notebook storage location for all Workspaces in the Studio\.

```
aws emr create-studio \
--name <example-studio-name> \
--auth-mode <SSO> \
--vpc-id <example-vpc-id> \
--subnet-ids <subnet-id-1> <subnet-id-2> <subnet-id-3> \
--service-role <example-studio-service-role> \
--user-role <example-studio-user-role> \
--workspace-security-group-id <example-workspace-sg-id> \
--engine-security-group-id <example-engine-sg-id> \
--default-s3-location s3://<name-of-default-bucket>
```

**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

Following is an example of the output you should see after successfully creating the Studio\.

```
{
    StudioId: "es-123XXXXXXXXX",
    Url: "https://es-123XXXXXXXXX.emrstudio-prod.us-east-1.amazonaws.com"
}
```

Copy the **`StudioId`**, which you use to assign users and groups to the Studio\. You should also note the **`Url`**, which is the Studio access URL that your team can use to log in to the Studio\.