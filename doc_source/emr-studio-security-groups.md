# Define security groups to control EMR Studio network traffic<a name="emr-studio-security-groups"></a>

## About the EMR Studio security groups<a name="emr-studio-about-security-groups"></a>

Amazon EMR Studio uses two security groups to control network traffic between Workspaces in the Studio and an attached Amazon EMR cluster running on Amazon EC2:
+ An **engine security group** that uses port 18888 to communicate with an attached Amazon EMR cluster running on Amazon EC2\.
+ A **Workspace security group** associated with the Workspaces in a Studio\. This security group includes an outbound HTTPS rule to allow the Workspace to route traffic to the internet and must allow outbound traffic to the internet on port 443 to enable linking Git repositories to a Workspace\.

EMR Studio uses these security groups in addition to any security groups associated with an EMR cluster attached to a Workspace\. 

You must create these security groups when you use the AWS CLI to create a Studio\. 

**Note**  
You can customize the security groups for EMR Studio with rules tailored to your environment, but you must include the rules noted on this page\. Your Workspace security group can't allow any inbound traffic, and the engine security group must allow inbound traffic from the Workspace security group\.

**Use the Default EMR Studio Security Groups**

When you use the EMR console, you can choose the following default security groups\. The default security groups are created by Amazon EMR Studio on your behalf, and include the minimum required inbound and outbound rules for Workspaces in an EMR Studio\. 
+ `DefaultEngineSecurityGroup`
+ `DefaultWorkspaceSecurityGroupGit` or `DefaultWorkspaceSecurityGroupWithoutGit`

## Prerequisites<a name="emr-studio-security-group-prereqs"></a>

To create the security groups for EMR Studio, you need an Amazon Virtual Private Cloud \(VPC\) for the Studio\. You choose this VPC when you create the security groups\. This should be the same VPC that you specify when you create the Studio\. If you plan to use Amazon EMR on EKS with EMR Studio, choose the VPC for your Amazon EKS cluster worker nodes\.

## Instructions<a name="emr-studio-security-group-instructions"></a>

Follow the instructions in [Creating a security group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/working-with-security-groups.html#creating-security-group) in the *Amazon EC2 User Guide for Linux Instances* to create an engine security group and a Workspace security group in your VPC\. The security groups must include the rules summarized in the following tables\.

When you create security groups for EMR Studio, note the IDs for both\. You specify each security group by ID when you create a Studio\.

**Engine security group**  
EMR Studio uses port 18888 to communicate with an attached cluster\.    
**Inbound rules**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-security-groups.html)

**Workspace security group**  
This security group is associated with the Workspaces in an EMR Studio\.     
**Outbound rules**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-security-groups.html)