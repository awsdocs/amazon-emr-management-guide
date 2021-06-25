# Considerations and issues<a name="emr-ranger-security-considerations"></a>

**Configuring Kerberos on EMR**

When an EMR cluster is configured for Kerberos, which is a requirement for Apache Ranger integration with Amazon EMR, Kerberos needs to be configured for users to be able to authenticate\. See [Configuring Kerberos on Amazon EMR](emr-kerberos-configure.md) for more information on configuring Kerberos, and [Configuring a cluster for Kerberos\-authenticated HDFS users and SSH connections](emr-kerberos-configuration-users.md) for information on how users and Kerberos principals can be created\.

**Configuring Hue**

For instructions on configuring Hue to use LDAP as an authentication mechanism, see [Configure Hue for LDAP users](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/hue-ldap.html) in the *Amazon EMR Release Guide*\.

**Known Issues**

There is a known issue within Amazon EMR release 5\.32 in which the permissions for Hive\-site\.xml was changed so that only privileged users can read it as there may be credentials stored within it\. This could prevent Hue from reading Hive\-site\.xml and cause webpages to continuously reload\. If you experience this issue, add the following configuration to fix the issue:

```
   {
        "Classification": "hue-ini",
        "Properties": {},
        "Configurations": [
            {
                "Classification": "desktop",
                "Properties": {
                    "server_group":"hive_site_reader"
                },
                "Configurations": [
                ]
            }
     }
```

**Configuring Zeppelin**

Use Apache Zeppelin as a notebook for interactive data exploration\. For more information about Zeppelin, see [Apache Zeppelin](https://zeppelin.apache.org/)\. Zeppelin is included in Amazon EMR release version 5\.0\.0 and later\. Earlier release versions include Zeppelin as a sandbox application\. For more information, see [Amazon EMR 4\.x release versions](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-release-4x.html) in the *Amazon EMR Release Guide*\.

By default, Zeppelin is configured with a default login and password which in a multi\-tenant environment is not secure\.

To configure Zeppelin for Apache Spark use cases, complete the following steps\.

**Step 1: Modify the authentication mechanism**

Modify the shiro\.ini file to implement your preferred authentication mechanism\. Zeppelin supports Active Directory, LDAP, PAM, and Knox SSO\. See [Apache Shiro authentication for Apache Zeppelin](https://zeppelin.apache.org/docs/0.8.2/setup/security/shiro_authentication.html) for more information\.

**Step 2: Configure Zeppelin to impersonate the end user**

Allowing Zeppelin to be able to impersonate the end user allows jobs submitted by Zeppelin to be run as the end user\.

**Step 2a\. Allow Zeppelin to sudo as the end user**

Create a file /etc/sudoers\.d/90\-zeppelin\-user that contains the following\.

```
zeppelin ALL=(ALL) NOPASSWD:ALL"
```

**Step 2b\. Modify interpreters settings to run user jobs in their own processes**\.

For all interpreters, configure them to instantiate the interpreters "Per User" in "isolated" processes as shown below\.

![\[Amazon EMR and Apache Ranger architecture diagram.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/per_user.png)

**Step 2c\. Modify zeppelin\-env\.sh**

Add the following to zeppelin\-env\.sh so that Zeppelin starts launch interpreters as the end user\.

```
ZEPPELIN_IMPERSONATE_USER=`echo ${ZEPPELIN_IMPERSONATE_USER} | cut -d @ -f1`
export ZEPPELIN_IMPERSONATE_CMD='sudo -H -u ${ZEPPELIN_IMPERSONATE_USER} bash -c'
```

Add the following to zeppelin\-env\.sh to change the default notebook permissions to read\-only to the creator only\.

```
export ZEPPELIN_NOTEBOOK_PUBLIC="false"
```

Lastly, add the following to zeppelin\-env\.sh to include the EMR RecordServer class path after the first CLASSPATH statement\.

```
export CLASSPATH="$CLASSPATH:/usr/share/aws/emr/record-server/lib/aws-emr-record-server-connector-common.jar:/usr/share/aws/emr/record-server/lib/aws-emr-record-server-spark-connector.jar:/usr/share/aws/emr/record-server/lib/aws-emr-record-server-client.jar:/usr/share/aws/emr/record-server/lib/aws-emr-record-server-common.jar:/usr/share/aws/emr/record-server/lib/jars/secret-agent-interface.jar"
```

**Step 3\. Restart Zeppelin\.**

Run the following command to restart Zeppelin

```
sudo systemctl restart zeppelin
```

**Application UIs**

By default, Application UI's do not perform authentication\. This includes the ResourceManager UI, NodeManager UI, Livy UI, among others\. In addition, any user that has the ability to access the UIs is able to view information about all other users' jobs\.

If this behavior is not desired, you should ensure that a security group is used to restrict access to the application UIs by users\.

**HDFS Default Permissions**

By default, the objects that users create in HDFS are given world readable permissions\. This can potentially cause data readable by users that should not have access to it\. To change this behavior such that the default file permissions are set to read and write only by the creator of the job, perform these steps\.

When creating your EMR cluster, provide the following configuration:

```
[{
        "Classification": "hdfs-site",
        "Properties": {
            "dfs.namenode.acls.enabled": "true",
            "fs.permissions.umask-mode": "077",
            "dfs.permissions.superusergroup": "hdfsadmingroup"
        }
}]
```

In addition, run the following bootstrap action:

```
--bootstrap-actions Name='HDFS UMask Setup',Path=s3://elasticmapreduce/hdfs/umask/umask-main.sh
```