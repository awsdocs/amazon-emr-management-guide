# Set up an EMR Studio<a name="emr-studio-set-up"></a>

Complete the following steps to set up an Amazon EMR Studio\.

**Before you start**

**Note**  
If you plan to use EMR Studio with Amazon EMR on EKS, we recommend that you [Set up Amazon EMR on EKS for Amazon EMR Studio](emr-studio-create-eks-cluster.md) before you set up a Studio\.

Before you set up an Amazon EMR Studio, make sure you have the following items:
+ An AWS account\. For instructions, see [Setting up Amazon EMR](emr-setting-up.md)\.
+ Permissions to create and manage an EMR Studio\. For more information, see [Administrator permissions to create and manage an EMR Studio](emr-studio-admin-permissions.md)\.
+ An Amazon S3 bucket where EMR Studio can back up the Workspaces and notebook files in your Studio\. For instructions, see [Creating a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html) in the *Amazon Simple Storage Service \(S3\) User Guide*\.
+ An Amazon Virtual Private Cloud \(VPC\) for the Studio, and a maximum of five subnets\. For tips on how to configure networking, see [VPC and subnet best practices](emr-studio-considerations.md#emr-studio-vpc-subnet-best-practices)\.

**To set up an EMR Studio**

1.  [Choose an authentication mode for Amazon EMR Studio](emr-studio-authentication.md)

1. Create the following Studio resources\.
   + [Create an EMR Studio service role](emr-studio-service-role.md)
   + [Configure EMR Studio user permissions](emr-studio-user-permissions.md)
   + \(Optional\) [Define security groups to control EMR Studio network traffic](emr-studio-security-groups.md)\.

1. [Create an EMR Studio](emr-studio-create-studio.md)

1. [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)

After you complete the setup steps, you can [Use an Amazon EMR Studio](use-an-emr-studio.md)\.