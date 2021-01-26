# Apache Ranger Troubleshooting<a name="emr-ranger-troubleshooting"></a>

Here are some commonly diagnosed issues related to using Apache Ranger\.

## Recommendations<a name="emr-ranger-troubleshooting-recommendations"></a>
+ **Test using a single master node cluster:** Single node master clusters provision quicker than a multi\-node cluster which can decrease the time for each testing iteration\.
+ **Set development mode on the cluster\.** When starting your EMR cluster, set the `--additional-info"` parameter to:

  `'{"clusterType":"development"}'`

  This parameter can only be set through the AWS CLI or AWS SDK and is not available through the EMR console\. When this flag is set, and the master fails to provision, the EMR service keeps the cluster alive for some time before it decommissions it\. This time is very useful for probing various log files before the cluster is terminated\.