# View a Summary of Application History<a name="app-history-summary"></a>

With Amazon EMR versions 5\.8\.0 and later, you can view a summary of the application history from the **Application history** tab in the Amazon EMR console\. Amazon EMR keeps the summary of application history for seven days after an application has completed\. 

The following sequence demonstrates a drill\-down through a Spark or YARN application into job details using the **Application history** on a cluster details page\. To view cluster details, from the **Clusters** list, select a cluster **Name**\. To view information about YARN container logs, you must enable logging for your cluster\. For more information, see [Configure Cluster Logging and Debugging](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-debugging.html)\. For Spark application history, the information provided in the summary table is only a subset of the information available through Spark history server UI\.

In the following **Application history** tab example, two rows are expanded to show the diagnostic summaries for two different Spark applications, and an **Application ID** is selected to view further application detail\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-app.png)

On the **Jobs** tab of **YARN application** details, the description of Job 0 is selected to see Job 0 details\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-1.png)

On the Job 0 details page, information about individual job stages is expanded, and then the **Description** for Stage 1 is selected to see Stage 1 details\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-2.png)

On the **Stage 1** details page, key metrics for stage tasks and executors can be seen, and task and executor logs can be viewed using links\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-3.png)