# Set Up Cross\-Account Access<a name="emr-lf-cross-account-access"></a>

After you launch an Amazon EMR cluster with Lake Formation integration, you can set up cross\-account access\. AWS Lake Formation cross\-account access lets users query and join tables across multiple AWS accounts\. 

When you enable cross\-account access, users from one account have granular access to the Glue Data Catalog metadata and its underlying data that belongs to another account\. For more information about cross\-account access, see the [https://docs.aws.amazon.com/lake-formation/latest/dg/crosss-account-how-works.html](https://docs.aws.amazon.com/lake-formation/latest/dg/crosss-account-how-works.html)\. 

## Overview<a name="emr-lf-cross-account-access-overview"></a>

To set up cross\-account access, you’ll complete tasks while logged in to the following AWS accounts:
+ **Account A** – An AWS account with the Lake Formation data you want to share with an external account\.
+ **Account B** – The external AWS account to which you want to grant Lake Formation access\. This should be an account where you have launched an EMR cluster with Lake Formation\.

First, you set up a data resource in Account A and grant access to the resource for Account B\. Then, you log in to Account B, accept the invitation from Account A, and create a resource link to give the resource a new name in Account B\. Finally, you add permissions to your Lake Formation IAM role from Account B so that users who are logged in to your cluster can make queries on the linked resource in Account A\.

## Prerequisites<a name="emr-lf-cross-account-access-prereqs"></a>

To prepare your Data Catalog for cross\-account access, follow the steps in [Cross\-Account Access Prerequisites](https://docs.aws.amazon.com/lake-formation/latest/dg/cross-account-prereqs.html) before you start\. You should also read through [Cross\-Account Best Practices and Limitations](https://docs.aws.amazon.com/lake-formation/latest/dg/cross-account-notes.html) to ensure the cross\-account setup meets your data access requirements\.

## Set Up Cross\-Account Access<a name="emr-lf-cross-account-access-steps"></a>

**To grant access to resources from Account A to Account B**

1. Log in to the AWS Management Console with **Account A** and open the AWS Lake Formation console\.

1. Follow the instructions in [Requirements for Roles Used to Register Locations](https://docs.aws.amazon.com/lake-formation/latest/dg/registration-role.html) to create a *user\-defined* service role for your data lake\. 

1. Register a storage location in Amazon S3 with the *user\-defined role* you created in the previous step using the instructions in [Registering an Amazon S3 Location](https://docs.aws.amazon.com/lake-formation/latest/dg/register-location.html)\.
**Warning**  
You must use a user\-defined role and not the Lake Formation service\-linked role when you register this data location\. Lake Formation does not support using its service\-linked role when you integrate with EMR\.

1. Create a database using the data location you just registered\. For instructions, see [Creating a Database](https://docs.aws.amazon.com/lake-formation/latest/dg/creating-database.html)\.

1. Create a table in the database you set up in the previous step\. For instructions, see [Creating Tables](https://docs.aws.amazon.com/lake-formation/latest/dg/creating-tables.html)\.

1. Grant permissions to **Account B**\. Specify the following values:
   + The database you created in step 3\.
   + The table you created in step 4\.
   + The account ID of the external AWS account to which you want to grant access \(**Account B**\)\.
   + Check the box next to **Select** under both **Table** and **Grantable** permissions\. You must also select the **Describe** permission for the table\. When you grant permissions to an external account, the permissions do not extend to any of the principals in that external account\. You must allow grantable permissions so that Account B can *in turn* grant access to its IAM role for Lake Formation principal\. 

1. Complete the steps in [Allow Data Filtering on Amazon EMR](https://docs.aws.amazon.com/lake-formation/latest/dg/getting-started-setup.html#emr-switch) in the *Lake Formation Developer Guide* to opt in to data filtering for data lakes on Amazon EMR in Account A\. Specify the ID for Account B under **AWS account IDs** on the **External data filtering** page\.

**To finish setting up cross\-account access in Account B**

1. After you complete the preceding steps in Account A, log in to the AWS Management Console with **Account B**\.

1. In the AWS Resource Access Manager console, accept the shared resource invitation from Account A\. For instructions, see [Accessing Resources Shared With You](https://docs.aws.amazon.com/ram/latest/userguide/working-with-shared.html#working-with-shared-invitation) in the *AWS Resource Access Manager User Guide*\.

1. Access the AWS Lake Formation console as a [Data Lake Administrator](https://docs.aws.amazon.com/lake-formation/latest/dg/getting-started-setup.html#create-data-lake-admin)\.

1. Create a database resource link for the remote database shared by Account A\. Creating a resource link lets you assign a different name to the remote database in your Data Catalog\. For instructions, see [Creating a Resource Link to a Shared Data Catalog Database](https://docs.aws.amazon.com/lake-formation/latest/dg/create-resource-link-database.html)\.

1. On the **Databases** page, choose the database resource link you created previously\. 

1. In the **Actions** dropdown under **Permissions**, select **Grant**\. 

1. For **SAML and Amazon QuickSight users and groups**, select the SAML user\(s\) or group\(s\) you want to give access to, and make sure **Describe** is enabled under **Resource Link Permissions**\. For more information, see [Granting Resource Link Permissions](https://docs.aws.amazon.com/lake-formation/latest/dg/granting-link-permissions.html)\.

1. Choose **Grant** to finish granting permissions to your resource link\. 

1. In the **Actions** dropdown under **Permissions**, select **Grant on target**\. This step is required since granting permissions on a resource link does not grant permissions on a target \(linked\) database or table\. You must grant permissions on a target separately\. For more information, see [Granting Resource Link Permissions](https://docs.aws.amazon.com/lake-formation/latest/dg/granting-link-permissions.html)\.

1. For **SAML and Amazon QuickSight users and groups**, select your the same SAML user\(s\) or group\(s\) you chose in Step 7\.

1. Choose appropriate target permissions for your IAM role for Lake Formation, then choose **Grant**\.

You should now be able to access a resource in Account A from your EMR cluster in Account B\.

For example, say you created a resource link called **resource\-db\-link** and granted access to all the columns of a table, **t1**, in the **resource\-db\-link** database\. You can now retrieve the first column, **col1**, with the following Spark SQL query:

```
select col1 from resource-db-link.t1
```

For information about troubleshooting cross\-account access, see [Troubleshooting Lake Formation](https://docs.aws.amazon.com/lake-formation/latest/dg/troubleshooting.html#trouble-cross-account) in the *AWS Lake Formation Developer Guide*\. 