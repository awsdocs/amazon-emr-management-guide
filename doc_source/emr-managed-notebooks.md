# Using Amazon EMR Notebooks<a name="emr-managed-notebooks"></a>

Use Amazon EMR Notebooks to create and open Jupyter notebooks with the Amazon EMR console\. You can use an EMR notebook with Amazon EMR clusters running [Apache Spark](https://aws.amazon.com/emr/features/spark/) to remotely run queries and code\. An EMR notebook is a "serverless" Jupyter notebook\. Unlike a traditional notebook, the contents of an EMR notebook itself—the equations, visualizations, queries, models, code, and narrative text—are saved in Amazon S3 separately from the cluster that runs the code\. This provides an EMR notebook with durable storage, efficient access, and flexibility\. 

An Amazon EMR cluster is required to execute the code and queries within an EMR notebook, but the notebook isn't locked to the cluster\. This makes support for transient clusters more efficient\. You can start a cluster, attach an EMR notebook to it, and terminate the cluster\. The notebook still exists, so the next time you want to analyze or model data, you can create another cluster and attach the same notebook to it\.

You can also stop an EMR notebook attached to a running cluster and then change clusters\. You can attach to another running cluster or create a new one without having to reconfigure the notebook or terminate clusters\. These features let you run clusters on\-demand to save cost\. Also, you can save time re\-configuring notebooks when you want to use the same notebook on a different cluster or dataset\.

Applicable charges for Amazon S3 storage and for Amazon EMR clusters apply\.

**Topics**
+ [Considerations](emr-managed-notebooks-considerations.md)
+ [Creating a Notebook](emr-managed-notebooks-create.md)
+ [Creating Clusters for Notebooks](emr-managed-notebooks-cluster.md)
+ [Working with Notebooks](emr-managed-notebooks-working-with.md)
+ [Monitoring](emr-managed-notebooks-spark-monitor.md)
+ [Security](emr-managed-notebooks-security.md)