# IAM roles for Lake Formation<a name="emr-lf-iam-role"></a>

The integration between Amazon EMR and AWS Lake Formation relies on three key roles you should create before launching your cluster: 

1. A custom Amazon EC2 instance profile for Amazon EMR\.

1. An IAM role for Lake Formation use for identity federation\.

1. An IAM role for non\-Lake Formation AWS services\.

This section gives an overview of these roles and the policies that you need to include for each role\. For information about creating these roles, see [Before you begin](emr-lf-prerequisites.md)\.

## EC2 instance profile<a name="emr-lf-iam-role-EC2"></a>

Amazon EMR uses IAM service roles to perform actions on your behalf to provision and manage clusters\. The service role for cluster EC2 instances, also called the EC2 instance profile for Amazon EMR, is a special type of service role assigned to every EC2 instance in a cluster during launch\. 

To define permissions for EMR clusters to interact with Lake Formation and other AWS services, you should define a custom EC2 instance profile to use instead of the `EMR_EC2_DefaultRole` when you launch your cluster\.

For more information, see [Service role for cluster EC2 instances \(EC2 instance profile\)](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-iam-role-for-ec2.html) and [Customize IAM roles](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-iam-roles-custom.html)\. 

## IAM role for Lake Formation<a name="emr-lf-iam-role-lake-formation"></a>

The IAM Role for Lake Formation defines what privileges a user logging in through your IdP will have and which identity provider \(IdP\) can assume this role\. The role's `Maximum CLI/API session duration` defines the session timeout for access to EMR Notebooks and Apache Zeppelin\.
+ This role must be created with the following permissions policy\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": {
      "Effect": "Allow",
      "Action": [
        "lakeformation:GetDataAccess",
        "lakeformation:GetMetadataAccess",
        "glue:GetUnfiltered*",
        "glue:GetTable",
        "glue:GetTables",
        "glue:GetDatabase",
        "glue:GetDatabases",
        "glue:CreateDatabase",
        "glue:GetUserDefinedFunction",
        "glue:GetUserDefinedFunctions",
        "glue:GetPartition",
        "glue:GetPartitions"
      ],
      "Resource": "*"
    }
  }
  ```
**Note**  
Do not grant this role permissions to access any Amazon S3 buckets managed by AWS Glue\. The federated user should access data through Lake Formation using Spark SQL and should not access data directly through Amazon S3\.
+ The role must also include the following trust policy, which allows your IAM identity provider to assume the role\. Replace *account\-id* with your AWS account ID\. Replace the *IAM\_identity\_provider\_name* with your IAM identity provider's name\. 

  ```
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Effect": "Allow",
              "Principal": {
                  "Federated": "arn:aws:iam::account-id:saml-provider/IAM_identity_provider_name"
              },
              "Action": "sts:AssumeRoleWithSAML"
          }
      ]
  }
  ```

## IAM role for non\-Lake Formation AWS services<a name="emr-lf-iam-role-AWS-services"></a>

The IAM Role for AWS Services defines the permissions that the Amazon EMR cluster has when accessing non\-AWS Lake Formation services\. For example, if the jobs running on your cluster require access to Amazon DynamoDB, or any other AWS services, the IAM role for AWS Services should have policies required to access those services\.

When you configure policies for this role, make sure the role does not have access to the following API operations:
+ Any AWS Glue API operations\.
+ Any AWS Lake Formation API operations\.
+ Any AWS Security Token Service \(STS\) `AssumeRole` operations\.
+ Any Amazon S3 access to buckets managed by AWS Glue\. The cluster should access data through Lake Formation using Spark SQL and should not access data directly through Amazon S3\.