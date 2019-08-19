# Overview of the IAM Roles for Lake Formation<a name="emr-lf-iam-role"></a>

The integration between Amazon EMR and AWS Lake Formation relies on three key roles: the IAM role for Lake Formation, the IAM role for AWS services, and the EC2 instance profile for Amazon EMR\. This section gives an overview of these roles and the policies that you need to include for each role\. 

For more information about how to configure the roles for Lake Formation, see [Configure Trust Relationship Between your IdP and Lake Formation](emr-lf-federation.md)\. 

## The IAM Role for Lake Formation<a name="emr-lf-iam-role-lake-formation"></a>

The IAM Role for Lake Formation defines what privileges a user logging in through your IdP will have and which Identity Provider can assume this role\. The role’s `Maximum CLI/API session duration` defines the session timeout for access to EMR Notebooks and Apache Zeppelin\.
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
        "glue:GetUserDefinedFunction",
        "glue:GetUserDefinedFunctions"
      ],
      "Resource": "*"
    }
  }
  ```
**Note**  
Do not grant this role permissions to access any Amazon S3 buckets managed by AWS Glue\. The federated user should access data through Lake Formation using Spark SQL and should not access data directly through Amazon S3\.
+ The role must also include the following trust policy, which allows your IAM identity provider to assume the role\. Replace *account\-id* with your AWS account ID\. Replace the *IAM\_identity\_provider\_name* with your IAM identity provider’s name\. 

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
      },
      {
        "Effect": "Allow",
        "Principal": {
          "Service": [
            "glue.amazonaws.com",
            "lakeformation.amazonaws.com"
          ]
        },
        "Action": "sts:AssumeRole"
      }
    ]
  }
  ```

## The IAM Role for AWS Services<a name="emr-lf-iam-role-AWS-services"></a>

The IAM Role for AWS Services defines the permissions that the Amazon EMR cluster has when accessing non\- AWS Lake Formation services\. For example, if the jobs running on your cluster require access to Amazon DynamoDB, or any other AWS services, the IAM role for AWS Services must contain the policies required to access those services\. When you configure policies for this role, make sure the role does not have access to the following API operations:
+ Any AWS Glue API operations\.
+ Any AWS Lake Formation API operations\.
+ Any AWS Security Token Service \(STS\) `AssumeRole` operations\.
+ Any Amazon S3 access to buckets managed by AWS Glue\. The cluster should access data through Lake Formation using Spark SQL and should not access data directly through Amazon S3\.

## The EC2 Instance Profile<a name="emr-lf-iam-role-EC2"></a>

The EC2 instance profile is a special type of service role that defines permissions for EMR clusters to interact with Lake Formation and other AWS services\. You may use the `EMR_EC2_DefaultRole` when launching a cluster or choose to use a customized EC2 instance profile\. In either case, the following policy must be added to the role including references to both the IAM Role for Lake Formation and IAM Role for AWS Services role\. Replace *account\-id* with your AWS account ID\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::account-id:role/IAM_Role_For_Lake_Formation"
        },
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::account-id:role/IAM_Role_for_AWS_Services"
        },
        {
            "Effect": "Allow",
            "Action": "lakeformation:GetTemporaryUserCredentialsWithSAML",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:GetRole",
            "Resource": "arn:aws:iam::*:role/*"
        }
    ]
}
```

For more information, see [Service Role for Cluster EC2 Instances \(EC2 Instance Profile\)](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-iam-role-for-ec2.html) and [Customize IAM Roles](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-iam-roles-custom.html)\.