# VPC Errors<a name="emr-troubleshoot-error-vpc"></a>

The following errors are common to VPC configuration in Amazon EMR\.

**Topics**
+ [Invalid Subnet Configuration](#emr-troubleshoot-error-gateway)
+ [Missing DHCP Options Set](#emr-troubleshoot-error-dhcp)
+ [Permissions Errors](#emr-troubleshoot-error-denied)
+ [Errors That Result in `START_FAILED`](#emr-troubleshoot-error-vpc-dns)
+ [Cluster `Terminated with errors` and NameNode Fails to Start](#emr-troubleshoot-namenode-dns)

## Invalid Subnet Configuration<a name="emr-troubleshoot-error-gateway"></a>

 On the **Cluster Details** page, in the **Status** field, you see an error similar to the following:

`The subnet configuration was invalid: Cannot find route to InternetGateway in main RouteTable rtb-id for vpc vpc-id.`

To solve this problem, you must create an Internet Gateway and attach it to your VPC\. For more information, see [Adding an Internet Gateway to Your VPC](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html)\.

Alternatively, verify that you have configured your VPC with **Enable DNS resolution** and **Enable DNS hostname support** enabled\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-dns.html)\. 

## Missing DHCP Options Set<a name="emr-troubleshoot-error-dhcp"></a>

You see a step failure in the cluster system log \(syslog\) with an error similar to the following:

` ERROR org.apache.hadoop.security.UserGroupInformation (main): PriviledgedActionException as:hadoop (auth:SIMPLE) cause:java.io.IOException: org.apache.hadoop.yarn.exceptions.ApplicationNotFoundException: Application with id 'application_id' doesn't exist in RM. `

or 

`ERROR org.apache.hadoop.streaming.StreamJob (main): Error Launching job : org.apache.hadoop.yarn.exceptions.ApplicationNotFoundException: Application with id 'application_id' doesn't exist in RM.`

To solve this problem, you must configure a VPC that includes a DHCP Options Set whose parameters are set to the following values: 

**Note**  
If you use the AWS GovCloud \(US\-West\) region, set domain\-name to **us\-gov\-west\-1\.compute\.internal** instead of the value used in the following example\.
+ **domain\-name** = **ec2\.internal**

  Use **ec2\.internal** if your region is US East \(N\. Virginia\)\. For other regions, use *region\-name***\.compute\.internal**\. For example in us\-west\-2, use **domain\-name**=**us\-west\-2\.compute\.internal**\.
+ **domain\-name\-servers** = **AmazonProvidedDNS**

For more information, see [DHCP Options Sets](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_DHCP_Options.html)\.

## Permissions Errors<a name="emr-troubleshoot-error-denied"></a>

A failure in the `stderr` log for a step indicates that an Amazon S3 resource does not have the appropriate permissions\. This is a 403 error and the error looks like:

```
Exception in thread "main" com.amazonaws.services.s3.model.AmazonS3Exception: Access Denied (Service: Amazon S3; Status Code: 403; Error Code: AccessDenied; Request ID: REQUEST_ID
```

If the ActionOnFailure is set to `TERMINATE_JOB_FLOW`, then this would result in the cluster terminating with the state, `SHUTDOWN_COMPLETED_WITH_ERRORS`\.

A few ways to troubleshoot this problem include:
+ If you are using an Amazon S3 bucket policy within a VPC, make sure to give access to all buckets by creating a VPC endpoint and selecting **Allow all** under the Policy option when creating the endpoint\. 
+ Make sure that any policies associated with S3 resources include the VPC in which you launch the cluster\.
+ Try running the following command from your cluster to verify you can access the bucket

  ```
  hadoop fs -copyToLocal s3://path-to-bucket /tmp/
  ```
+ You can get more specific debugging information by setting the `log4j.logger.org.apache.http.wire` parameter to `DEBUG` in `/home/hadoop/conf/log4j.properties` file on the cluster\. You can check the `stderr` log file after trying to access the bucket from the cluster\. The log file will provide more detailed information:

  ```
  Access denied for getting the prefix for bucket - us-west-2.elasticmapreduce with path samples/wordcount/input/
  15/03/25 23:46:20 DEBUG http.wire: >> "GET /?prefix=samples%2Fwordcount%2Finput%2F&delimiter=%2F&max-keys=1 HTTP/1.1[\r][\n]"
  15/03/25 23:46:20 DEBUG http.wire: >> "Host: us-west-2.elasticmapreduce.s3.amazonaws.com[\r][\n]"
  ```

## Errors That Result in `START_FAILED`<a name="emr-troubleshoot-error-vpc-dns"></a>

Before AMI 3\.7\.0, for VPCs where a hostname is specified, Amazon EMR maps the internal hostnames of the subnet with custom domain addresses as follows: `ip-X.X.X.X.customdomain.com.tld`\. For example, if the hostname was `ip-10.0.0.10` and the VPC has the domain name option set to customdomain\.com, the resulting hostname mapped by Amazon EMR would be `ip-10.0.1.0.customdomain.com`\. An entry is added in `/etc/hosts` to resolve the hostname to 10\.0\.0\.10\. This behavior is changed with AMI 3\.7\.0 and now Amazon EMR honors the DHCP configuration of the VPC entirely\. Previously, customers could also use a bootstrap action to specify a hostname mapping\.

If you would like to preserve this behavior, you must provide the DNS and forward resolution setup you require for the custom domain\.

## Cluster `Terminated with errors` and NameNode Fails to Start<a name="emr-troubleshoot-namenode-dns"></a>

When launching an EMR cluster in a VPC which makes use of a custom DNS domain name, your cluster may fail with the following error message in the console:

```
Terminated with errors  On the master instance(instance-id), bootstrap action 1 returned a  non-zero return code
```

The failure is a result of the NameNode not being able to start up\. This will result in the following error found in the NameNode logs, whose Amazon S3 URI is of the form: `s3://mybucket/logs/cluster-id/daemons/master instance-id/hadoop-hadoop-namenode-master node hostname.log.gz`:

```
2015-07-23 20:17:06,266 WARN
      org.apache.hadoop.hdfs.server.namenode.FSNamesystem (main): Encountered  exception
      loading fsimage  java.io.IOException: NameNode is not formatted.      
      at
      org.apache.hadoop.hdfs.server.namenode.FSImage.recoverTransitionRead(FSImage.java:212)
           at
      org.apache.hadoop.hdfs.server.namenode.FSNamesystem.loadFSImage(FSNamesystem.java:1020)
           at
      org.apache.hadoop.hdfs.server.namenode.FSNamesystem.loadFromDisk(FSNamesystem.java:739)
           at
      org.apache.hadoop.hdfs.server.namenode.NameNode.loadNamesystem(NameNode.java:537)
           at
      org.apache.hadoop.hdfs.server.namenode.NameNode.initialize(NameNode.java:596)      
      at  org.apache.hadoop.hdfs.server.namenode.NameNode.<init>(NameNode.java:765)
           at
      org.apache.hadoop.hdfs.server.namenode.NameNode.<init>(NameNode.java:749)      
      at
      org.apache.hadoop.hdfs.server.namenode.NameNode.createNameNode(NameNode.java:1441)
           at
      org.apache.hadoop.hdfs.server.namenode.NameNode.main(NameNode.java:1507)
```

This is due to a potential issue where an EC2 instance can have multiple sets of fully qualified domain names when launching EMR clusters in a VPC, which makes use of both an AWS\-provided DNS server and a custom user\-provided DNS server\. If the user\-provided DNS server does not provide any pointer \(PTR\) records for any A records used to designate nodes in an EMR cluster, clusters will fail starting up when configured in this way\. The solution is to add 1 PTR record for every A record that is created when an EC2 instance is launched in any of the subnets in the VPC\.