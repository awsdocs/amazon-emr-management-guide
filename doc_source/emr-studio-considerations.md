# Considerations and limitations<a name="emr-studio-considerations"></a>

Consider the following when you work with Amazon EMR Studio:
+ EMR Studio is available in the following AWS Regions: US East \(N\. Virginia, Ohio\), US West \(Oregon\), Asia Pacific \(Mumbai, Seoul, Singapore, Sydney, Tokyo\), Canada \(Central\), EU \(Frankfurt, Ireland, London, Paris, Stockholm\), and South America \(Sao Paulo\)\.
+ EMR Studio works with Amazon EMR versions 5\.32\.0 \(EMR 5\.x series\) or 6\.2\.0 \(EMR 6\.x series\) and later\.
+ To let users provision new EMR clusters running on Amazon EC2 for a Workspace, you can associate an EMR Studio with a set of cluster templates\. Administrators can define cluster templates with AWS Service Catalog and can choose whether a user or group can access the cluster templates, or no cluster templates, within a Studio\.
+ When you define access permissions to notebook files stored in Amazon S3 or read secrets from AWS Secrets Manager, use the EMR service role\. Defining these permissions using session policies isn't supported\.
+ You can create multiple EMR Studios to control access to EMR clusters in different VPCs\.
+ Use the AWS CLI to set up Amazon EMR on EKS clusters\. You can then use the Studio interface to attach clusters to Workspaces with a managed endpoint to run notebook jobs\.
+ EMR Studio doesn't support the following Python magic commands:
  + `%alias`
  + `%alias_magic`
  + `%automagic`
  + `%macro`
  + `%%js`
  + `%%javascript`
  + Modifying `proxy_user` using `%configure`
  + Modifying `KERNEL_USERNAME` using `%env` or `%set_env`
+ Amazon EMR on EKS clusters don't support SparkMagic commands for EMR Studio\.
+ To write multi\-line Scala statements in notebook cells, make sure that all but the last line end with a period\. The following example uses the correct syntax for multi\-line Scala statements\.

  ```
  val df = spark.sql("SELECT * from table_name).
          filter("col1=='value'").
          limit(50)
  ```

## Known issues<a name="emr-studio-known-issues"></a>
+ Make sure you deactivate proxy management tools such as FoxyProxy or SwitchyOmega in the browser before you create a Studio\. Active proxies can cause errors when you choose **Create Studio**, and result in a **Network Failure ** error message\.
+ Kernels that run on Amazon EMR on EKS clusters can fail to start due to timeout issues\. If you encounter an error or issue starting the kernel, close the notebook file, shut down the kernel, and then reopen the notebook file\.
+ The **Restart kernel** operation doesn't work as expected when you use an Amazon EMR on EKS cluster\. After you select **Restart kernel**, refresh the Workspace for the restart to take effect\.
+ If a Workspace isn't attached to a cluster, an error message appears when a Studio user opens a notebook file and tries to select a kernel\. You can ignore this error message by choosing **Ok**, but you must attach the Workspace to a cluster and select a kernel before you can run notebook code\.
+ When you use Amazon EMR 6\.2\.0 with a [security configuration](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-security-configurations.html) to set up cluster security, the Workspace interface appears blank and doesn't work as expected\. We recommend that you use a different supported version of EMR if you want to configure data encryption or Amazon S3 authorization for EMRFS for a cluster\. EMR Studio works with Amazon EMR versions 5\.32\.0 \(EMR 5\.x series\) or 6\.2\.0 \(EMR 6\.x series\) and later\.
+ When you launch the on\-cluster Spark UI from a notebook file, you can see information about a job after running notebook code\. However, when you launch the Spark History Server from the Studio **Clusters** list, the job may not appear for up to two minutes\.
+ When you [Debug Amazon EMR running on Amazon EC2 jobs](emr-studio-debug.md#emr-studio-debug-ec2), the links to the on\-cluster Spark UI may not work or fail to appear\. To regenerate the links, create a new notebook cell and run the `%%info` command\.
+ Jupyter Enterprise Gateway doesn't clean up idle kernels on the master node of a cluster in the following Amazon EMR release versions: 5\.32\.0, 5\.33\.0, 6\.2\.0, and 6\.3\.0\. Idle kernels consume computing resources and can cause long running clusters to fail\. You can configure idle kernel cleanup for Jupyter Enterprise Gateway using the following example script\. You can [Connect to the master node using SSH](emr-connect-master-node-ssh.md), or submit the script as a step\. For more information, see [Run commands and scripts on an Amazon EMR cluster](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-commandrunner.html)\.

  ```
  #!/bin/bash
  sudo tee -a /emr/notebook-env/conf/jupyter_enterprise_gateway_config.py << EOF
  c.MappingKernelManager.cull_connected = True
  c.MappingKernelManager.cull_idle_timeout = 10800
  c.MappingKernelManager.cull_interval = 300
  EOF
  sudo systemctl daemon-reload
  sudo systemctl restart jupyter_enterprise_gateway
  ```
+ When you use an auto\-termination policy with Amazon EMR versions 5\.32\.0, 5\.33\.0, 6\.2\.0, or 6\.3\.0, EMR marks a cluster as idle and may automatically terminate the cluster even if you have an active Python3 kernel\. This is because executing a Python3 kernel does not submit a Spark job on the cluster\. To use auto\-termination with a Python3 kernel, we recommend that you use Amazon EMR version 6\.4\.0 or later\. For more information about auto\-termination, see [Using an auto\-termination policy](emr-auto-termination-policy.md)\.
+ When you use `%%display` to display a Spark DataFrame in a table, very wide tables may get truncated\. You can right\-click the output and select **Create New View for Output** to get a scrollable view of the output\.
+ Starting a Spark\-based kernel, such as PySpark, Spark, or SparkR, starts a Spark session, and running a cell in a notebook queues up Spark jobs in that session\. When you interrupt a running cell, the Spark job continues to run\. To stop the Spark job, you should use the on\-cluster Spark UI\. For instructions on how to connect to the Spark UI, see [Debug applications and jobs with EMR Studio](emr-studio-debug.md)\.

## Feature limitations<a name="emr-studio-limitations"></a>

Amazon EMR Studio doesn't support the following Amazon EMR features:
+ Attaching and running jobs on EMR clusters with a security configuration that specifies Kerberos authentication
+ Clusters with multiple master nodes
+ Clusters integrated with AWS Lake Formation
+ Clusters using Amazon EC2 instances based on AWS Graviton2

## Service limits for EMR Studio<a name="emr-studio-default-limits"></a>

The following table displays service limits for EMR Studio\.


****  

| Item | Limit | 
| --- | --- | 
| EMR Studios | Maximum of 10 per AWS account | 
| Subnets | Maximum of 5 associated with each EMR Studio | 
| AWS SSO Groups | Maximum of 5 assigned to each EMR Studio | 
| AWS SSO Users | Maximum of 100 assigned to each EMR Studio | 

## VPC and subnet best practices<a name="emr-studio-vpc-subnet-best-practices"></a>

Use the following best practices to set up an Amazon Virtual Private Cloud \(Amazon VPC\) with subnets for EMR Studio:
+ You can specify a maximum of five subnets in your VPC to associate with the Studio\. We recommend that you provide multiple subnets in different Availability Zones in order to support Workspace availability and give Studio users access to clusters across different Availability Zones\. To learn more about working with VPCs, subnets, and Availability Zones, see [VPCs and subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html) in the *Amazon Virtual Private Cloud User Guide*\.
+ The subnets that you specify should be able to communicate with each other\.
+ To let users link a Workspace to publicly hosted Git repositories, you should specify only private subnets that have access to the internet through Network Address Translation \(NAT\)\. For more information about setting up a private subnet for Amazon EMR, see [Private subnets](emr-clusters-in-a-vpc.md#emr-vpc-private-subnet)\.
+ When you use Amazon EMR on EKS with EMR Studio, there must be *at least one subnet in common* between your Studio and the Amazon EKS cluster that you use to register a virtual cluster\. Otherwise, your managed endpoint won't appear as an option in Studio Workspaces\. You can create an Amazon EKS cluster and associate it with a subnet that belongs to the Studio, or create a Studio and specify your EKS cluster's subnets\. For more information, see [Set up Amazon EMR on EKS for Amazon EMR Studio](emr-studio-create-eks-cluster.md)\.
+ If you plan to use Amazon EMR on EKS with EMR Studio, choose the same VPC as your Amazon EKS cluster worker nodes\.

## Cluster requirements for Amazon EMR Studio<a name="emr-studio-cluster-requirements"></a>

**Amazon EMR Clusters Running on Amazon EC2**

All Amazon EMR clusters running on Amazon EC2 that you create for an EMR Studio Workspace must meet the following requirements\. Clusters that you create using the EMR Studio interface automatically meet these requirements\.
+ The cluster must use Amazon EMR versions 5\.32\.0 \(EMR 5\.x series\) or 6\.2\.0 \(EMR 6\.x series\) or later\. You can create a cluster using the Amazon EMR console, AWS Command Line Interface, or SDK, and then attach it to an EMR Studio Workspace\. Studio users can also provision and attach clusters when creating or working in an Amazon EMR Workspace\. For more information, see [Attach a cluster to a Workspace](emr-studio-create-use-clusters.md)\.
+ The cluster must be within an Amazon Virtual Private Cloud\. The EC2\-Classic platform isn't supported\.
+ The cluster must have Spark, Livy, and Jupyter Enterprise Gateway installed\. You can install other applications, but EMR Studio is designed to work with Spark clusters\. 
+ The cluster must be in a private subnet with network address translation \(NAT\) to use publicly\-hosted Git repositories with EMR Studio\.

We recommend the following cluster configurations when you work with EMR Studio\.
+ Set deploy mode for Spark sessions to cluster mode\. Cluster mode places the application master processes on the core nodes and not on the master node of a cluster\. Doing so relieves the master node of potential memory pressures\. For more information, see [Cluster Mode Overview](https://spark.apache.org/docs/latest/cluster-overview.html) in the Apache Spark documentation\.
+ Change the Livy timeout from the default of one hour to six hours as in the following example configuration\.

  ```
  {
      "classification":"livy-conf",
          "Properties":{
              "livy.server.session.timeout":"6h",
              "livy.spark.deploy-mode":"cluster"
          }
  }
  ```
+ Create diverse instance fleets with up to 30 instances, and select multiple instance types in your Spot Instance fleet\. For example, you might specify the following memory\-optimized instance types for Spark workloads: r5\.2x, r5\.4x, r5\.8x, r5\.12x, r5\.16x, r4\.2x, r4\.4x, r4\.8x, r4\.12, etc\. For more information, see [Configure instance fleets](emr-instance-fleet.md)\.
+ Use the capacity\-optimized allocation strategy for Spot Instances to help Amazon EMR make effective instance selections based on real\-time capacity insights from Amazon EC2\. For more information, see [Allocation strategy for instance fleets](emr-instance-fleet.md#emr-instance-fleet-allocation-strategy)\.
+ Enable managed scaling on your cluster\. Set the maximum core nodes parameter to the minimum persistent capacity that you plan to use, and configure scaling on a well\-diversified task fleet that runs on Spot Instances to save on costs\. For more information, see [Using EMR managed scaling in Amazon EMR](emr-managed-scaling.md)\.

We also urge you to keep Amazon EMR Block Public Access enabled, and that to restrict inbound SSH traffic to trusted sources\. Inbound access to a cluster lets users run notebooks on the cluster\. For more information, see [Using Amazon EMR block public access](emr-block-public-access.md) and [Control network traffic with security groups](emr-security-groups.md)\.

**Amazon EMR on EKS Clusters**

In addition to EMR clusters running on Amazon EC2, you can set up and manage Amazon EMR on EKS clusters for EMR Studio using the AWS CLI\. Set up EMR on EKS clusters using the following guidelines:
+ Create a managed HTTPS endpoint for the Amazon EMR on EKS cluster\. Users attach a Workspace to a managed endpoint\. The Amazon Elastic Kubernetes Service \(EKS\) cluster that you use to register a virtual cluster must have a private subnet to support managed endpoints\.
+ Use an Amazon EKS cluster with at least one private subnet and network address translation \(NAT\) when you want to use publicly\-hosted Git repositories\.
+ Avoid using [Amazon EKS optimized Arm Amazon Linux AMIs](https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html#arm-ami), which aren't supported for EMR on EKS managed endpoints\.
+ Avoid using AWS Fargate\-only Amazon EKS clusters, which aren't supported\.

For more information, see [Set up Amazon EMR on EKS for Amazon EMR Studio](emr-studio-create-eks-cluster.md)\.