# Configure IAM Roles for EMRFS Requests to Amazon S3<a name="emr-emrfs-iam-roles"></a>

When an application running on an Amazon EMR cluster references data using the `s3://mydata` format, Amazon EMR uses EMRFS to make the request\. To interact with Amazon S3, EMRFS assumes the permissions policies attached to the [Service Role for Cluster EC2 Instances \(EC2 Instance Profile\)](emr-iam-role-for-ec2.md) specified when the cluster was created\. The same service role for cluster EC2 instances is used regardless of the user or group using the application or the location of the data in Amazon S3\. If you have clusters with multiple users who need different levels of access to data in Amazon S3 through EMRFS, you can set up a security configuration with IAM roles for EMRFS\. EMRFS can assume a different service role for cluster EC2 instances based on the user or group making the request, or based on the location of data in Amazon S3\. Each IAM role for EMRFS can have different permissions for data access in Amazon S3\.

IAM roles for EMRFS are available with Amazon EMR release version 5\.10\.0 and later\. If you use an earlier release version or have requirements beyond what IAM roles for EMRFS provide, you can create a custom credentials provider instead\. For more information, see [Authorizing Access to EMRFS Data in Amazon S3](emr-plan-credentialsprovider.md)\. For more information about EMRFS, see [Use EMR File System \(EMRFS\)](emr-fs.md)\.

When you use a security configuration to specify IAM roles for EMRFS, you set up role mappings\. Each role mapping specifies an IAM role that corresponds to identifiers\. These identifiers determine the basis for access to Amazon S3 through EMRFS\. The identifiers can be users, groups, or Amazon S3 prefixes that indicate a data location\. When EMRFS makes a request to Amazon S3, if the request matches the basis for access, EMRFS has cluster EC2 instances assume the corresponding IAM role for the request\. The IAM permissions attached to that role apply instead of the IAM permissions attached to the service role for cluster EC2 instances\.

The users and groups in a role mapping are Hadoop users and groups that are defined on the cluster\. Users and groups are passed to EMRFS in the context of the application using it \(for example, YARN user impersonation\)\. The Amazon S3 prefix can be a bucket specifier of any depth \(for example, `s3://mybucket` or `s3://mybucket/myproject/mydata`\)\. You can specify multiple identifiers within a single role mapping, but they all must be of the same type\.

**Important**  
IAM roles for EMRFS provide application\-level isolation between users of the application\. It does not provide host level isolation between users on the host\. Any user with access to the cluster can bypass the isolation to assume any of the roles\.

When a cluster application makes a request to Amazon S3 through EMRFS, EMRFS evaluates role mappings in the top\-down order that they appear in the security configuration\. If a request made through EMRFS doesn’t match any identifier, EMRFS falls back to using the service role for cluster EC2 instances\. For this reason, we recommend that the policies attached to this role limit permissions to Amazon S3\. For more information, see [Service Role for Cluster EC2 Instances \(EC2 Instance Profile\)](emr-iam-role-for-ec2.md)\.

## Configure Roles<a name="emr-emrfs-iam-roles-role-configuration"></a>

Before you set up a security configuration with IAM roles for EMRFS, plan and create the roles and permission policies to attach to the roles\. For more information, see [How Do Roles for EC2 Instances Work?](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html) in the *IAM User Guide*\. When creating permissions policies, we recommend that you start with the managed policy attached to the default EMR role for EC2, and then edit this policy according to your requirements\. The default role is `EMR_EC2_DefaultRole`, and the default managed policy to edit is `AmazonElasticMapReduceforEC2Role`\. For more information, see [Service Role for Cluster EC2 Instances \(EC2 Instance Profile\)](emr-iam-role-for-ec2.md)\.

### Updating Trust Policies for Assume Role Permissions<a name="emr-emrfs-iam-role-trust-policy"></a>

Each role that EMRFS uses must have a trust policy that allows the cluster's EMR role for EC2 to assume it\. Similarly, the cluster's EMR role for EC2 must have a trust policy that allows EMRFS roles to assume it\.

The following example trust policy is attached to roles for EMRFS\. The statement allows the default EMR role for EC2 to assume the role\. For example, if you have two fictitious EMRFS roles, `EMRFSRole_First` and `EMRFSRole_Second`, this policy statement is added to the trust policies for each of them\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Principal":{
            "AWS":"arn:aws:iam::AWSAcctID:role/EMR_EC2_DefaultRole"
         },
         "Action":"sts:AssumeRole"
      }
   ]
}
```

In addition, the following example trust policy statement is added to the `EMR_EC2_DefaultRole` to allow the two fictitious EMRFS roles to assume it\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Principal":{
            "AWS": ["arn:aws:iam::AWSAcctID:role/EMRFSRole_First", "arn:aws:iam::AWSAcctID:role/EMRFSRole_Second"]
         },
         "Action":"sts:AssumeRole"
      }
   ]
}
```

**To update the trust policy of an IAM role**

Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Roles**, enter the name of the role in **Search**, and then select its **Role name**\.

1. Choose **Trust relationships**, **Edit trust relationship**\.

1. Add a trust statement according to the **Policy Document** according to the guidelines above, and then choose **Update Trust Policy**\.

### Specifying a Role as a Key User<a name="emr-emrfs-iam-role-key-user"></a>

If a role allows access to a location in Amazon S3 that is encrypted using an AWS Key Management Service customer master key \(CMK\), make sure that the role is specified as a key user\. This gives the role permission to use the CMK\. For more information, see [Using Key Policies](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-default-allow-users) in the *AWS Key Management Service Developer Guide*\.

## Set Up a Security Configuration with IAM Roles for EMRFS<a name="emr-emrfs-iam-roles-setup"></a>

**Important**  
If none of the IAM roles for EMRFS that you specify apply, EMRFS falls back to the EMR role for EC2\. Consider customizing this role to restrict permissions to Amazon S3 as appropriate for your application and then specifying this custom role instead of `EMR_EC2_DefaultRole` when you create a cluster\. For more information, see [Customize IAM Roles](emr-iam-roles-custom.md) and [Specify Custom IAM Roles When You Create a Cluster](emr-iam-roles-custom.md#emr-iam-roles-launch-jobflow)\.

**To specify IAM roles for EMRFS requests to Amazon S3 using the console**

1. Create a security configuration that specifies role mappings:

   1. In the Amazon EMR console, select **Security configurations**, **Create**\.

   1. Type a **Name** for the security configuration\. You use this name to specify the security configuration when you create a cluster\.

   1. Choose **Use IAM roles for EMRFS requests to Amazon S3**\.

   1. Select an **IAM role** to apply, and under **Basis for access** select an identifier type \(**Users**, **Groups**, or **S3 prefixes**\) from the list and enter corresponding identifiers\. If you use multiple identifiers, separate them with a comma and no space\. For more information about each identifier type, see the [JSON configuration reference](#emrfs-seccfg-json) below\.

   1. Choose **Add role** to set up additional role mappings as described in the previous step\.

   1. Set up other security configuration options as appropriate and choose **Create**\. For more information, see [Create a Security Configuration](emr-create-security-configuration.md)\.

1. Specify the security configuration you created above when you create a cluster\. For more information, see [Specify a Security Configuration for a Cluster](emr-specify-security-configuration.md)\.

**To specify IAM roles for EMRFS requests to Amazon S3 using the AWS CLI**

1. Use the `aws emr create-security-configuration` command, specifying a name for the security configuration, and the security configuration details in JSON format\.

   The example command shown below creates a security configuration with the name `EMRFS_Roles_Security_Configuration`\. It is based on a JSON structure in the file `MyEmrfsSecConfig.json`, which is saved in the same directory where the command is executed\.

   ```
   aws emr create-security-configuration --name EMRFS_Roles_Security_Configuration --security-configuration file://MyEmrFsSecConfig.json.
   ```

   Use the following guidelines for the structure of the `MyEmrFsSecConfig.json` file\. You can specify this structure along with structures for other security configuration options\. For more information, see [Create a Security Configuration](emr-create-security-configuration.md)\.

   The following is an example JSON snippet for specifying custom IAM roles for EMRFS within a security configuration\. It demonstrates role mappings for the three different identifier types, followed by a parameter reference\. 

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
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-emrfs-iam-roles.html)

1. Use the `aws emr create-cluster` command to create a cluster and specify the security configuration you created in the previous step\. 

   The following example creates a cluster with default core Hadoop applications installed\. The cluster uses the security configuration created above as `EMRFS_Roles_Security_Configuration` and also uses a custom EMR role for EC2, `EC2_Role_EMR_Restrict_S3`, which is specified using the `InstanceProfile` argument of the `--ec2-attributes` parameter\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

   ```
   aws emr create-cluster --name MyEmrFsS3RolesCluster \
   --release-label emr-5.29.0 --ec2-attributes InstanceProfile=EC2_Role_EMR_Restrict_S3,KeyName=MyKey \
   --instance-type m5.xlarge --instance-count 3 \
   --security-configuration EMRFS_Roles_Security_Configuration
   ```