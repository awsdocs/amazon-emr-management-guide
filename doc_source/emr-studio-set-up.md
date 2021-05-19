# Set Up and Manage an Amazon EMR Studio<a name="emr-studio-set-up"></a>

This section includes instructions for creating an Amazon EMR Studio for your team and configuration options for your Studio\.

## Before You Start<a name="emr-studio-set-up-prereqs"></a>
+ [Enable AWS Single Sign\-On for Amazon EMR Studio](emr-studio-enable-sso.md) using the *management* account of your AWS organization\. To learn more about AWS account terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
+ If you plan to use EMR Studio with Amazon EMR on EKS, we recommend that you [Set Up Amazon EMR on EKS for Your Studio](emr-studio-create-eks-cluster.md) before you set up a Studio\.

## Instructions<a name="emr-studio-set-up-instructions"></a>

Use a *member* account in your AWS organization to set up an EMR Studio according to the following steps\.

**To set up an EMR Studio**

1. Create and configure the following EMR Studio resources\.
   + [Create an EMR Studio Service Role](emr-studio-service-role.md)
   + [Create an EMR Studio User Role with Session Policies](emr-studio-user-role.md)
   + [Define Security Groups to Control EMR Studio Network Traffic](emr-studio-security-groups.md)
   + [Establish Access and Permissions for Git\-Based Repositories](emr-studio-enable-git.md)

   You can also use AWS CloudFormation to create your EMR Studio resources\. For an example CloudFormation template, see `full_studio_dependencies.yml` in the [emr\-studio\-samples GitHub repository](https://github.com/aws-samples/emr-studio-samples/blob/main/full_studio_dependencies.yml)\. For more information about CloudFormation, see [Working with templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html) and [Working with stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html)\.

1. [Add Required Permissions to Create and Manage an EMR Studio](emr-studio-admin-permissions.md)

1. [Create an EMR Studio](emr-studio-create-studio.md)

1. [Assign a User or Group to Your EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)

1. \(Optional\) Though not required, we recommend that you enable your team to use cluster templates with your EMR Studio\. For more information, see [Create Cluster Templates in AWS Service Catalog](emr-studio-cluster-templates.md)\.