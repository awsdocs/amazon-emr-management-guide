# AWS GovCloud \(US\-West\) errors<a name="emr-troubleshoot-error-govcloud"></a>

The AWS GovCloud \(US\-West\) region differs from other regions in its security, configuration, and default settings\. As a result, use the following checklist to troubleshoot Amazon EMR errors that are specific to the AWS GovCloud \(US\-West\) region before using more general troubleshooting recommendations\.
+ Verify that your IAM roles are correctly configured\. For more information, see [Configure IAM service roles for Amazon EMR permissions to AWS services and resources](emr-iam-roles.md)\.
+ Ensure that your VPC configuration has correctly configured DNS resolution/hostname support, Internet Gateway, and DHCP Option Set parameters\. For more information, see [VPC errors](emr-troubleshoot-error-vpc.md)\.

If these steps do not solve the problem, continue with the steps for troubleshooting common Amazon EMR errors\. For more information, see [Common errors in Amazon EMR](emr-troubleshoot-errors.md)\. 