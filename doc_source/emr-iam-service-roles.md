# IAM Service Roles Used By Amazon EMR<a name="emr-iam-service-roles"></a>

Amazon EMR uses IAM service roles to perform actions on your behalf when provisioning cluster resources, running applications, dynamically scaling resources, and creating and running EMR Notebooks\. Amazon EMR uses the following roles when interacting with other AWS services\. Each role has a unique function within Amazon EMR\. The topics in this section describe the role function and provide the default roles and permissions policy for each role\.

If you have application code on your cluster that calls AWS services directly, you may need to use the SDK to specify roles\. For more information, see [Use IAM Roles with Applications That Call AWS Services Directly](emr-iam-roles-calling.md)\.

**Topics**
+ [Service Role for Amazon EMR \(EMR Role\)](emr-iam-role.md)
+ [Service Role for Cluster EC2 Instances \(EC2 Instance Profile\)](emr-iam-role-for-ec2.md)
+ [Service Role for Automatic Scaling in EMR \(Auto Scaling Role\)](emr-iam-role-automatic-scaling.md)
+ [Service Role for EMR Notebooks](emr-managed-notebooks-service-role.md)
+ [Using the Service\-Linked Role for Amazon EMR](using-service-linked-roles.md)