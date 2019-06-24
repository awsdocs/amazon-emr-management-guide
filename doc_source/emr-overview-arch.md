# Overview of Amazon EMR Architecture<a name="emr-overview-arch"></a>

Amazon EMR service architecture consists of several layers, each of which provides certain capabilities and functionality to the cluster\. This section provides an overview of the layers and the components of each\.

**Topics**
+ [Storage](#emr-arch-storage)
+ [Cluster Resource Management](#emr-arch-resource-management)
+ [Data Processing Frameworks](#emr-arch-processing-frameworks)
+ [Applications and Programs](#emr-arch-applications)

## Storage<a name="emr-arch-storage"></a>

The storage layer includes the different file systems that are used with your cluster\. There are several different types of storage options as follows\.

### Hadoop Distributed File System \(HDFS\)<a name="emr-storage-hdfs"></a>

Hadoop Distributed File System \(HDFS\) is a distributed, scalable file system for Hadoop\. HDFS distributes the data it stores across instances in the cluster, storing multiple copies of data on different instances to ensure that no data is lost if an individual instance fails\. HDFS is ephemeral storage that is reclaimed when you terminate a cluster\. HDFS is useful for caching intermediate results during MapReduce processing or for workloads that have significant random I/O\. 

For more information, go to [HDFS Users Guide](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) on the Apache Hadoop website\.

### EMR File System \(EMRFS\)<a name="emr-storage-emrfs"></a>

Using the EMR File System \(EMRFS\), Amazon EMR extends Hadoop to add the ability to directly access data stored in Amazon S3 as if it were a file system like HDFS\. You can use either HDFS or Amazon S3 as the file system in your cluster\. Most often, Amazon S3 is used to store input and output data and intermediate results are stored in HDFS\.

### Local File System<a name="emr-storage-lfs"></a>

The local file system refers to a locally connected disk\. When you create a Hadoop cluster, each node is created from an Amazon EC2 instance that comes with a preconfigured block of pre\-attached disk storage called an instance store\. Data on instance store volumes persists only during the lifecycle of its Amazon EC2 instance\.

## Cluster Resource Management<a name="emr-arch-resource-management"></a>

The resource management layer is responsible for managing cluster resources and scheduling the jobs for processing data\.

By default, Amazon EMR uses YARN \(Yet Another Resource Negotiator\), which is a component introduced in Apache Hadoop 2\.0 to centrally manage cluster resources for multiple data\-processing frameworks\. However, there are other frameworks and applications that are offered in Amazon EMR that do not use YARN as a resource manager\. Amazon EMR also has an agent on each node that administers YARN components, keeps the cluster healthy, and communicates with Amazon EMR\.

Because Spot Instances are often used to run task nodes, Amazon EMR has default functionality for scheduling YARN jobs so that running jobs don’t fail when task nodes running on Spot Instances are terminated\. Amazon EMR does this by allowing application master processes to run only on core nodes\. The application master process controls running jobs and needs to stay alive for the life of the job\.

Amazon EMR release version 5\.19\.0 and later uses the built\-in [YARN node labels](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/NodeLabel.html) feature to achieve this\. \(Earlier versions used a code patch\)\. Properties in the `yarn-site` and `capacity-scheduler` configuration classifications are configured by default so that the YARN capacity\-scheduler and fair\-scheduler take advantage of node labels\. Amazon EMR automatically labels core nodes with the `CORE` label, and sets properties so that application masters are scheduled only on nodes with the CORE label\. Manually modifying related properties in the yarn\-site and capacity\-scheduler configuration classifications, or directly in associated XML files, could break this feature or modify this functionality\.

## Data Processing Frameworks<a name="emr-arch-processing-frameworks"></a>

The data processing framework layer is the engine used to process and analyze data\. There are many frameworks available that run on YARN or have their own resource management\. Different frameworks are available for different kinds of processing needs, such as batch, interactive, in\-memory, streaming, and so on\. The framework that you choose depends on your use case\. This impacts the languages and interfaces available from the application layer, which is the layer used to interact with the data you want to process\. The main processing frameworks available for Amazon EMR are Hadoop MapReduce and Spark\. 

### Hadoop MapReduce<a name="emr-processing-framework-mapreduce"></a>

Hadoop MapReduce is an open\-source programming model for distributed computing\. It simplifies the process of writing parallel distributed applications by handling all of the logic, while you provide the Map and Reduce functions\. The Map function maps data to sets of key\-value pairs called intermediate results\. The Reduce function combines the intermediate results, applies additional algorithms, and produces the final output\. There are multiple frameworks available for MapReduce, such as Hive, which automatically generates Map and Reduce programs\.

For more information, go to [How Map and Reduce operations are actually carried out](http://wiki.apache.org/hadoop/HadoopMapReduce) on the Apache Hadoop Wiki website\.

### Apache Spark<a name="emr-processing-framework-spark"></a>

Spark is a cluster framework and programming model for processing big data workloads\. Like Hadoop MapReduce, Spark is an open\-source, distributed processing system but uses directed acyclic graphs for execution plans and in\-memory caching for datasets\. When you run Spark on Amazon EMR, you can use EMRFS to directly access your data in Amazon S3\. Spark supports multiple interactive query modules such as SparkSQL\.

For more information, see [Apache Spark on Amazon EMR Clusters](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-spark.html) in the *Amazon EMR Release Guide*\.

## Applications and Programs<a name="emr-arch-applications"></a>

 Amazon EMR supports many applications, such as Hive, Pig, and the Spark Streaming library to provide capabilities such as using higher\-level languages to create processing workloads, leveraging machine learning algorithms, making stream processing applications, and building data warehouses\. In addition, Amazon EMR also supports open\-source projects that have their own cluster management functionality instead of using YARN\.

You use various libraries and languages to interact with the applications that you run in Amazon EMR\. For example, you can use Java, Hive, or Pig with MapReduce or Spark Streaming, Spark SQL, MLlib, and GraphX with Spark\.

For more information, see the [Amazon EMR Release Guide](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/)\.