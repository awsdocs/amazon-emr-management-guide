# Attach a cluster to a Workspace<a name="emr-studio-create-use-clusters"></a>

Amazon EMR Studio runs notebook commands using a kernel on an EMR cluster\. Before you can select a kernel, you should attach the Workspace to a cluster that uses Amazon EC2 instances, or to an EMR on EKS cluster\. EMR Studio lets you attach Workspaces to new or existing clusters, and gives you the flexibility to change clusters without closing the Workspace\.

**Topics**
+ [Attach a running cluster to a Workspace to run notebooks](#emr-studio-attach-cluster)
+ [Use an Amazon EMR on EKS cluster to run Notebook code](#emr-studio-use-eks-cluster)
+ [Create a new EMR Cluster for a Workspace](#emr-studio-create-cluster)
+ [Detach a cluster from a Workspace](#emr-studio-detach-cluster)

## Attach a running cluster to a Workspace to run notebooks<a name="emr-studio-attach-cluster"></a>

You can attach an existing EMR cluster running on Amazon EC2 to a Workspace when you create the Workspace, or you can choose a cluster from the Workspace user interface\. If you want to create and attach a *new* cluster, see [Create a new EMR Cluster for a Workspace](#emr-studio-create-cluster)\.

------
#### [ Create a Workspace dialog box ]

**To attach a running cluster when you create a Workspace**

1. In the **Create a Workspace** dialog box, make sure you've already selected a subnet for the new Workspace\. Expand the **Advanced configuration** section\.

1. Choose **Attach Workspace to an EMR cluster**\.

1. In the **EMR cluster** dropdown list, select an existing EMR cluster to attach it to the Workspace\.

After you attach a cluster, you can finish the Workspace creation process\. When you open the new Workspace for the first time and choose the **EMR Clusters** panel, you should see that your selected cluster is attached\.

------
#### [ Workspace UI ]

**To attach a running cluster from the Workspace user interface**

1. In the Workspace that you want to attach to a cluster, choose the **EMR Clusters** icon from the left sidebar to open the **Cluster** panel\.

1. Under **Cluster type**, expand the dropdown and select **EMR Cluster on EC2**\.

1. Choose a cluster from the dropdown list\. You might need to detach an existing cluster first to enable the cluster selection dropdown list\.

1. Choose **Attach**\. When the cluster is attached, you should see a success message appear\.

------

## Use an Amazon EMR on EKS cluster to run Notebook code<a name="emr-studio-use-eks-cluster"></a>

In addition to using Amazon EMR clusters running on Amazon EC2, you can attach a Workspace to an Amazon EMR on EKS cluster to run notebook code\. For more information about EMR on EKS, see [What is Amazon EMR on EKS](https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/emr-eks.html)\.

Before you can connect a Workspace to an EMR on EKS cluster, your Studio administrator must grant you access permissions\.

------
#### [ Create a Workspace dialog box ]

**To attach an EMR on EKS cluster when you create a Workspace**

1. In the **Create a Workspace** dialog box, expand the **Advanced configuration** section\.

1. Choose **Attach Workspace to an EMR on EKS cluster**\.

1. Under **EMR on EKS cluster**, choose a cluster from the dropdown list\.

1. Under **Select an endpoint**, choose a managed endpoint to attach to the Workspace\. A managed endpoint is a gateway that lets EMR Studio communicate with your chosen cluster\.

1. Choose **Create Workspace** to finish the Workspace creation process and attach the selected cluster\.

After you attach a cluster, you can finish the Workspace creation process\. When you open the new Workspace for the first time and choose the **EMR Clusters** panel, you should see that your selected cluster is attached\.

------
#### [ Workspace UI ]

**To attach an EMR on EKS cluster from the Workspace user interface**

1. In the Workspace that you want to attach to a cluster, choose the **EMR Clusters** icon from the left sidebar to open the **Cluster** panel\.

1. Expand the **Cluster type** dropdown and choose **EMR clusters on EKS**\.

1. Under **EMR cluster on EKS**, choose a cluster from the dropdown list\.

1. Under **Endpoint**, choose a managed endpoint to attach to the Workspace\. A managed endpoint is a gateway that lets EMR Studio communicate with your chosen cluster\.

1. Choose **Attach**\. When the cluster is attached, you should see a success message appear\.

------

## Create a new EMR Cluster for a Workspace<a name="emr-studio-create-cluster"></a>

Advanced EMR Studio users can provision new EMR clusters running on Amazon EC2 to use with a Workspace\. The new cluster has all of the big data applications that are required for EMR Studio installed by default\. 

To create clusters, your Studio administrator must first give you permission using a session policy\. For more information, see [Create permissions policies for EMR Studio users](emr-studio-user-permissions.md#emr-studio-permissions-policies)\.

You can create a new cluster in the **Create a Workspace** dialog box or from the **Cluster** panel in the Workspace UI\. Either way, you have two cluster creation options:

1. **Create an EMR cluster** – Create an EMR cluster by choosing the Amazon EC2 instance type and count\.

1. **Use a cluster template** – Provision a cluster by selecting a predefined cluster template\. This option appears if you have permission to use cluster templates\.

**To create an EMR cluster by providing a cluster configuration**

1. Choose a starting point\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-create-use-clusters.html)

1. Enter a **Cluster name**\. Naming the cluster helps you find it later in the EMR Studio Clusters list\.

1. For **EMR release**, Choose an EMR release version for the cluster\.

1. For **Instance**, select the type and number of Amazon EC2 instances for the cluster\. For more information about selecting instance types, see [Configure Amazon EC2 instances](emr-plan-ec2-instances.md)\. One instance will be used as the master node\.

1. Select a **Subnet** where EMR Studio can launch the new cluster\. Each subnet option is preapproved by your Studio administrator, and your Workspace should be able to connect to a cluster in any listed subnet\.

1. Choose an **S3 URI for log storage**\.

1. Choose **Create EMR cluster** to provision the cluster\. If you use the **Create a Workspace** dialog box, choose **Create Workspace** to create the Workspace and provision the cluster\. After EMR Studio provisions the new cluster, it attaches the cluster to the Workspace\.

**To create a cluster using a cluster template**

1. Choose a starting point\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-create-use-clusters.html)

1. Select a cluster template from the dropdown list\. Each available cluster template includes a brief description to help you make a selection\.

1. The cluster template you choose may have additional parameters such as Amazon EMR release version or cluster name\. You can choose or insert values, or use the default values that your administrator selected\.

1. Select a **Subnet** where EMR Studio can launch the new cluster\. Each subnet option is preapproved by your Studio administrator, and your Workspace should be able to connect to a cluster in any subnet\.

1. Choose **Use cluster template** to provision the cluster and attach it to the Workspace\. It will take a few minutes for EMR Studio to create the cluster\. If you use the **Create a Workspace** dialog box, choose **Create Workspace** to create the Workspace and provision the cluster\. After EMR Studio provisions the new cluster, it attaches the cluster to your Workspace\.

## Detach a cluster from a Workspace<a name="emr-studio-detach-cluster"></a>

To exchange the cluster attached to a Workspace, you can detach a cluster from the Workspace UI\.

**To detach a cluster from a Workspace**

1. In the Workspace that you want to detach from a cluster, choose the **EMR Clusters** icon from the left sidebar to open the **Cluster** panel\.

1. Under **Select cluster**, choose **Detach** and wait for EMR Studio to detach the cluster\. When the cluster is detached, you will see a success message\.