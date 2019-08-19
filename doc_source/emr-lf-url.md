# Update the callback or single sign\-on URL in IdP<a name="emr-lf-url"></a>

1. Locate the public IP address of the master node and the master instance ID in your cluster by using the console or CLI\.

1. Set up a callback URL in your IdP account:
   + When using AD FS as your IDP, complete the following steps:

     1. From the AD FS Management Console, go to **Relying Party Trusts**\.

     1. Right\-click the display name of your replying party trust, and choose **Properties**\.

     1. In the **Properties** window, choose the **Endpoints** tab\. 

     1. Select the temporary URL that you provided previously, then choose **edit**\. 

     1. In the **Edit Endpoint** window, update the Trusted URL with the correct DNS name for your master node\. 

     1. In the **Add an Endpoint** window, fill in the **Trusted URL** box with your master node public DNS\. For example, 

        ```
        https://ec2-11-111-11-111.compute-1.amazonaws.com:8442/gateway/knoxsso/api/v1/websso?pac4jCallback=true&client_name=SAML2Client
        ```

     1. Choose **OK**\.
   + When using Auth0 as your IdP, complete the following steps: 

     1. Go to https://auth0\.com/ and log in\.

     1. In the left panel, choose **Applications**\. 

     1. Select your previously created application\. 

     1. On the **Settings** tab, update **Allowed Callback URLs** with your master node public DNS\. 
   + When using Okta as your IdP, complete the following steps:

     1. Go to https://developer\.okta\.com/ and log in\.

     1. In the top right corner, choose **Admin**, then choose the **Applications** tab\. 

     1. Select your application name\.

     1. On the **General** tab under your application name, choose **SAML Settings**, then choose **Edit**\. 

     1. On the **Configure SAML** tab, update **Single\-sign on URL** with your master node public DNS\.