# EMR Studio Security and Access Control<a name="emr-studio-security"></a>

This section helps you understand and set up the security features that EMR Studio uses to interact with other AWS services\. It also covers how to use fine\-grained permissions policies to authorize Studio users and groups to create and access Workspaces and clusters, or link Git repositories\. 

The security features covered in this section do not enforce data access control\. You should configure permissions directly on the clusters that you associate with a Studio in order to manage access to input data\. For more information, see [Security in Amazon EMR](emr-security.md)\.

**Topics**
+ [Create an EMR Studio Service Role](emr-studio-service-role.md)
+ [Create an EMR Studio User Role with Session Policies](emr-studio-user-role.md)
+ [Define Security Groups to Control EMR Studio Network Traffic](emr-studio-security-groups.md)
+ [Establish Access and Permissions for Git\-Based Repositories](emr-studio-enable-git.md)