# Diagnose Applications and Jobs with EMR Studio<a name="emr-studio-debug"></a>

With Amazon EMR Studio, you can quickly analyze applications and job runs using application interfaces in your browser without setting up a web proxy\. For applications that you submit to EMR clusters running on EC2, you can launch the YARN timeline server, Spark history server, or Tez UI with one click\. You can also diagnose applications running on Amazon EMR on EKS clusters using the Spark history server\.

**Note**  
Depending on your browser settings, you might need to enable pop\-ups for an Application UI to open\.

**To open an application interface to debug an EMR running on EC2 job from the Studio UI**

1. In your EMR Studio, select **Clusters** under **EMR on EC2** on the left side of the page\. 

1. Filter the list of clusters by **name**, **state**, **ID**, or creation **time range** by entering values in the search box\.

1. Select a cluster and then choose **Launch application UIs** to select an application user interface\. The Application UI opens in a new browser tab and might take some time to load\.

**To open the Spark history server to diagnose an EMR on EKS job run from the Studio UI**

1. In your EMR Studio, select **Clusters** under **EMR on EKS** on the left side of the page\. 

1. Filter the list of clusters by **status** or **ID** by entering values in the search box\.

1. Select a cluster to open its detail page\. The cluster detail page displays information about the EMR on EKS cluster, such as ID, namespace, and status\. The page also shows a list of job runs associated with the cluster\. For more information about job runs, see [Concepts and Components](https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/emr-eks-concepts.html) in the *Amazon EMR on EKS Development Guide*\.
**Note**  
For EMR Studio \(preview\), the **Jobs** list on the cluster detail page only displays job runs that have been submitted to EMR on EKS using the AWS CLI\.

1. From the cluster detail page, select a job run to debug\.

1. In the upper right of the **Jobs** list, choose **Launch Spark History Server** to open the application interface in a new browser tab\.

You can also launch the Spark history server from a Workspace connected to an Amazon EMR on EKS cluster to debug Spark applications that you submit using your notebook\. Choose** Spark UI** at the top of a notebook file to open the Spark history server\.

For information about configuring and using the persistent application interfaces, see [The YARN Timeline Server](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/TimelineServer.html), [Monitoring and Instrumentation](https://spark.apache.org/docs/latest/monitoring.html), or [Tez UI Overview](https://tez.apache.org/tez-ui.html)\.