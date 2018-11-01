# Restrict Permissions to a VPC Using IAM<a name="emr-iam-on-vpc"></a>

When you launch a cluster into a VPC, you can use AWS Identity and Access Management \(IAM\) to control access to clusters and restrict actions using policies, just as you would with clusters launched into EC2\-Classic\. For more information about IAM, see [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\. 

You can also use IAM to control who can create and administer subnets\. For more information about administering policies and actions in Amazon EC2 and Amazon VPC, see [IAM Policies for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html) in the *Amazon EC2 User Guide for Linux Instances*\. 

By default, all IAM users can see all of the subnets for the account, and any user can launch a cluster in any subnet\. 

You can limit access to the ability to administer the subnet, while still allowing users to launch clusters into subnets\. To do so, create one user account that has permissions to create and configure subnets and a second user account that can launch clusters but which can’t modify Amazon VPC settings\. 