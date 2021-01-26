# Set Up an Amazon EMR Studio for Your Team<a name="emr-studio-set-up"></a>

This section includes instructions for creating an Amazon EMR Studio for your team and configuration options for your Studio\. You must provision Studio dependencies such as IAM roles, user session policies, and security groups before you create a Studio\.

**To set up an EMR Studio**

1. [Enable AWS Single Sign\-On for Amazon EMR Studio](emr-studio-enable-sso.md) using the management account of your AWS organization\. To learn more about AWS terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 

1. Use a *member* account to set up dependencies, create an EMR Studio, and assign users and groups according to the following steps:

   1. [Create an EMR Studio Service Role](emr-studio-service-role.md)

   1. [Create an EMR Studio User Role with Session Policies](emr-studio-user-role.md)

   1. [Define Security Groups to Control EMR Studio Network Traffic](emr-studio-security-groups.md)

   1. [Add Required Permissions to Create and Manage an EMR Studio](emr-studio-admin-permissions.md)

   1. \(Optional\) [Set Up Amazon EMR on EKS for Your Studio](emr-studio-create-eks-cluster.md)

   1. \(Optional\) [Create Cluster Templates in AWS Service Catalog](emr-studio-cluster-templates.md)

   1. [Create an EMR Studio](emr-studio-create-studio.md)

   1. [Assign a User or Group to Your EMR Studio](emr-studio-assign-users-groups.md)

**Note**  
To create a Studio for testing purposes, see [https://github\.com/aws\-samples/emr\-studio\-samples GitHub](https://github.com/aws-samples/emr-studio-samples)\. The repository includes a script that you can use to provision a test environment and create a Studio connected to that test environment\. Charges accrue for the AWS resources that the script provisions, such as the Amazon VPC, subnets, and the AWS Service Catalog portfolio of AWS CloudFormation templates\.