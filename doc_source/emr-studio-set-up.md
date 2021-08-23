# Set up and manage an Amazon EMR Studio<a name="emr-studio-set-up"></a>

This section includes instructions for creating an Amazon EMR Studio for your team and configuration options for your Studio\.

## Before you start<a name="emr-studio-set-up-prereqs"></a>
+ [Enable AWS Single Sign\-On for Amazon EMR Studio](emr-studio-enable-sso.md) using the *management* account of your AWS Organizations\. To learn more about AWS account terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
+ If you plan to use EMR Studio with Amazon EMR on EKS, we recommend that you [Set up Amazon EMR on EKS for your Studio](emr-studio-create-eks-cluster.md) before you set up a Studio\.

## Instructions<a name="emr-studio-set-up-instructions"></a>

Use a *member* account in your AWS organization to set up an EMR Studio according to the following steps\.

**To set up an EMR Studio**

1. Create and configure the following EMR Studio resources\.
   + [Create an EMR Studio service role](emr-studio-service-role.md)
   + [Create an EMR Studio user role with session policies](emr-studio-user-role.md)
   + [Define security groups to control EMR Studio network traffic](emr-studio-security-groups.md)
   + [Establish access and permissions for Git\-based repositories](emr-studio-enable-git.md)

   You can also use AWS CloudFormation to create your EMR Studio resources\. For an example CloudFormation template, see `full_studio_dependencies.yml` in the [emr\-studio\-samples GitHub repository](https://github.com/aws-samples/emr-studio-samples/blob/main/full_studio_dependencies.yml)\. For more information about CloudFormation, see [Working with templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html) and [Working with stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html)\.

1. [Add required permissions to create and manage an EMR Studio](emr-studio-admin-permissions.md)

1. [Create an EMR Studio](emr-studio-create-studio.md)

1. [Assign a user or group to your EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)

1. \(Optional\) Though not required, we recommend that you enable your team to use cluster templates with your EMR Studio\. For more information, see [Create AWS CloudFormation templates for Amazon EMR Studio](emr-studio-cluster-templates.md)\.