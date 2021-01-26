# Start an EMR cluster<a name="emr-ranger-start-emr-cluster"></a>

Before you launch an Amazon EMR cluster with Apache Ranger, make sure each component meets the minimum version requirement:
+ Amazon EMR release should be at least 5\.32 or later\. EMR 6\.x is currently not supported\.
+ Apache Ranger Admin server is 2\.x\.

Complete the following steps\.
+ Install Apache Ranger if you haven't already\. For more information, see [Apache Ranger 0\.5\.0 Installation](https://cwiki.apache.org/confluence/display/RANGER/Apache+Ranger+0.5.0+Installation)\.
+ Make sure there is network connectivity between your Amazon EMR cluster and the Apache Ranger Admin server\. See [Set Up Ranger Admin Server](emr-ranger-admin.md)
+ Create the necessary IAM Roles\. See [IAM roles for native integration with Apache Ranger](emr-ranger-iam.md)\.
+ Create a EMR security configuration for Apache Ranger installation\. See more information, see [Create the EMR Security Configuration](emr-ranger-security-config.md)\.