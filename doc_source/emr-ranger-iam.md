# IAM roles for native integration with Apache Ranger<a name="emr-ranger-iam"></a>

The integration between Amazon EMR and Apache Ranger relies on three key roles that you should create before you launch your cluster:
+ A custom Amazon EC2 instance profile for Amazon EMR
+ An IAM role for Apache Ranger Engines
+ An IAM role for other AWS services

This section gives an overview of these roles and the policies that you need to include for each IAM role\. For information about creating these roles, see [Set Up Ranger Admin Server](emr-ranger-admin.md)\.