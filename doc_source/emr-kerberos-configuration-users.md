# Configuring a Cluster for Kerberos\-Authenticated HDFS Users and SSH Connections<a name="emr-kerberos-configuration-users"></a>

Amazon EMR creates Kerberos\-authenticated user clients for the applications that run on the cluster—for example, the `hadoop` user, `spark` user, and others\. You can also add users who are authenticated to cluster processes using Kerberos\. Authenticated users can then connect to the cluster with their Kerberos credentials and work with applications\. For a user to authenticate to the cluster, the following configurations are required:
+ A Linux user account matching the Kerberos principal in the KDC must exist on the cluster\. Amazon EMR does this automatically in architectures that integrate with Active Directory\.
+ You must create an HDFS user directory on the master node for each user, and give the user permissions to the directory\.
+ You must configure the SSH service so that GSSAPI is enabled on the master node\. In addition, users must have an SSH client with GSSAPI enabled\.

## Adding Linux Users and Kerberos Principals to the Master Node<a name="emr-kerberos-configure-linux-kdc"></a>

If you do not use Active Directory, you must create Linux accounts on the cluster master node and add principals for these Linux users to the KDC\. This includes a principal in the KDC for the master node\. In addition to the user principals, the KDC running on the master node needs a principal for the local host\.

When your architecture includes Active Directory integration, Linux users and principals on the local KDC, if applicable, are created automatically\. You can skip this step\. For more information, see [Cross\-Realm Trust](emr-kerberos-options.md#emr-kerberos-crossrealm-summary) and [External KDC—Cluster KDC on a Different Cluster with Active Directory Cross\-Realm Trust](emr-kerberos-options.md#emr-kerberos-extkdc-ad-trust-summary)\.

**Important**  
The KDC, along with the database of principals, is lost when the master node terminates because the master node uses ephemeral storage\. If you create users for SSH connections, we recommend that you establish a cross\-realm trust with an external KDC configured for high\-availability\. Alternatively, if you create users for SSH connections using Linux user accounts, automate the account creation process using bootstrap actions and scripts so that it can be repeated when you create a new cluster\.

Submitting a step to the cluster after you create it or when you create the cluster is the easiest way to add users and KDC principals\. Alternatively, you can connect to the master node using an EC2 key pair as the default `hadoop` user to run the commands\. For more information, see [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\.

The following example submits a bash script `configureCluster.sh` to a cluster that already exists, referencing its cluster ID\. The script is saved to Amazon S3\. 

```
aws emr add-steps --cluster-id j-01234567 \
--steps Type=CUSTOM_JAR,Name=CustomJAR,ActionOnFailure=CONTINUE,\
Jar=s3://myregion.elasticmapreduce/libs/script-runner/script-runner.jar,\
Args=["s3://mybucket/configureCluster.sh"]
```

The following example demonstrates the contents of the `configureCluster.sh` script\. The script also handles creating HDFS user directories and enabling GSSAPI for SSH, which are covered in the following sections\.

```
#!/bin/bash
#Add a principal to the KDC for the master node, using the master node's returned host name
sudo kadmin.local -q "ktadd -k /etc/krb5.keytab host/`hostname -f`"
#Declare an associative array of user names and passwords to add
declare -A arr
arr=([lijuan]=pwd1 [marymajor]=pwd2 [richardroe]=pwd3)
for i in ${!arr[@]}; do
    #Assign plain language variables for clarity
     name=${i} 
     password=${arr[${i}]}

     # Create a principal for each user in the master node and require a new password on first logon
     sudo kadmin.local -q "addprinc -pw $password +needchange $name"

     #Add hdfs directory for each user
     hdfs dfs -mkdir /user/$name

     #Change owner of each user's hdfs directory to that user
     hdfs dfs -chown $name:$name /user/$name
done

# Enable GSSAPI authentication for SSH and restart SSH service
sudo sed -i 's/^.*GSSAPIAuthentication.*$/GSSAPIAuthentication yes/' /etc/ssh/sshd_config
sudo sed -i 's/^.*GSSAPICleanupCredentials.*$/GSSAPICleanupCredentials yes/' /etc/ssh/sshd_config
sudo /etc/init.d/sshd restart
```

## Adding User HDFS Directories<a name="emr-kerberos-configure-HDFS"></a>

To allow your users to log in to the cluster to run Hadoop jobs, you must add HDFS user directories for their Linux user accounts, and grant each user ownership of their directory\.

Submitting a step to the cluster after you create it or when you create the cluster is the easiest way to create HDFS directories\. Alternatively, you could connect to the master node using an EC2 key pair as the default `hadoop` user to run the commands\. For more information, see [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\.

The following example submits a bash script `AddHDFSUsers.sh` to a cluster that already exists, referencing its cluster ID\. The script is saved to Amazon S3\. 

```
aws emr add-steps --cluster-id ClusterID \
--steps Type=CUSTOM_JAR,Name=CustomJAR,ActionOnFailure=CONTINUE,\
Jar=s3://MyRegion.elasticmapreduce/libs/script-runner/script-runner.jar,Args=["s3://MyBucketPath/AddHDFSUsers.sh"]
```

The following example demonstrates the contents of the `AddHDFSUsers.sh` script\.

```
#!/bin/bash
# AddHDFSUsers.sh script

# Initialize an array of user names from AD, or Linux users created manually on the cluster
ADUSERS=("lijuan" "marymajor" "richardroe" "myusername")

# For each user listed, create an HDFS user directory
# and change ownership to the user

for username in ${ADUSERS[@]}; do
     hdfs dfs -mkdir /user/$username
     hdfs dfs -chown $username:$username /user/$username
done
```

## Enabling GSSAPI for SSH<a name="emr-kerberos-ssh-config"></a>

For Kerberos\-authenticated users to connect to the master node using SSH, the SSH service must have GSSAPI authentication enabled\. To enable GSSAPI, run the following commands from the master node command line or use a step to run it as a script\. After reconfiguring SSH, you must restart the service\.

```
sudo sed -i 's/^.*GSSAPIAuthentication.*$/GSSAPIAuthentication yes/' /etc/ssh/sshd_config
sudo sed -i 's/^.*GSSAPICleanupCredentials.*$/GSSAPICleanupCredentials yes/' /etc/ssh/sshd_config
sudo /etc/init.d/sshd restart
```