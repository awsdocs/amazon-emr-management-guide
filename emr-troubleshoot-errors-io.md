# Input and Output Errors<a name="emr-troubleshoot-errors-io"></a>

The following errors are common in cluster input and output operations\.

**Topics**
+ [Does your path to Amazon Simple Storage Service \(Amazon S3\) have at least three slashes?](#threeslashes)
+ [Are you trying to recursively traverse input directories?](#recurseinput)
+ [Does your output directory already exist?](#directoryexist)
+ [Are you trying to specify a resource using an HTTP URL?](#httpurl)
+ [Are you referencing an Amazon S3 bucket using an invalid name format?](#validdnsname)
+ [Are you experiencing trouble loading data to or from Amazon S3?](#emr-troubleshoot-errors-io-1)

## Does your path to Amazon Simple Storage Service \(Amazon S3\) have at least three slashes?<a name="threeslashes"></a>

 When you specify an Amazon S3 bucket, you must include a terminating slash on the end of the URL\. For example, instead of referencing a bucket as “s3n://myawsbucket”, you should use “s3n://myawsbucket/”, otherwise Hadoop fails your cluster in most cases\. 

## Are you trying to recursively traverse input directories?<a name="recurseinput"></a>

 Hadoop does not recursively search input directories for files\. If you have a directory structure such as /corpus/01/01\.txt, /corpus/01/02\.txt, /corpus/02/01\.txt, etc\. and you specify /corpus/ as the input parameter to your cluster, Hadoop does not find any input files because the /corpus/ directory is empty and Hadoop does not check the contents of the subdirectories\. Similarly, Hadoop does not recursively check the subdirectories of Amazon S3 buckets\. 

 The input files must be directly in the input directory or Amazon S3 bucket that you specify, not in subdirectories\. 

## Does your output directory already exist?<a name="directoryexist"></a>

 If you specify an output path that already exists, Hadoop will fail the cluster in most cases\. This means that if you run a cluster one time and then run it again with exactly the same parameters, it will likely work the first time and then never again; after the first run, the output path exists and thus causes all successive runs to fail\. 

## Are you trying to specify a resource using an HTTP URL?<a name="httpurl"></a>

 Hadoop does not accept resource locations specified using the http:// prefix\. You cannot reference a resource using an HTTP URL\. For example, passing in http://mysite/myjar\.jar as the JAR parameter causes the cluster to fail\. 

## Are you referencing an Amazon S3 bucket using an invalid name format?<a name="validdnsname"></a>

 If you attempt to use a bucket name such as “myawsbucket\.1” with Amazon EMR, your cluster will fail because Amazon EMR requires that bucket names be valid RFC 2396 host names; the name cannot end with a number\. In addition, because of the requirements of Hadoop, Amazon S3 bucket names used with Amazon EMR must contain only lowercase letters, numbers, periods \(\.\), and hyphens \(\-\)\. For more information about how to format Amazon S3 bucket names, see [Bucket Restrictions and Limitations](https://docs.aws.amazon.com/AmazonS3/latest/dev//index.html?BucketRestrictions.html) in the *Amazon Simple Storage Service Developer Guide*\. 

## Are you experiencing trouble loading data to or from Amazon S3?<a name="emr-troubleshoot-errors-io-1"></a>

 Amazon S3 is the most popular input and output source for Amazon EMR\. A common mistake is to treat Amazon S3 as you would a typical file system\. There are differences between Amazon S3 and a file system that you need to take into account when running your cluster\. 
+  If an internal error occurs in Amazon S3, your application needs to handle this gracefully and re\-try the operation\. 
+  If calls to Amazon S3 take too long to return, your application may need to reduce the frequency at which it calls Amazon S3\. 
+  Listing all the objects in an Amazon S3 bucket is an expensive call\. Your application should minimize the number of times it does this\. 

 There are several ways you can improve how your cluster interacts with Amazon S3\. 
+  Launch your cluster using the most recent release version of Amazon EMR\. 
+ Use S3DistCp to move objects in and out of Amazon S3\. S3DistCp implements error handling, retries and back\-offs to match the requirements of Amazon S3\. For more information, see [Distributed Copy Using S3DistCp](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/UsingEMR_s3distcp.html)\. 
+  Design your application with eventual consistency in mind\. Use HDFS for intermediate data storage while the cluster is running and Amazon S3 only to input the initial data and output the final results\. 
+  If your clusters will commit 200 or more transactions per second to Amazon S3, [contact support](https://aws.amazon.com//contact-us/) to prepare your bucket for greater transactions per second and consider using the key partition strategies described in [Amazon S3 Performance Tips & Tricks](http://aws.typepad.com/aws/2012/03/amazon-s3-performance-tips-tricks-seattle-hiring-event.html)\. 
+  Set the Hadoop configuration setting io\.file\.buffer\.size to 65536\. This causes Hadoop to spend less time seeking through Amazon S3 objects\. 
+  Consider disabling Hadoop's speculative execution feature if your cluster is experiencing Amazon S3 concurrency issues\. You do this through the mapred\.map\.tasks\.speculative\.execution and mapred\.reduce\.tasks\.speculative\.execution configuration settings\. This is also useful when you are troubleshooting a slow cluster\. 
+  If you are running a Hive cluster, see [Are you having trouble loading data to or from Amazon S3 into Hive?](emr-troubleshoot-error-hive.md#emr-troubleshoot-error-hive-3)\. 

 For additional information, see [Amazon S3 Error Best Practices](https://docs.aws.amazon.com/AmazonS3/latest/dev//ErrorBestPractices.html) in the *Amazon Simple Storage Service Developer Guide*\. 