# Configure EMR Security Features<a name="emr-lf-security"></a>

To ensure that Amazon EMR is securely integrated with AWS Lake Formation, configure the following EMR security features:
+ Enable Kerberos authentication using the cluster\-dedicated KDC\. For more information, see [Use Kerberos Authentication](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-kerberos.html)\.
+ Configure your Amazon EC2 security group or Amazon VPC network access control list \(ACL\) to allow access to the proxy agent \(port 8442\) from your user's desktops\. For more information, see [Control Network Traffic with Security Groups](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-security-groups.html)\.
+ \(Optional\) Enable encryption in transit or at rest\. For more information, see [Encryption Options](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-data-encryption-options.html) in the *Amazon EMR Management Guide*\. 
+ \(Optional\) Create a custom Transport Layer Security \(TLS\) key pair for the proxy agent\. For more information, see [Customize Proxy Agent Certificate](emr-lf-TLS.md)\.

For more information, see [Security in Amazon EMR](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-security.html)\.