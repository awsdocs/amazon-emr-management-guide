# How to write data to an Amazon S3 bucket you don't own<a name="emr-s3-acls"></a>

 When you write a file to an Amazon Simple Storage Service \(Amazon S3\) bucket, by default, you are the only one able to read that file\. The assumption is that you will write files to your own buckets, and this default setting protects the privacy of your files\. 

 However, if you are running a cluster, and you want the output to write to the Amazon S3 bucket of another AWS user, and you want that other AWS user to be able to read that output, you must do two things: 
+  Have the other AWS user grant you write permissions for their Amazon S3 bucket\. The cluster you launch runs under your AWS credentials, so any clusters you launch will also be able to write to that other AWS user's bucket\. 
+  Set read permissions for the other AWS user on the files that you or the cluster write to the Amazon S3 bucket\. The easiest way to set these read permissions is to use canned access control lists \(ACLs\), a set of pre\-defined access policies defined by Amazon S3\. 

 For information about how the other AWS user can grant you permissions to write files to the other user's Amazon S3 bucket, see [Editing Bucket Permissions](https://docs.aws.amazon.com/AmazonS3/latest/user-guide//EditingBucketPermissions.html) in the *Amazon Simple Storage Service Console User Guide*\. 

 For your cluster to use canned ACLs when it writes files to Amazon S3, set the `fs.s3.canned.acl` cluster configuration option to the canned ACL to use\. The following table lists the currently defined canned ACLs\. 


| Canned ACL | Description | 
| --- | --- | 
| AuthenticatedRead | Specifies that the owner is granted Permission\.FullControl and the GroupGrantee\.AuthenticatedUsers group grantee is granted Permission\.Read access\. | 
| BucketOwnerFullControl | Specifies that the owner of the bucket is granted Permission\.FullControl\. The owner of the bucket is not necessarily the same as the owner of the object\. | 
| BucketOwnerRead | Specifies that the owner of the bucket is granted Permission\.Read\. The owner of the bucket is not necessarily the same as the owner of the object\. | 
| LogDeliveryWrite | Specifies that the owner is granted Permission\.FullControl and the GroupGrantee\.LogDelivery group grantee is granted Permission\.Write access, so that access logs can be delivered\. | 
| Private | Specifies that the owner is granted Permission\.FullControl\. | 
| PublicRead | Specifies that the owner is granted Permission\.FullControl and the GroupGrantee\.AllUsers group grantee is granted Permission\.Read access\. | 
| PublicReadWrite | Specifies that the owner is granted Permission\.FullControl and the GroupGrantee\.AllUsers group grantee is granted Permission\.Read and Permission\.Write access\. | 

 There are many ways to set the cluster configuration options, depending on the type of cluster you are running\. The following procedures show how to set the option for common cases\. 

**To write files using canned ACLs in Hive**
+  From the Hive command prompt, set the `fs.s3.canned.acl` configuration option to the canned ACL you want to have the cluster set on files it writes to Amazon S3\. To access the Hive command prompt connect to the master node using SSH, and type Hive at the Hadoop command prompt\. For more information, see [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\. 

   The following example sets the `fs.s3.canned.acl` configuration option to `BucketOwnerFullControl`, which gives the owner of the Amazon S3 bucket complete control over the file\. Note that the set command is case sensitive and contains no quotation marks or spaces\. 

  ```
  hive> set fs.s3.canned.acl=BucketOwnerFullControl;   
  create table acl (n int) location 's3://acltestbucket/acl/'; 
  insert overwrite table acl select count(n) from acl;
  ```

   The last two lines of the example create a table that is stored in Amazon S3 and write data to the table\. 

**To write files using canned ACLs in Pig**
+  From the Pig command prompt, set the `fs.s3.canned.acl` configuration option to the canned ACL you want to have the cluster set on files it writes to Amazon S3\. To access the Pig command prompt connect to the master node using SSH, and type Pig at the Hadoop command prompt\. For more information, see [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)\. 

   The following example sets the `fs.s3.canned.acl` configuration option to BucketOwnerFullControl, which gives the owner of the Amazon S3 bucket complete control over the file\. Note that the set command includes one space before the canned ACL name and contains no quotation marks\. 

  ```
  pig> set fs.s3.canned.acl BucketOwnerFullControl; 
  store some data into 's3://acltestbucket/pig/acl';
  ```

**To write files using canned ACLs in a custom JAR**
+  Set the `fs.s3.canned.acl` configuration option using Hadoop with the \-D flag\. This is shown in the example below\. 

  ```
  hadoop jar hadoop-examples.jar wordcount 
  -Dfs.s3.canned.acl=BucketOwnerFullControl s3://mybucket/input s3://mybucket/output
  ```