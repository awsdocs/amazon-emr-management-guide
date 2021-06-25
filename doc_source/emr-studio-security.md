# EMR Studio security and access control<a name="emr-studio-security"></a>

This section helps you understand and set up the security features that EMR Studio uses to interact with other AWS services\. It also covers how to use fine\-grained IAM permissions policies to authorize Studio users and groups to create and access Workspaces and clusters, or link Git repositories\. 

The security features covered in this section do not enforce data access control\. You should configure permissions directly on the clusters that you associate with a Studio in order to manage access to input datasets\. For more information, see [Security in Amazon EMR](emr-security.md)\.

**Topics**
+ [Create an EMR Studio service role](emr-studio-service-role.md)
+ [Create an EMR Studio user role with session policies](emr-studio-user-role.md)
+ [Define security groups to control EMR Studio network traffic](emr-studio-security-groups.md)
+ [Establish access and permissions for Git\-based repositories](emr-studio-enable-git.md)