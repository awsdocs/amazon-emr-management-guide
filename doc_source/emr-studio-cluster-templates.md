# Create Cluster Templates in AWS Service Catalog<a name="emr-studio-cluster-templates"></a>

## About EMR Studio Cluster Templates<a name="emr-studio-about-cluster-templates"></a>

We recommend that you enable your team to use cluster templates with an EMR Studio\. Doing so lets users quickly choose from a list of templates to create a cluster for a Workspace\. It also lets you specify how new clusters should be created, and it simplifies Workspace setup\. You can define cluster templates using AWS Service Catalog\. For more information about using templates with Amazon EMR, see the [Build a self\-service environment for each line of business using Amazon EMR and AWS Service Catalog](http://aws.amazon.com/blogs/big-data/build-a-self-service-environment-for-each-line-of-business-using-amazon-emr-and-aws-service-catalog/) blog post\.

Each template must specify the following options:
+ Input parameters
  + **SubnetId** – The ID of the subnet where the cluster should be launched\. EMR Studio uses the same subnet that is associated with the Workspace to fill in the **SubnetId** parameter when provisioning the cluster\.
**Important**  
In order for your cluster to appear in the Workspace after it has been launched, you must refer to the **SubnetId** parameter throughout your entire cluster template when you specify a subnet\. EMR Studio does not support multiple subnets for Amazon EMR cluster features such as instance fleets\.
  + **ClusterName** – A name for the cluster to help users identify it after it has been provisioned\.
+ Output
  + **ClusterId** – The ID of the newly\-provisioned EMR cluster\.

You can also include additional options in the `Parameters` section in your template, which lets Studio users input or select custom values for a cluster\. The following example `Parameters` section defines additional input parameters such as **ClusterName**, **EmrRelease** version, and **ClusterInstanceType**\.

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

Following is an example of the cluster options that a Studio user sees after selecting a cluster template with parameters\.

![\[Screenshot of inputs in the Studio user interface when a user selects a cluster template with parameters.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/cluster-template-parameters-studio-ui.jpg)

For more information, see [Parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html) in the *AWS CloudFormation User Guide*\.

## Prerequisites<a name="emr-studio-cluster-template-prereqs"></a>

Before you create a template, make sure you have IAM permissions to access the AWS Service Catalog administrator console view as well as any IAM permissions needed to perform AWS Service Catalog administrative tasks\. For more information, see [Grant Permissions to AWS Service Catalog Administrators](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/getstarted-iamadmin.html)\. 

## Instructions<a name="emr-studio-cluster-template-instructions"></a>

**To create EMR cluster templates using AWS Service Catalog**

1. Create a portfolio for your cluster templates in the same AWS account as your Studio\. For instructions, see [Creating and Deleting Portfolios](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/portfoliomgmt-create.html)\.

1. Create one or more cluster templates \(also called portfolio *products* in the context of AWS CloudFormation\)\. 

   Use the following rules to name your templates, or check your names against the pattern `[a-zA-Z0-9][a-zA-Z0-9._-]*`\.
   + Template names must start with a letter or a number\.
   + Templates names can consist of only letters, numbers, periods \(\.\), underscores \(\_\), and hyphens \(\-\)\.

   Each template must include the following options: 
   + Input parameters
     + **SubnetId** – Denotes the ID of the subnet where the cluster should be launched\. EMR Studio uses the same subnet that is associated with the Workspace to fill in the **SubnetId** parameter when provisioning the cluster\.
**Important**  
In order for your cluster to appear in the Workspace after it has been launched, you must refer to the **SubnetId** parameter throughout your entire cluster template when you specify a subnet\. EMR Studio does not support multiple subnets for Amazon EMR cluster features such as instance fleets\.
     + **ClusterName** – A name for the cluster to help users identify it after it has been provisioned\.
   + Output
     + **ClusterId** – The ID of the newly\-provisioned EMR cluster\.

   Following is an example AWS CloudFormation template for a cluster with two nodes and additional input parameters that meets all of the [Cluster Requirements for Amazon EMR Studio](emr-studio-considerations.md#emr-studio-cluster-requirements)\.

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

   For instructions, see [Creating Products](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/productmgmt-cloudresource.html)\.

1. Add your templates to the portfolio that you created in step 1\. For more information, see [Adding Products to Portfolios](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/catalogs_portfolios_adding-products.html)\.

1. For each template \(product\) in your portfolio, create a launch constraint and apply the constraint to that template\. A launch constraint specifies the AWS Identity and Access Management \(IAM\) role that AWS Service Catalog assumes when a user launches a product\. For more information and instructions, see [AWS Service Catalog Launch Constraints](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/constraints-launch.html)\.

1. Grant access to your portfolio to the [user role for EMR Studio](emr-studio-user-role.md)\. For instructions, see [Granting Access to Users](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/catalogs_portfolios_users.html)\. 