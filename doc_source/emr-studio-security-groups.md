# Define Security Groups to Control EMR Studio Network Traffic<a name="emr-studio-security-groups"></a>

## About the EMR Studio Security Groups<a name="emr-studio-about-security-groups"></a>

Amazon EMR Studio uses two security groups to control network traffic between Workspaces in the Studio and an attached Amazon EMR cluster running on Amazon EC2:
+ An **engine security group**, which uses port 18888 to communicate with an attached Amazon EMR cluster running on Amazon EC2\.
+ A **Workspace security group** associated with the Workspaces in a Studio\. This security group includes an outbound HTTPS rule to allow the Workspace to route traffic to the internet and must allow outbound traffic to the internet on port 443 to enable linking Git repositories to a Workspace\.

EMR Studio uses these security groups in addition to any security groups associated with an EMR cluster attached to a Workspace\. 

You must create these security groups\. You can customize your security groups with rules tailored to your environment, but you must include the rules noted below\. For example, you might add custom rules to limit network traffic so that only a subset of Workspaces can run code on particular clusters\. 

## Prerequisites<a name="emr-studio-security-group-prereqs"></a>

To create the security groups for EMR Studio, you need the following items:
+ A designated AWS account for your EMR Studio\. You must create your EMR Studio security groups in the same account\. If you use multiple accounts in your AWS organization, use a *member* account\. To learn more about AWS terminology, see [AWS Organizations terminology and concepts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html)\. 
+ An Amazon Virtual Private Cloud \(VPC\) designated for your Studio in your chosen account\. You choose this VPC when you create your security groups\. This should be the same VPC that you specify when you create your Studio\. If you plan to use Amazon EMR on EKS with EMR Studio, choose the VPC to which your Amazon EKS cluster worker nodes belong\.

## Instructions<a name="emr-studio-security-group-instructions"></a>

Follow the instructions in [Creating a security group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/working-with-security-groups.html#creating-security-group) in the *Amazon EC2 User Guide for Linux Instances* to create an engine security group and a Workspace security group in your designated VPC\. The security groups must include the rules noted below\.

When you create your security groups for EMR Studio, note the IDs for both\. You specify each security group by ID when you create a Studio\.

**Engine security group**  
EMR Studio uses port 18888 to communicate with an attached cluster\.    
**Inbound Rules**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-security-groups.html)

**Workspace security group**  
This security group is associated with the Workspaces in an EMR Studio\.     
**Outbound Rules**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-security-groups.html)