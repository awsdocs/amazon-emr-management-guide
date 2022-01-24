# Configure Zeppelin for Apache Ranger\-enabled Amazon EMR clusters<a name="emr-ranger-configure-zeppelin"></a>

The topic covers how to configure [Apache Zeppelin](https://zeppelin.apache.org/) for an Apache Ranger\-enabled Amazon EMR cluster so that you can use Zeppelin as a notebook for interactive data exploration\. Zeppelin is included in Amazon EMR release versions 5\.0\.0 and later\. Earlier release versions include Zeppelin as a sandbox application\. For more information, see [Amazon EMR 4\.x release versions](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-release-4x.html) in the *Amazon EMR Release Guide*\.

By default, Zeppelin is configured with a default login and password which is not secure in a multi\-tenant environment\.

To configure Zeppelin, complete the following steps\.

1. **Modify the authentication mechanism**\. 

   Modify the `shiro.ini` file to implement your preferred authentication mechanism\. Zeppelin supports Active Directory, LDAP, PAM, and Knox SSO\. See [Apache Shiro authentication for Apache Zeppelin](https://zeppelin.apache.org/docs/0.8.2/setup/security/shiro_authentication.html) for more information\.

1. **Configure Zeppelin to impersonate the end user**

   When you allow Zeppelin to impersonate the end user, jobs submitted by Zeppelin can be run as that end user\. Add the following configuration to `core-site.xml`:

   ```
   [
     {
       "Classification": "core-site",
       "Properties": {
         "hadoop.proxyuser.zeppelin.hosts": "*",
         "hadoop.proxyuser.zeppelin.groups": "*"
       },
       "Configurations": [
       ]
     }
   ]
   ```

   Next, add the following configuration to `hadoop-kms-site.xml` located in `/etc/hadoop/conf`:

   ```
   [
     {
       "Classification": "hadoop-kms-site",
       "Properties": {
         "hadoop.kms.proxyuser.zeppelin.hosts": "*",
         "hadoop.kms.proxyuser.zeppelin.groups": "*"
       },
       "Configurations": [
       ]
     }
   ]
   ```

   You can also add these configurations to your Amazon EMR cluster using the console by following the steps in [Reconfigure an instance group in the console](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-configure-apps-running-cluster.html#emr-configure-apps-running-cluster-console)\.

1. **Allow Zeppelin to sudo as the end user**

   Create a file `/etc/sudoers.d/90-zeppelin-user` that contains the following:

   ```
   zeppelin ALL=(ALL) NOPASSWD:ALL
   ```

1. **Modify interpreters settings to run user jobs in their own processes**\.

   For all interpreters, configure them to instantiate the interpreters "Per User" in "isolated" processes as shown below\.  
![\[Amazon EMR and Apache Ranger architecture diagram.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/per_user.png)

1. **Modify `zeppelin-env.sh`**

   Add the following to `zeppelin-env.sh` so that Zeppelin starts launch interpreters as the end user:

   ```
   ZEPPELIN_IMPERSONATE_USER=`echo ${ZEPPELIN_IMPERSONATE_USER} | cut -d @ -f1`
   export ZEPPELIN_IMPERSONATE_CMD='sudo -H -u ${ZEPPELIN_IMPERSONATE_USER} bash -c'
   ```

   Add the following to `zeppelin-env.sh` to change the default notebook permissions to read\-only to the creator only:

   ```
   export ZEPPELIN_NOTEBOOK_PUBLIC="false"
   ```

   Finally, add the following to `zeppelin-env.sh` to include the EMR RecordServer class path after the first `CLASSPATH` statement:

   ```
   export CLASSPATH="$CLASSPATH:/usr/share/aws/emr/record-server/lib/aws-emr-record-server-connector-common.jar:/usr/share/aws/emr/record-server/lib/aws-emr-record-server-spark-connector.jar:/usr/share/aws/emr/record-server/lib/aws-emr-record-server-client.jar:/usr/share/aws/emr/record-server/lib/aws-emr-record-server-common.jar:/usr/share/aws/emr/record-server/lib/jars/secret-agent-interface.jar"
   ```

1. **Restart Zeppelin\.**

   Run the following command to restart Zeppelin:

   ```
   sudo systemctl restart zeppelin
   ```