# Configure EMRFS Authorization for Data in Amazon S3<a name="emr-emrfs-authz"></a>

If you have clusters with multiple users who need different levels of access to EMRFS data in Amazon S3, you can use EMRFS authorization within a security configuration to implement fine\-grained access control\. EMRFS authorization lets you apply different permissions for access based on the user or group making the request, or the location of EMRFS data in Amazon S3\. This lets you allow or deny access permissions more easily, even for IAM users in different AWS accounts\. EMRFS authorization is available in Amazon EMR release version 5\.10\.0 and later\. If you use an earlier version of Amazon EMR or have additional authorization requirements, you can create a custom credentials provider instead\. For more information, see [Creating a Custom Credentials Provider for EMRFS Data in Amazon S3](emr-plan-credentialsprovider.md#emr-create-credentialsprovider)

## How EMRFS Authorization Works<a name="emr-how-emrfs-authz-works.title"></a>

When EMRFS issues a request to Amazon S3, the permissions to access Amazon S3 data are determined by the permissions policies attached to the IAM role for the EC2 instance profile \(the default is `EMR_EC2_DefaultRole`\)\. If a cluster doesn't use EMRFS authorization, the IAM policies attached to this role apply regardless of the user or group making the request through EMRFS, or the location of data in Amazon S3\. When you set up EMRFS authorization, you create a security configuration and specify *role mappings*\. Each role mapping specifies an IAM role along with *identifiers*\. The identifiers can be users, groups, or Amazon S3 prefixes that indicate a data location\. The users and groups in a role mapping are Hadoop users and groups that are defined on the cluster\. Users and groups are passed to EMRFS in the context of the application using it \(for example, YARN user impersonation\)\. When EMRFS makes a request to Amazon S3 from a cluster that uses the security configuration, if the request contains an identifier specified in a role mapping, the Amazon EC2 instance profile assumes the IAM role, using it for that request so that the IAM permissions attached to that role apply\.

+ For more information about creating and specifying a security configuration for a cluster, see [Use Security Configurations to Set Up Cluster Security](emr-security-configurations.md)\.

+ For more information about EMRFS, see [Using EMR File System \(EMRFS\)](emr-fs.md)\.

## Set Up EMRFS Authorization<a name="emr-set-up-emrfs-authz"></a>

Before you set up EMRFS authorization, plan and create the roles and permission policies to attach to the roles\. For more information, see [How Do Roles for EC2 Instances Work?](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html) in the *IAM User Guide*\. When creating permissions policies, we recommend that you start with the managed policy attached to the default role for the instance profile, which is `AmazonElasticMapReduceforEC2Role`, and edit this policy according to your requirements\. For more information, see [Use Default IAM Roles and Managed Policies](emr-iam-roles-defaultroles.md)\. If a role allows access to a location in Amazon S3 that is encrypted using an AWS Key Management Service customer master key \(CMK\), make sure that the role is specified as a key user\. This gives the role permission to use the CMK\. For more information, see [Using Key Policies](http://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-default-allow-users) in the *AWS Key Management Service Developer Guide*\.

You can create and use multiple roles, and you can specify multiple identifiers and multiple role mappings within a security configuration\. You can also specify multiple identifiers within a single role mapping, but they must all be of the same type\. When EMRFS makes a request to Amazon S3, role mappings are evaluated in the top\-down order that they appear in the security configuration\. The EC2 instance profile assumes the role that is specified for the first identifier that matches the request\. If an identifier isn't found in any role mapping, the EC2 instance profile assumes the role that you specified when you created the cluster\. For this reason, we recommend that the policies attached to this role limit permissions to Amazon S3\.

The following is an example JSON snippet for EMRFS authorization within a security configuration\. It demonstrates role mappings for the three different identifier types, followed by a parameter reference\. 

```
{
  "AuthorizationConfiguration": {
    "EmrFsConfiguration": {
      "RoleMappings": [{
        "Role": "arn:aws:iam::123456789101:role/allow_EMRFS_access_for_user1",
        "IdentifierType": "User",
        "Identifiers": [ "user1" ]
      },{
        "Role": "arn:aws:iam::123456789101:role/allow_EMRFS_access_to_MyBuckets",
        "IdentifierType": "Prefix",
        "Identifiers": [ "s3://MyBucket/","s3://MyOtherBucket/" ]
      },{
        "Role": "arn:aws:iam::123456789101:role/allow_EMRFS_access_for_AdminGroup",
        "IdentifierType": "Group",
        "Identifiers": [ "AdminGroup" ]
      }]
    }
  }
}
```


| Parameter | Description | 
| --- | --- | 
|  `"AuthorizationConfiguration":`  |  Required for EMRFS authorization\. Contains authorization configurations\.  | 
|   `"EmrFsConfiguration":`  |  Required for EMRFS authorization\. Contains EMRFS authorization configurations\.  | 
|    `"RoleMappings":`  |  Required for EMRFS authorization\. Contains one or more role mapping definitions\. Role mappings are evaluated in the top\-down order that they appear\. If a role mapping evaluates as true for an EMRFS call for data in Amazon S3, no further role mappings are evaluated\. Role mappings consist of the following required parameters: | 
|    `"Role":` | Specifies the ARN identifier of an IAM role in the format `arn:aws:iam::account-id:role/role-name`\. This is the IAM role that Amazon EMR assumes if the EMRFS request to Amazon S3 matches any of the `Identifiers` specified\. | 
|    `"IdentifierType":` | Can be one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-emrfs-authz.html)  | 
|     `"Identifiers":`  |  Specifies one or more identifiers of the appropriate identifier type\. Separate multiple identifiers by commas with no spaces\.  | 