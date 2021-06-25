# IAM service roles used by Amazon EMR<a name="emr-iam-service-roles"></a>

Amazon EMR uses IAM service roles to perform actions on your behalf when provisioning cluster resources, running applications, dynamically scaling resources, and creating and running EMR Notebooks\. Amazon EMR uses the following roles when interacting with other AWS services\. Each role has a unique function within Amazon EMR\. The topics in this section describe the role function and provide the default roles and permissions policy for each role\.

If you have application code on your cluster that calls AWS services directly, you may need to use the SDK to specify roles\. For more information, see [Use IAM roles with applications that call AWS services directly](emr-iam-roles-calling.md)\.

**Topics**
+ [Service role for Amazon EMR \(EMR role\)](emr-iam-role.md)
+ [Service role for cluster EC2 instances \(EC2 instance profile\)](emr-iam-role-for-ec2.md)
+ [Service role for automatic scaling in EMR \(Auto Scaling role\)](emr-iam-role-automatic-scaling.md)
+ [Service role for EMR Notebooks](emr-managed-notebooks-service-role.md)
+ [Using the service\-linked role for Amazon EMR](using-service-linked-roles.md)