# Create an EMR Studio<a name="emr-studio-create-studio"></a>

Create an EMR Studio for your team using the Amazon EMR console or the AWS CLI\. You can also use the [AWS::EMR::Studio](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-emr-studio.html) resource template to create a Studio using AWS CloudFormation\. 

## Prerequisites<a name="emr-studio-create-studio-prereqs"></a>

Before you create an EMR Studio, you must have the following:
+ AWS Single Sign\-On enabled\. For more information, see [Enable AWS Single Sign\-On for Amazon EMR Studio](emr-studio-enable-sso.md)\.
+ Permission to create and manage an EMR Studio\. For more information, see [Add Required Permissions to Create and Manage an EMR Studio](emr-studio-admin-permissions.md)\.
+ An IAM service role, IAM user role, IAM session policies, and security groups set up for Amazon EMR Studio\. For more information, see [EMR Studio Security and Access Control](emr-studio-security.md)\.
+ A designated Amazon S3 bucket where EMR Studio can back up the Workspaces and notebook files in your Studio\. Your EMR Studio service role must have read and write access to the bucket that you select\.
+ An Amazon Virtual Private Cloud \(VPC\) designated for your Studio\. If you plan to use Amazon EMR on EKS with EMR Studio, choose the same VPC to which your Amazon EKS cluster worker nodes belong\.
+ A maximum of five subnets that belong to your designated VPC to associate with the Studio\. If you plan to use publicly\-hosted Git repositories, you must use private subnets that have access to the internet through Network Address Translation \(NAT\)\. 
**Note**  
There must be *at least one subnet in common* between your EMR Studio and the Amazon EKS cluster that you use to register your virtual cluster\. Otherwise, your managed endpoint will not appear as an option in your Studio Workspaces\. You can create an Amazon EKS cluster and associate it with a subnet that belongs to your Studio\. Alternatively, you can create a Studio and specify your EKS cluster's subnets\.
+ The AWS Command Line Interface **version 1\.18\.184** or later, or **version 2\.1\.4** or later if you want to use the AWS CLI\. Use credentials for the AWS account \(a member account\) which you have designated for your Studio\.

**Important**  
Make sure you disable proxy management tools such as FoxyProxy or SwitchyOmega in your browser before you create a Studio\. Active proxies can cause errors when you choose **Create Studio**, and result in a **Network Failure **error message\.

## Instructions<a name="emr-studio-creat-studio-instructions"></a>

------
#### [ Console ]

**To create an EMR Studio using the EMR console**

1. Tag the following EMR Studio resources with the tag key `"for-use-with-amazon-emr-managed-policies"` and tag value `"true"`\. The EMR Studio service role uses this tag to identify and access your resources\.
   + Your designated VPC
   + Each subnet that you want to associate with your Studio
   + Your EMR Studio security groups \(if using custom security groups\)
   + Any *existing* AWS Secrets Manager secrets that Studio users might use to link Git repositories to a Workspace

   You can apply tags to resources using the **Tags** tab on the relevant resource screen in the AWS Management Console\. Alternatively, you can the AWS Resource Groups Tag Editor to apply the required tag to all of your EMR Studio resources at once\. For more information, see [Tag Editor](https://docs.aws.amazon.com/ARG/latest/userguide/tag-editor.html) in the *AWS Resource Groups* *User Guide\.*

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/home](https://console.aws.amazon.com/elasticmapreduce/home)

1. Choose **EMR Studio** from the left navigation\.

1. Choose **Create Studio** to open the **Create a Studio** page\.

1. Enter a **Studio name** and an optional **Description**\.

1. \(Optional\) Choose **Add new tag** to add one or more a key\-value tags to the Studio\. Tags help you manage, identify, organize, and filter resources\. For more information, see [Tagging AWS resources](https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html)\.

1. Under **Define roles and S3 bucket**, choose a **Service Role** and a **User Role**\. For more information, see [Create an EMR Studio Service Role](emr-studio-service-role.md) and [Create an EMR Studio User Role with Session Policies](emr-studio-user-role.md)\.

1. Under **S3 Bucket**, choose **Browse S3** to select the bucket that you designated for backing up Workspaces and notebook files\.
**Note**  
Your EMR Studio service role must have read and write access to the bucket that you select\.

1. Under **Networking and security**, choose an Amazon Virtual Private Cloud \(**VPC**\) for your Studio from the dropdown list\.

1. Under **Subnets**, select a maximum of five subnets from your VPC to associate with the Studio\. You have the option to add more subnets after you create the Studio\.

1. For **Security and access**, choose either the default security groups or custom security groups\. For more information, see [Define Security Groups to Control EMR Studio Network Traffic](emr-studio-security-groups.md)\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-create-studio.html)

1. Choose **Create Studio** to finish and navigate to the **Studios** page\. You should see your new Studio in the list with details such as **Studio name**, **Create date**, and **Studio access URL**\.

After you create a Studio, follow the instructions in [Assign a User or Group to Your EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.

------
#### [ CLI ]

**Before you create an EMR Studio using the AWS CLI**

Tag the following EMR Studio resources with the tag key `"for-use-with-amazon-emr-managed-policies"` and tag value `"true"`\. The EMR Studio service role uses this tag to identify and access your resources\.
+ Your designated VPC
+ Each subnet that you want to associate with your Studio
+ Your EMR Studio security groups
+ Any *existing* AWS Secrets Manager secrets that Studio users might use to link Git repositories to a Workspace

You can apply tags to resources using the **Tags** tab on the relevant resource screen in the AWS Management Console\. Alternatively, you can the AWS Resource Groups Tag Editor to apply the required tag to all of your EMR Studio resources at once\. For more information, see [Tag Editor](https://docs.aws.amazon.com/ARG/latest/userguide/tag-editor.html) in the *AWS Resource Groups* *User Guide\.*

**To create an EMR Studio using the AWS CLI**

Use the `create-studio` AWS CLI command\. Insert your own values for the following options\. For more information about the `create-studio` command, see [https://docs.aws.amazon.com/cli/latest/reference/emr/create-studio.html](https://docs.aws.amazon.com/cli/latest/reference/emr/create-studio.html)\.

```
aws emr create-studio \
--name <example-studio-name> \
--auth-mode <SSO> \
--vpc-id <example-vpc-id> \
--subnet-ids <subnet-id-1> <subnet-id-2>... <subnet-id-5>  \
--service-role <example-studio-service-role-name> \
--user-role <example-studio-user-role-name> \
--workspace-security-group-id <example-workspace-sg-id> \
--engine-security-group-id <example-engine-sg-id> \
--default-s3-location <example-s3-location>
```

**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

The following is an example of the output you should see after you successfully create the Studio\.

```
{
    StudioId: "es-123XXXXXXXXX",
    Url: "https://es-123XXXXXXXXX.emrstudio-prod.us-east-1.amazonaws.com"
}
```

Copy the **`StudioId`**, which you use to [Assign a User or Group to Your EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\. You should also note the **`Url`**, which is the Studio access URL that your team can use to log in to the Studio\.

------