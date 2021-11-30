# Create an EMR Studio<a name="emr-studio-create-studio"></a>

You can create an EMR Studio for your team using the Amazon EMR console or the AWS CLI\. Creating a Studio instance is part of setting up Amazon EMR Studio\.

**Prerequisites**

Before you create a Studio, make sure you've completed the previous tasks in [Set up an EMR Studio](emr-studio-set-up.md)\.

To create a Studio using the AWS CLI, you should have the latest version installed\. For more information, see [Installing or updating the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)\.

**Important**  
Deactivate proxy management tools such as FoxyProxy or SwitchyOmega in the browser before you create a Studio\. Active proxies can result in a **Network Failure ** error message when you choose **Create Studio**\.

------
#### [ Console ]

**To create an EMR Studio using the EMR console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/home](https://console.aws.amazon.com/elasticmapreduce/home)

1. Choose **EMR Studio** from the left navigation\.

1. Choose **Create Studio** to open the **Create a Studio** page\.

1. Enter a **Studio name** and an optional **Description**\.

1. If you use IAM authentication for the Studio, you can choose **Add new tag** to add one or more key\-value tags of your choice to the Studio\. You use tags to give specified users access to the Studio\. For more information, see [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.

   You can also add tags to help you manage, identify, organize, and filter Studios\. For more information, see [Tagging AWS resources](https://docs.aws.amazon.com/general/latest/gr/aws_tagging.html)\.

1. Under **Networking**, choose an Amazon Virtual Private Cloud \(**VPC**\) for the Studio from the dropdown list\.

1. Under **Subnets**, select a maximum of five subnets in your VPC to associate with the Studio\. You have the option to add more subnets after you create the Studio\.

1. For **Security groups**, choose either the default security groups or custom security groups\. For more information, see [Define security groups to control EMR Studio network traffic](emr-studio-security-groups.md)\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-create-studio.html)

1. Under **Authentication**, choose an authentication mode for the Studio and provide information according to the following table\. To learn more about authentication for EMR Studio, see [Choose an authentication mode for Amazon EMR Studio](emr-studio-authentication.md)\.  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-create-studio.html)

1. Under **Workspace storage**, choose **Browse S3** to select your Amazon S3 bucket for backing up Workspaces and notebook files\.
**Note**  
Your EMR Studio service role must have read and write access to the bucket that you select\.

1. Choose **Create Studio** to finish and navigate to the **Studios** page\. Your new Studio appears in the list with details such as **Studio name**, **Creation date**, and **Studio access URL**\.

After you create a Studio, follow the instructions in [Assign a user or group to an EMR Studio](emr-studio-manage-users.md#emr-studio-assign-users-groups)\.

------
#### [ CLI ]

**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

**Example CLI command to create an EMR Studio with IAM authentication mode**  
The following example AWS CLI command creates an EMR Studio with IAM authentication mode\. When you use IAM authentication or federation for the Studio, you don't specify a `--user-role`\.   
To let federated users log in using the Studio URL and credentials for your identity provider \(IdP\), specify your `--idp-auth-url` and `--idp-relay-state-parameter-name`\. For a list of IdP authentication URLs and RelayState names, see [Identity provider RelayState parameters and authentication URLs](#emr-studio-idp-reference-table)\.  

```
aws emr create-studio \
--name <example-studio-name> \
--auth-mode IAM \
--vpc-id <example-vpc-id> \
--subnet-ids <subnet-id-1> <subnet-id-2>... <subnet-id-5>  \
--service-role <example-studio-service-role-name> \
--workspace-security-group-id <example-workspace-sg-id> \
--engine-security-group-id <example-engine-sg-id> \
--default-s3-location <example-s3-location> \
--idp-auth-url <https://EXAMPLE/login/> \
--idp-relay-state-parameter-name <example-RelayState>
```

**Example CLI command to create an EMR Studio with AWS SSO authentication mode**  
The following AWS CLI example command creates an EMR Studio that uses AWS SSO authentication mode\. When you use AWS SSO authentication, you must specify a `--user-role`\.   
For more information about AWS SSO authentication mode, see [Set up SSO authentication mode for Amazon EMR Studio](emr-studio-authentication.md#emr-studio-enable-sso)\.  

```
aws emr create-studio \
--name <example-studio-name> \
--auth-mode SSO \
--vpc-id <example-vpc-id> \
--subnet-ids <subnet-id-1> <subnet-id-2>... <subnet-id-5>  \
--service-role <example-studio-service-role-name> \
--user-role <example-studio-user-role-name> \
--workspace-security-group-id <example-workspace-sg-id> \
--engine-security-group-id <example-engine-sg-id> \
--default-s3-location <example-s3-location>
```

**Example CLI output for `aws emr create-studio`**  
The following is an example of the output that appears after you create a Studio\.  

```
{
    StudioId: "es-123XXXXXXXXX",
    Url: "https://es-123XXXXXXXXX.emrstudio-prod.us-east-1.amazonaws.com"
}
```

For more information about the `create-studio` command, see [https://docs.aws.amazon.com/cli/latest/reference/emr/create-studio.html](https://docs.aws.amazon.com/cli/latest/reference/emr/create-studio.html)\.

------

## Identity provider RelayState parameters and authentication URLs<a name="emr-studio-idp-reference-table"></a>

When you use IAM federation, and you want users to log in using your Studio URL and credentials for your identity provider \(IdP\), you can specify your **Identity provider \(IdP\) login URL** and **RelayState** parameter name when you [Create an EMR Studio](#emr-studio-create-studio)\.

The following table shows the standard authentication URL and RelayState parameter name for some popular identity providers\.


| Identity provider | Parameter | Authentication URL | 
| --- | --- | --- | 
| Auth0 | RelayState | https://<sub\_domain>\.auth0\.com/samlp/<app\_id> | 
| Google accounts | RelayState | https://accounts\.google\.com/o/saml2/initsso?idpid=<idp\_id>&spid=<sp\_id>&forceauthn=false | 
| Microsoft Azure | RelayState | https://myapps\.microsoft\.com/signin/<app\_name>/<app\_id>?tenantId=<tenant\_id> | 
| Okta | RelayState | https://<sub\_domain>\.okta\.com/app/<app\_name>/<app\_id>/sso/saml | 
| PingFederate | TargetResource | https://<host>/idp/<idp\_id>/startSSO\.ping?PartnerSpId=<sp\_id> | 
| PingOne | TargetResource | https://sso\.connect\.pingidentity\.com/sso/sp/initsso?saasid=<app\_id>&idpid=<idp\_id> | 