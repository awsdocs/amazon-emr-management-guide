# Before You Begin<a name="emr-lf-prerequisites"></a>

Before you launch an Amazon EMR cluster with AWS Lake Formation, complete the following tasks: 

1. Set up a trust relationship between your identity provider and AWS to enable SAML 2\.0\-based federation, and create IAM roles for Lake Formation\. For instructions, see [Configure a Trust Relationship Between your IdP and Lake Formation](emr-lf-federation.md)\.

1. Create a new Amazon EC2 instance profile\. For instructions, see [Create a Customized EC2 Instance Profile](emr-lf-create-instance-profile.md)\.

1. Configure Amazon EMR security features\. For instructions, see [Configure EMR Security](emr-lf-security.md)\.

You should also complete the following AWS Lake Formation tasks covered in the *AWS Lake Formation Developer Guide*:

1. Allow data filtering for data lakes on Amazon EMR by opting in\. You may opt in before or after launching an Amazon EMR cluster with Lake Formation, but you must explicitly allow data filtering before Amazon EMR can access data in Amazon S3 locations registered with Lake Formation\. For more information and instructions, see [Allow Data Filtering on Amazon EMR](https://docs.aws.amazon.com/lake-formation/latest/dg/getting-started-setup.html#emr-switch) in the *Lake Formation Developer Guide*\.

1. Create a user\-defined service role for Lake Formation to register data locations that will be accessed by Amazon EMR\. For instructions, see [Requirements for Roles Used to Register Locations](https://docs.aws.amazon.com/lake-formation/latest/dg/registration-role.html)\.
**Warning**  
You must use a user\-defined role and not the [Lake Formation service\-linked role](https://docs.aws.amazon.com/lake-formation/latest/dg/service-linked-roles.html) when you register data locations\. Lake Formation does not support using its service\-linked role when you integrate with EMR\.

1. Set up and control user access to resources through Lake Formation policies in the AWS Lake Formation console\. For more information, see [Lake Formation Permissions](https://docs.aws.amazon.com/lake-formation/latest/dg/lake-formation-permissions.html)\. 

**Topics**
+ [Configure a Trust Relationship Between your IdP and Lake Formation](emr-lf-federation.md)
+ [Configure Third\-Party Providers for SAML](emr-lf-idp.md)
+ [Create a Customized EC2 Instance Profile](emr-lf-create-instance-profile.md)
+ [Configure EMR Security](emr-lf-security.md)