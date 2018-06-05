# Configure a Cluster\-Dedicated KDC<a name="emr-kerberos-cluster-kdc"></a>

You can set up your cluster without a cross\-realm trust, manually adding Linux user accounts to all cluster nodes, adding Kerberos principals to the KDC on the master node, and ensuring that client computers have a Kerberos client installed\.

## Step 1: Create the Kerberized Cluster<a name="emr-kerberos-clusterdedicated-cluster"></a>

1. Create a security configuration that enables Kerberos\. The following example demonstrates a `create-security-configuration` command using the AWS CLI that specifies the security configuration as an inline JSON structure\. You can also reference a file saved locally or in Amazon S3\.

   ```
   aws emr create-security-configuration --name MyKerberosConfig \
   --security-configuration '{"AuthenticationConfiguration": {"KerberosConfiguration": \
   {"Provider": "ClusterDedicatedKdc", "ClusterDedicatedKdcConfiguration": {"TicketLifeTimeInHours": 24}}}}}'
   ```

1. Create a cluster that references the security configuration, establishes Kerberos attributes for the cluster, and adds Linux accounts using a bootstrap action\. The following example demonstrates a `create-cluster `command using the AWS CLI\. The command references the security configuration that you created above, `MyKerberosConfig`\. It also references a simple script, `createlinuxusers.sh`, as a bootstrap action, which you create and upload to Amazon S3 before creating the cluster\.

   ```
   aws emr create-cluster --name "MyKerberosCluster" \
   --release-label emr-5.10.0 \
   --instance-type m4.large \
   --instance-count 3 \
   --ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=MyEC2KeyPair \
   --service-role EMR_DefaultRole \
   --security-configuration MyKerberosConfig\
   --applications Name=Hadoop Name=Hive Name=Oozie Name=Hue Name=HCatalog Name=Spark \
   --kerberos-attributes Realm=EC2.INTERNAL,\
   KdcAdminPassword=MyClusterKDCAdminPwd \
   --bootstrap-actions Path=s3://mybucket/createlinuxusers.sh
   ```

   The following example demonstrates the contents of the `createlinuxusers.sh` script, which adds user1, user2, and user3 to each node in the cluster\. In the next step, you add these users as KDC principals\.

   ```
   #!/bin/bash
   sudo adduser user1
   sudo adduser user2
   sudo adduser user3
   ```

## Step 2: Add Principals to the KDC, Create HDFS User Directories, and Configure SSH<a name="emr-kerberos-clusterdedicated-KDC"></a>

The KDC running on the master node needs a principal added for the local host and for each user that you create on the cluster\. You may also create HDFS directories for each user if they need to connect to the cluster and run Hadoop jobs\. Similarly, configure the SSH service to enable GSSAPI authentication, which is required for Kerberos\. After you enable GSSAPI, restart the SSH service\.

The easiest way to accomplish these tasks is to submit a step to the cluster\. The following example submits a bash script `configurekdc.sh` to the cluster you created in the previous step, referencing its cluster ID\. The script is saved to Amazon S3\. Alternatively, you could connect to the master node using an EC2 key pair to run the commands or submit the step during cluster creation\.

```
aws emr add-steps --cluster-id j-01234567 --steps Type=CUSTOM_JAR,Name=CustomJAR,ActionOnFailure=CONTINUE,Jar=s3://myregion.elasticmapreduce/libs/script-runner/script-runner.jar,Args=["s3://mybucket/configurekdc.sh"]
```

The following example demonstrates the contents of the `configurekdc.sh` script\.

```
#!/bin/bash
#Add a principal to the KDC for the master node, using the master node's returned host name
sudo kadmin.local -q "ktadd -k /etc/krb5.keytab host/`hostname -f`"
#Declare an associative array of user names and passwords to add
declare -A arr
arr=([user1]=pwd1 [user2]=pwd2 [user3]=pwd3)
for i in ${!arr[@]}; do
    #Assign plain language variables for clarity
     name=${i} 
     password=${arr[${i}]}

     # Create principal for sshuser in the master node and require a new password on first logon
     sudo kadmin.local -q "addprinc -pw $password +needchange $name"

     #Add user hdfs directory
     hdfs dfs -mkdir /user/$name

     #Change owner of user's hdfs directory to user
     hdfs dfs -chown $name:$name /user/$name
done

# Enable GSSAPI authentication for SSH and restart SSH service
sudo sed -i 's/^.*GSSAPIAuthentication.*$/GSSAPIAuthentication yes/' /etc/ssh/sshd_config
sudo sed -i 's/^.*GSSAPICleanupCredentials.*$/GSSAPICleanupCredentials yes/' /etc/ssh/sshd_config
sudo /etc/init.d/sshd restart
```

## Step 3: Connect Using SSH \(Linux Client Computer\)<a name="emr-kerberos-clusterdedicated-login"></a>

Your Linux computer most likely includes an SSH client by default\. For example, OpenSSH is installed on most Linux, Unix, and macOS operating systems\. You can check for an SSH client by typing ssh at the command line\. If your computer does not recognize the command, install an SSH client to connect to the master node\. The OpenSSH project provides a free implementation of the full suite of SSH tools\. For more information, see the [OpenSSH](http://www.openssh.org/) website\.

For more information about SSH connections, see [Connect to the Cluster](emr-connect-master-node.md)\. You must also have a Kerberos client installed\.

The following procedure demonstrates the steps to connect to an EMR cluster using Kerberos from a Linux client using the [ssh command](https://www.ssh.com/ssh/command/)\.

For *MasterPublicDNS*, use the value that appears for **Master public DNS **on the **Summary** tab of the cluster details pane, for example, ec2\-11\-222\-33\-44\.compute\-1\.amazonaws\.com\.

**To use SSH to connect to a Kerberized EMR cluster from a Linux client**

1. Use SSH to connect to the master node using an EC2 key pair and copy the contents of the `/etc/krb5.conf` file\. For more information, see [Connect to the Cluster](emr-connect-master-node.md)\.

1. On each client computer that is used to connect to the cluster, create an identical `/etc/krb5.conf` file based on the copy that you made in the previous step\.

1. Each time a user connects from a client computer, the user first renews Kerberos tickets as shown in the following example\.

   ```
   kinit user1
   ```

   The user can then connect with `ssh` using a user name you created earlier and the public DNS name of the master node, as shown in the following example\. Replace *ec2\-xx\-xxx\-xx\-xx\.compute\-1\.amazonaws\.com* with the **Master public DNS** value listed on the cluster's **Summary** page\. The `-K `option specifies GSSAPI authentication\.

   ```
   ssh -K user1@ec2-xx-xxx-xx-xx.compute-1.amazonaws.com
   ```