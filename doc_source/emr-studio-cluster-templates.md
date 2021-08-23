# Create AWS CloudFormation templates for Amazon EMR Studio<a name="emr-studio-cluster-templates"></a>

## About EMR Studio cluster templates<a name="emr-studio-about-cluster-templates"></a>

You can create AWS CloudFormation templates to help EMR Studio users launch new Amazon EMR clusters in a Workspace\. CloudFormation templates are formatted text files in JSON or YAML\. In a template, you describe a stack of AWS resources and tell CloudFormation how to provision those resources for you\. For EMR Studio, you can create one or more templates that describe an Amazon EMR cluster\. 

You organize your templates in AWS Service Catalog\. AWS Service Catalog lets you create and manage commonly deployed IT services called *products* on AWS\. You collect your templates as products in a *portfolio* that you share with your EMR Studio users\. After you create cluster templates, Studio users can launch a new cluster for a Workspace with one of your templates\. Users must have permission to create new clusters from templates\. You can set user permissions in your [EMR Studio session policies](emr-studio-user-role.md#emr-studio-session-policies)\.

To learn more about CloudFormation templates, see [Templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-whatis-concepts.html#w2ab1b5c15b7) in the *AWS CloudFormation User Guide*\. For more information about AWS Service Catalog, see [What is AWS Service Catalog](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/introduction.html)\.

The following video demonstrates how to set up cluster templates in AWS Service Catalog for Amazon EMR Studio\. You can also learn more in the [Build a self\-service environment for each line of business using Amazon EMR and AWS Service Catalog](http://aws.amazon.com/blogs/big-data/build-a-self-service-environment-for-each-line-of-business-using-amazon-emr-and-aws-service-catalog/) blog post\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/9w_TXTdFLpo/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/9w_TXTdFLpo)

### Optional template parameters<a name="emr-studio-cluster-template-parameters"></a>

You can include additional options in the [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html) section of your template\. Parameters let Studio users input or select custom values for a cluster\. For example, you could add a parameter that lets users select a particular Amazon EMR release\. For more information, see [Parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html) in the *AWS CloudFormation User Guide*\.

The following example `Parameters` section defines additional input parameters such as `ClusterName`, `EmrRelease` version, and `ClusterInstanceType`\.

```
Parameters:
  SubnetId:
    Type: "String"
  ClusterName:
    Type: "String"
    Default: "Cluster_Name_Placeholder"
  EmrRelease:
    Type: "String"
    Default: "emr-6.2.0"
    AllowedValues:
    - "emr-6.2.0"
    - "emr-5.32.0"
  ClusterInstanceType:
    Type: "String"
    Default: "m5.xlarge"
    AllowedValues:
    - "m5.xlarge"
    - "m5.2xlarge"
```

When you add parameters, Studio users see additional form options after selecting a cluster template\. The following image shows additional form options for **EmrRelease** version, **ClusterName**, and **InstanceType**\.

![\[Screenshot of the additional inputs in the Studio user interface when a user selects a cluster template with parameters.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/cluster-template-parameters-studio-ui.jpg)

## Prerequisites<a name="emr-studio-cluster-template-prereqs"></a>

Before you create a cluster template, make sure you have IAM permissions to access the AWS Service Catalog administrator console view\. You also need the required IAM permissions to perform AWS Service Catalog administrative tasks\. For more information, see [Grant permissions to AWS Service Catalog administrators](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/getstarted-iamadmin.html)\. 

## Instructions<a name="emr-studio-cluster-template-instructions"></a>

**To create EMR cluster templates using AWS Service Catalog**

1. Create one or more CloudFormation templates\. Where you store your templates is up to you\. Since templates are formatted text files, you can upload them to Amazon S3 or keep them in your local file system\. To learn more about CloudFormation templates, see [Templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-whatis-concepts.html#w2ab1b5c15b7) in the *AWS CloudFormation User Guide*\.

   Use the following rules to name your templates, or check your names against the pattern `[a-zA-Z0-9][a-zA-Z0-9._-]*`\.
   + Template names must start with a letter or a number\.
   + Template names can only consist of letters, numbers, periods \(\.\), underscores \(\_\), and hyphens \(\-\)\.

   Each cluster template that you create must include the following options: 

   **Input parameters**
   + SubnetId – The ID of the subnet where the cluster will be launched\. EMR Studio fills this value the Workspace's subnet ID\.
**Important**  
Use the SubnetId parameter name throughout your whole cluster template when you specify a subnet\. EMR Studio does not support multiple subnets for Amazon EMR cluster features like instance fleets\.
   + ClusterName – A name for the cluster to help users identify it after it has been provisioned\.

   **Output**
   + ClusterId – The ID of the newly\-provisioned EMR cluster\.

   Following is an example AWS CloudFormation template in YAML format for a cluster with two nodes\. The example template includes the required template options, and defines additional input parameters for `EmrRelease` and `ClusterInstanceType`\.

   ```
   AWSTemplateFormatVersion: 2010-09-09
   
   Parameters:
     SubnetId:
       Type: "String"
     ClusterName:
       Type: "String"
       Default: "Example_Two_Node_Cluster"
     EmrRelease:
       Type: "String"
       Default: "emr-6.2.0"
       AllowedValues:
       - "emr-6.2.0"
       - "emr-5.32.0"
     ClusterInstanceType:
       Type: "String"
       Default: "m5.xlarge"
       AllowedValues:
       - "m5.xlarge"
       - "m5.2xlarge"
   
   Resources:
     EmrCluster:
       Type: AWS::EMR::Cluster
       Properties:
         Applications:
         - Name: Spark
         - Name: Livy
         - Name: JupyterEnterpriseGateway
         - Name: Hive
         EbsRootVolumeSize: '10'
         Name: !Ref ClusterName
         JobFlowRole: EMR_EC2_DefaultRole
         ServiceRole: EMR_DefaultRole
         ReleaseLabel: !Ref EmrRelease
         VisibleToAllUsers: true
         LogUri: 
           Fn::Sub: 's3://aws-logs-${AWS::AccountId}-${AWS::Region}/elasticmapreduce/'
         Instances:
           TerminationProtected: false
           Ec2SubnetId: !Ref SubnetId
           MasterInstanceGroup:
             InstanceCount: 1
             InstanceType: !Ref ClusterInstanceType
           CoreInstanceGroup:
             InstanceCount: 1
             InstanceType: !Ref ClusterInstanceType
             Market: ON_DEMAND
             Name: Core
   
   Outputs:
     ClusterId:
       Value:
         Ref: EmrCluster
       Description: The ID of the EMR Cluster
   ```

1. Create a portfolio for your cluster templates in the same AWS account as your Studio\. 

   1. Open the AWS Service Catalog console at [https://console\.aws\.amazon\.com/servicecatalog/](https://console.aws.amazon.com/servicecatalog/)\.

   1. Choose **Portfolios** in the left navigation menu\.

   1. Enter the requested information on the **Create portfolio** page\.

   1. Choose **Create**\. AWS Service Catalog creates the portfolio and displays the portfolio details\.

1. Use the following steps to add your cluster templates as AWS Service Catalog products\.

   1. Navigate to the **Products** page under **Administration** in the AWS Service Catalog management console\.

   1. Choose **Upload new product**\.

   1. Enter a **Product name** and **Owner**\.

   1. Specify your template file under **Version details**\. 

   1. Choose **Review** to review your product settings, then choose **Create product**\.

1. Complete the following steps to add your products to your portfolio\.

   1. Navigate to the **Products** page in the AWS Service Catalog management console\.

   1. Choose your product, choose **Actions**, then choose **Add product to portfolio**\.

   1. Choose your portfolio, then choose **Add product to portfolio**\.

1. Create a launch constraint for your products\. A launch constraint is an IAM role that specifies user permissions for launching a product\. You can tailor your launch constraints, but must allow permissions to use CloudFormation, Amazon EMR, and AWS Service Catalog\. For more information and instructions, see [AWS Service Catalog launch constraints](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/constraints-launch.html)\.

1. Apply your launch constraint to each product in your portfolio\. You must apply the launch constraint to each product individually\.

   1. Select your portfolio from the **Portfolios** page in the AWS Service Catalog management console\.

   1. Choose the **Constraints** tab and choose **Create constraint**\.

   1. Choose your product and choose **Launch** under **Constraint type**\. Choose **Continue**\.

   1. Select your launch constraint role in the **Launch constraint** section, then choose **Create**\.

1. Grant your [user role for EMR Studio](emr-studio-user-role.md) access to your portfolio\.

   1. Select your portfolio from the **Portfolios** page in the AWS Service Catalog management console\.

   1. Expand the **Groups, roles, and users** tab and choose **Add groups, roles, users**\.

   1. Search for your EMR Studio user role in the **Roles** tab, select your role, and choose **Add access**\.