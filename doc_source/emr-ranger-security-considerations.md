# Known issues<a name="emr-ranger-security-considerations"></a>

**Known Issues**

There is a known issue within Amazon EMR release 5\.32 in which the permissions for `hive-site.xml` was changed so that only privileged users can read it as there may be credentials stored within it\. This could prevent Hue from reading `hive-site.xml` and cause webpages to continuously reload\. If you experience this issue, add the following configuration to fix the issue:

```
[
  {
    "Classification": "hue-ini",
    "Properties": {},
    "Configurations": [
      {
        "Classification": "desktop",
        "Properties": {
          "server_group":"hive_site_reader"
         },
        "Configurations":[
        ]
      }
    ]
  }
]
```

**Application UIs**

By default, Application UI's do not perform authentication\. This includes the ResourceManager UI, NodeManager UI, Livy UI, among others\. In addition, any user that has the ability to access the UIs is able to view information about all other users' jobs\.

If this behavior is not desired, you should ensure that a security group is used to restrict access to the application UIs by users\.

**HDFS Default Permissions**

By default, the objects that users create in HDFS are given world readable permissions\. This can potentially cause data readable by users that should not have access to it\. To change this behavior such that the default file permissions are set to read and write only by the creator of the job, perform these steps\.

When creating your EMR cluster, provide the following configuration:

```
[
  {
    "Classification": "hdfs-site",
    "Properties": {
      "dfs.namenode.acls.enabled": "true",
      "fs.permissions.umask-mode": "077",
      "dfs.permissions.superusergroup": "hdfsadmingroup"
    }
  }
]
```

In addition, run the following bootstrap action:

```
--bootstrap-actions Name='HDFS UMask Setup',Path=s3://elasticmapreduce/hdfs/umask/umask-main.sh
```