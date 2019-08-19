# Use IAM Roles with Applications That Call AWS Services Directly<a name="emr-iam-roles-calling"></a>

Applications running on the EC2 instances of a cluster can use the EC2 instance profile to obtain temporary security credentials when calling AWS services\.

The versions of Hadoop available with Amazon EMR release 2\.3\.0 and later have already been updated to make use of IAM roles\. If your application runs strictly on top of the Hadoop architecture, and does not directly call any service in AWS, it should work with IAM roles with no modification\.

If your application calls services in AWS directly, you need to update it to take advantage of IAM roles\. This means that instead of obtaining account credentials from `/etc/hadoop/conf/core-site.xml` on the EC2 instances in the cluster, your application uses an SDK to access the resources using IAM roles, or calls the EC2 instance metadata to obtain the temporary credentials\.

**To access AWS resources with IAM roles using an SDK**
+ The following topics show how to use several of the AWS SDKs to access temporary credentials using IAM roles\. Each topic starts with a version of an application that does not use IAM roles and then walks you through the process of converting that application to use IAM roles\. 
  +  [Using IAM Roles for Amazon EC2 Instances with the SDK for Java](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/java-dg-roles.html) in the *AWS SDK for Java Developer Guide* 
  +  [Using IAM Roles for Amazon EC2 Instances with the SDK for \.NET](https://docs.aws.amazon.com/sdk-for-net/latest/developer-guide/net-dg-roles.html) in the *AWS SDK for \.NET Developer Guide* 
  +  [Using IAM Roles for Amazon EC2 Instances with the SDK for PHP](https://docs.aws.amazon.com/aws-sdk-php/guide/latest/php-dg-roles.html) in the *AWS SDK for PHP Developer Guide* 
  +  [Using IAM Roles for Amazon EC2 Instances with the SDK for Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/developer-guide/ruby-dg-roles.html) in the *AWS SDK for Ruby Developer Guide* 

**To obtain temporary credentials from EC2 instance metadata**
+ Call the following URL from an EC2 instance that is running with the specified IAM role, which returns the associated temporary security credentials \(AccessKeyId, SecretAccessKey, SessionToken, and Expiration\)\. The following example uses the default instance profile for Amazon EMR, `EMR_EC2_DefaultRole`\. 

  ```
  GET http://169.254.169.254/latest/meta-data/iam/security-credentials/EMR_EC2_DefaultRole
  ```

For more information about writing applications that use IAM roles, see [Granting Applications that Run on Amazon EC2 Instances Access to AWS Resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/role-usecase-ec2app.html)\.

For more information about temporary security credentials, see [Using Temporary Security Credentials](https://docs.aws.amazon.com/STS/latest/UsingSTS/using-temp-creds.html) in the *Using Temporary Security Credentials* guide\. 