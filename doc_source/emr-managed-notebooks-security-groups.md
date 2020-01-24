# Specifying EC2 Security Groups for EMR Notebooks<a name="emr-managed-notebooks-security-groups"></a>

When you create an EMR notebook, two security groups are used to control network traffic between the EMR notebook and the Amazon EMR cluster when the notebook editor is used\. The default security groups have minimal rules that allow only network traffic between the EMR Notebooks service and the clusters to which notebooks are attached\.

An EMR notebook uses [Apache Livy](https://livy.incubator.apache.org/) to communicate with the cluster via a proxy using TCP Port 18888\. By creating custom security groups with rules tailored to your environment, you can limit network traffic so that only a subset of notebooks can run code within the notebook editor on particular clusters\. The security groups are used in addition to the security groups for the cluster\. For more information, see [Control Network Traffic with Security Groups](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-security-groups.html) in the *Amazon EMR Management Guide* and [Specifying EC2 Security Groups for EMR Notebooks](#emr-managed-notebooks-security-groups)\.

## Default EC2 Security Group for the Master Instance<a name="emr-managed-notebooks-security-group-for-master"></a>

The default EC2 security group for the master instance is associated with the master instance in addition to the cluster's security groups for the master instance\.

Group Name: **ElasticMapReduceEditors\-Livy**

**Rules**
+ Inbound

  Allow TCP Port 18888 from any resources in the default EC2 security group for EMR Notebooks
+ Outbound

  None

## Default EC2 Security Group for EMR Notebooks<a name="emr-managed-notebooks-security-group-for-notebooks"></a>

The default EC2 security group for the EMR notebook is associated with the notebook editor for any EMR notebook to which it is assigned\.

Group Name: **ElasticMapReduceEditors\-Editor**

**Rules**
+ Inbound

  None
+ Outbound

  Allow TCP Port 18888 to any resources in the default EC2 security group for EMR Notebooks\.

## Custom EC2 Security Group for EMR Notebooks When Associating Notebooks with Git Repositories<a name="emr-managed-notebooks-security-group-for-notebooks-git"></a>

To link a Git repository to your notebook, the security group for the EMR notebook must include an outbound rule to allow the notebook to route traffic to the internet\. It is recommended that you create a new security group for this purpose\. Updating the default **ElasticMapReduceEditors\-Editor** security group may give the same outbound rules to other notebooks that are attached to this security group\. 

**Rules**
+ Inbound

  None
+ Outbound

  Allow the notebook to route traffic to the internet via the cluster, as the following example demonstrates:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks-security-groups.html)