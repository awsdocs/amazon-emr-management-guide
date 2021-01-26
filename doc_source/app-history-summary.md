# View a Summary of Application History<a name="app-history-summary"></a>

With Amazon EMR versions 5\.8\.0 and later, you can view a high\-level application history from the **Application user interfaces** tab in the Amazon EMR console\. Amazon EMR keeps the summary of application history for seven days after an application has completed\. 

The following sequence demonstrates a drill\-down through a Spark or YARN application into job details using the **Application user interfaces** on a cluster details page\. To view cluster details, from the **Clusters** list, select a cluster **Name**\. To view information about YARN container logs, you must enable logging for your cluster\. For more information, see [Configure Cluster Logging and Debugging](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-debugging.html)\. For Spark application history, the information provided in the summary table is only a subset of the information available through Spark history server UI\.

In the following **Application user interfaces** tab example, a row is expanded to show the diagnostic summary for a Spark application, and an **Application ID** link is selected to view another application's detail\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-app.png)

In the example below, on the **Jobs** tab of **YARN application** details, the description of Job 9 is selected to see Job 9 details\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-1.png)

On the Job 9 details page, information about individual job stages is expanded, and then the **Description** for Stage 29 is selected to see Stage 29 details\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-2.png)

On the **Stage 29** details page, key metrics for stage tasks and executors can be seen, and task and executor logs can be viewed using links\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-3.png)