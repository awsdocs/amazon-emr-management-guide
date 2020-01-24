# View Application History<a name="emr-cluster-application-history"></a>

You can view Spark and YARN application details using the **Application history** tab of a cluster's detail page in the console\. Amazon EMR application history makes it easier for you to troubleshoot and analyze active jobs and job history\. 

The **Application history** tab provides two viewing options:
+ **One\-click access to persistent Spark history server ** – With Amazon EMR version 5\.25\.0 or later, you can choose the link to access Spark history server UI without setting up a web proxy through an SSH connection\. The Spark history server UI provides details about scheduler stages and tasks, RDD sizes and memory usage, environmental information, and information about the running executors\. 
+ **View a summary of the application history** – With Amazon EMR version 5\.8\.0 or later, you can view a summary of the application history in the EMR console including key metrics for stage tasks and executors\. The summary of the application history is available for all YARN applications\. Additional details are provided for Spark applications, but these details are only a subset of the information available through Spark history server UI\. 

**Topics**
+ [One\-click Access to Persistent Spark History Server](app-history-spark-UI.md)
+ [View a Summary of Application History](app-history-summary.md)