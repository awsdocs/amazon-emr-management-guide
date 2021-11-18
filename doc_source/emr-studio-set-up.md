# Set up an Amazon EMR Studio<a name="emr-studio-set-up"></a>

This section is for Amazon EMR Studio administrators\. It covers how to set up an Amazon EMR Studio for your team and provides instructions for tasks such as assigning users and groups, setting up cluster templates, and optimizing Apache Spark for Amazon EMR Studio\.

## Quickstart: The EMR Studio workshop<a name="emr-studio-workshop"></a>

The [Amazon EMR developer experience workshop](https://emr-developer-experience.workshop.aws/) helps you build a foundational knowledge of Amazon EMR Studio through a series of lab activities\. In the workshop, you set up a Studio, create and attach an Amazon EMR cluster to a Workspace in the Studio, and analyze data using a sample notebook\.

## How to set up an Amazon EMR Studio<a name="emr-studio-set-up-instructions"></a>

The steps below cover how to set up resources for an Amazon EMR Studio, how to create a Studio, and how to assign users and groups\.

**Note**  
If you plan to use EMR Studio with Amazon EMR on EKS, we recommend that you [Set up Amazon EMR on EKS for Amazon EMR Studio](emr-studio-create-eks-cluster.md) before you set up a Studio\.

**To set up an EMR Studio**

1. Choose AWS Identity and Access Management \(IAM\) or AWS SSO authentication for EMR Studio, and complete the steps to configure your chosen authentication mode\. For more information, see [Choose an authentication mode for Amazon EMR Studio](emr-studio-authentication.md)\.

1. Complete the following tasks\.
   + [Create an EMR Studio service role](emr-studio-service-role.md)
   + [Configure EMR Studio user permissions](emr-studio-user-permissions.md)
   + [Define security groups to control EMR Studio network traffic](emr-studio-security-groups.md)
   + [Establish access and permissions for Git\-based repositories](emr-studio-enable-git.md)

   You can also use AWS CloudFormation to create EMR Studio resources\. For an example CloudFormation template, see `full_studio_dependencies.yml` in the [emr\-studio\-samples GitHub repository](https://github.com/aws-samples/emr-studio-samples/blob/main/full_studio_dependencies.yml)\. For more information about CloudFormation, see [Working with templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html) and [Working with stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html)\.

1. Add the [Administrator permissions to create and manage an EMR Studio](emr-studio-admin-permissions.md)

1. [Create an EMR Studio](emr-studio-create-studio.md)

1. [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)

1. \(Optional\) Though not required, we recommend that you let your team to use cluster templates with EMR Studio\. For more information, see [Create AWS CloudFormation templates for Amazon EMR Studio](emr-studio-cluster-templates.md)\.