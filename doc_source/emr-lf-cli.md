# Launch an Amazon EMR cluster integrated with Lake Formation using the CLI<a name="emr-lf-cli"></a>

The following procedure demonstrates how to launch an Amazon EMR cluster with Zeppelin integrated with AWS Lake Formation\. 

1. Create an `security-configuration.json` file for security configuration with the following content\.
   + Specify the whole path to the IdP metadata file uploaded in S3\.
   + Replace *account\-id* with your AWS account ID\.
   + Specify a value for `TicketLifetimeInHours` to determine the period for which a Kerberos ticket issued by the KDC is valid\. 

   ```
   {
       "LakeFormationConfiguration": {
           "IdpMetadataS3Path": "s3://mybucket/myfolder/idpmetadata.xml",
           "EmrRoleForUsersARN": "arn:aws:iam::account-id:role/IAM_Role_For_AWS_Services",
           "LakeFormationRoleForSAMLPrincipalARN": "arn:aws:iam::account-id:role/IAM_Role_For_Lake_Formation"
       },
       "AuthenticationConfiguration": {
           "KerberosConfiguration": {
               "Provider": "ClusterDedicatedKdc",
               "ClusterDedicatedKdcConfiguration": {
                   "TicketLifetimeInHours": 24
               }
           }
       }
   }
   ```

1. Run the following command to create a security configuration\. 

   ```
   aws emr create-security-configuration \
   --security-configuration file://./security-configuration.json \
   --name security-configuration
   ```

1. Create a `configurations.json` file that configures the Hive Metastore\. 

   ```
   [
       {
           "Classification": "spark-hive-site",
           "Properties": {
               "hive.metastore.glue.catalogid": "account-id"
           }
       }
   ]
   ```

1. Run the following command to launch an Amazon EMR cluster\. 
   + Replace *subnet\-00xxxxxxxxxxxxx11* with your subnet ID\.
   + Replace the *EC2\_KEY\_PAIR* with the name of your EC2 key pair for this cluster\. EC2 key pair is optional and only required if you want to use SSH to access your cluster\.
   + Replace *cluster\-name* with the name of your cluster\.

   ```
   aws emr create-cluster --region us-east-1 \
   --release-label emr-5.26.0 \
   --use-default-roles \
   --instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m4.xlarge \
   InstanceGroupType=CORE,InstanceCount=1,InstanceType=m4.xlarge \
   --applications Name=Zeppelin Name=Livy \
   --kerberos-attributes Realm=EC2.INTERNAL,KdcAdminPassword=MyClusterKDCAdminPassword \
   --configurations file://confugurations.json \
   --ec2-attributes KeyName=EC2_KEY_PAIR,SubnetId=subnet-00xxxxxxxxxxxxx11 \
   --security-configuration security-configuration \
   --name cluster-name
   ```