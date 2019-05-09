# Use Security Configurations to Set Up Cluster Security<a name="emr-security-configurations"></a>

With Amazon EMR release version 4\.8\.0 or later, you can use security configurations to configure data encryption, Kerberos authentication \(available in release version 5\.10\.0 and later\), and Amazon S3 authorization for EMRFS \(available in release version 5\.10\.0 or later\)\.

After you create a security configuration, you specify it when you create a cluster, and you can re\-use it for any number of clusters\.

You can use the console, the AWS Command Line Interface \(AWS CLI\), or the AWS SDKs to create security configurations\. You can also use an AWS CloudFormation template to create a security configuration\. For more information, see [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/) and the template reference for [AWS::EMR::SecurityConfiguration](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-emr-securityconfiguration.html#cfn-emr-securityconfiguration-securityconfiguration)\.

**Topics**
+ [Create a Security Configuration](emr-create-security-configuration.md)
+ [Specify a Security Configuration for a Cluster](emr-specify-security-configuration.md)