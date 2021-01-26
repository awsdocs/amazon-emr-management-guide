# Prerequisites and Considerations<a name="emr-managed-notebooks-git-considerations"></a>

Consider the following when planning to integrate a Git\-based repository with EMR Notebooks\.

## AWS CodeCommit<a name="code-commit-considerations"></a>

If you use a CodeCommit repository, you must use Git credentials and HTTPS with CodeCommit\. SSH Keys, and HTTPS with the AWS CLI credential helper are not supported\. CodeCommit does not support personal access tokens \(PATs\)\. For more information, see [Using IAM with CodeCommit: Git Credentials, SSH Keys, and AWS Access Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_ssh-keys.html) in the *IAM User Guide* and [Setup for HTTPS Users Using Git Credentials](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html) in the *AWS CodeCommit User Guide*\.

## Access and Permission Considerations<a name="access-considerations"></a>

Before associating a repository with your notebook, make sure that your cluster, IAM role for EMR Notebooks, and security groups have the correct settings and permissions\.
+ **Access to internet required** – The network interface that is launched has only a private IP address\. This means that the cluster that your notebook connects to must be in a private subnet with a network address translation \(NAT\) gateway or must be able to access the internet through a virtual private gateway\. For more information, see [Amazon VPC Options](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-clusters-in-a-vpc.html)\.

  The security groups for your notebook must include an outbound rule that allows the notebook to route traffic to the internet from the cluster\. We recommend that you create your own security groups\. For more information, see [Specifying EC2 Security Groups for EMR Notebooks](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks-security-groups.html)\.
**Important**  
If the network interface is launched into a public subnet, it won’t be able to communicate with the internet through an internet gateway \(IGW\)\.
+ **Permissions for AWS Secrets Manager** – If you use Secrets Manager to store secrets that you use to access a repository, the [Service Role for EMR Notebooks](emr-managed-notebooks-service-role.md) must have a permissions policy attached that allows the `secretsmanager:GetSecretValue` action\.