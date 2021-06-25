# Plan and configure clusters<a name="emr-plan"></a>

This section explains configuration options and instructions for planning, configuring, and launching clusters using Amazon EMR\. Before you launch a cluster, you make choices about your system based on the data that you're processing and your requirements for cost, speed, capacity, availability, security, and manageability\. Your choices include: 
+ What region to run a cluster in, where and how to store data, and how to output results\. See [Configure cluster location and data storage](emr-cluster-location-data-storage.md)\.
+ Whether you are running Amazon EMR clusters on Outposts or Local Zones\. See [EMR clusters on AWS Outposts](emr-plan-outposts.md) or [EMR clusters on AWS Local Zones](emr-plan-localzones.md)\.
+ Whether a cluster is long\-running or transient, and what software it runs\. See [Configuring a cluster to auto\-terminate or continue](emr-plan-longrunning-transient.md) and [Configure cluster software](emr-plan-software.md)\.
+ Whether a cluster has a single master node or three master nodes\. See [Plan and configure master nodes](emr-plan-ha.md)\.
+ The hardware and networking options that optimize cost, performance, and availability for your application\. See [Configure cluster hardware and networking](emr-plan-instances.md)\.
+ How to set up clusters so you can manage them more easily, and monitor activity, performance, and health\. See [Configure cluster logging and debugging](emr-plan-debugging.md) and [Tag clusters](emr-plan-tags.md)\.
+ How to authenticate and authorize access to cluster resources, and how to encrypt data\. See [Security in Amazon EMR](emr-security.md)\.
+ How to integrate with other software and services\. See [Drivers and third\-party application integration](emr-plan-third-party.md)\.