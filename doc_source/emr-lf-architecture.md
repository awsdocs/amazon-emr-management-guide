# Architecture of SAML\-Enabled Single Sign\-On and Fine\-Grained Access Control<a name="emr-lf-architecture"></a>

The following diagram illustrates the architecture of SAML\-enabled single sign\-on and fine\-grained access control with Lake Formation and Amazon EMR\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/EMR-saml-based-federation.png)

1. An unauthenticated user uses the proxy agent to access EMR notebook or Zeppelin\. The user is redirected to your organization’s Identity Provider \(IdP\) sign\-on page\. 

1. The IdP verifies the user's identity in your organization\.

1. The IdP generates a SAML authentication response that includes assertions that identify the user and include attributes about the user\. 

1. The client browser posts the SAML assertion to the proxy agent\.

1. The proxy agent requests user\-specific temporary security credentials from AWS Lake Formation on behalf of the user\. The temporary security credentials are sent back to the proxy agent\. 

1. The proxy agent stores the user\-specific temporary security credentials in the secret agent\. The secret agent sends the temporary user credentials to the secret agents in core and task nodes\. 

1. The proxy agent enables successful user login\.

1. When the user runs a Spark job by using the EMR notebook or Zeppelin, the record server calls the secret agent to obtain temporary user credentials\. 

1. The record server reads and filters data from Amazon S3 based on the policies defined in Lake Formation\. 

From the user's perspective, this process happens transparently\. The user starts at your organization's authentication page and ends up at the EMR notebook or Zeppelin through the browser without ever having to supply any AWS credentials\.