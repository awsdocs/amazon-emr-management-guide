# Establish Access and Permissions for Git\-Based Repositories<a name="emr-studio-enable-git"></a>

To let your EMR Studio users associate a Git repository with a Workspace, you should set up the following access and permissions requirements\.

**Cluster internet access**  
Amazon EMR clusters running on Amazon EC2 and Amazon EMR on EKS clusters attached to Studio Workspaces must be in a private subnet that uses a network address translation \(NAT\) gateway, or they must be able to access the internet through a virtual private gateway\. For more information, see [Amazon VPC Options](emr-clusters-in-a-vpc.md)\.  
The security groups that you use with EMR Studio must also include an outbound rule that allows Workspaces to route traffic to the internet from an attached EMR cluster\. For more information, see [Define Security Groups to Control EMR Studio Network Traffic](emr-studio-security-groups.md)\.

**Permissions for AWS Secrets Manager**  
To let EMR Studio users access Git repositories with secrets stored in AWS Secrets Manager, add a permissions policy to the [service role for EMR Studio](emr-studio-service-role.md) that allows the `secretsmanager:GetSecretValue` action\.

For information about how to link Git\-based repositories to Workspaces, see [Link Git\-Based Repositories to an EMR Studio Workspace](emr-studio-git-repo.md)\.