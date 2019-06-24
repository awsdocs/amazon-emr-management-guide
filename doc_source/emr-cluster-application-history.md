# View Application History<a name="emr-cluster-application-history"></a>

Using Amazon EMR version 5\.8\.0 or later, you can view YARN application details using the **Application history** tab of a cluster's detail page in the console\. Using Amazon EMR application history makes it easier for you to troubleshoot and analyze active jobs and job history\. Instead of setting up and connecting to the master node to view open\-source troubleshooting UIs or sift through log files, you can quickly view application metrics and access relevant log files\.

Application history is enabled automatically for all clusters that run YARN applications\. No special setup is required\. Amazon EMR keeps historical information for up to seven days after an application has completed\. Detailed application history is available only for Spark\. There are additional minor limitations in Amazon EMR version 5\.8\.0\. For more information, see [Known Issues](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-whatsnew-history.html#emr-580-known-issues) for version 5\.8\.0 in the *Amazon EMR Release Guide*\.

## Example: View Job Details for a Spark Application<a name="app-history-spark-example"></a>

The following sequence demonstrates a drill\-down through a Spark application into job detail to evaluate stages, tasks, and executors using the **Application history** on a cluster details page \(to view cluster details, from the **Clusters** list, select a cluster **Name**\)\.

On the **Application history** tab, two rows are expanded to show the diagnostic summaries for two different Spark applications, and then an **Application ID** is selected to view further application detail:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-app.png)

On the **Jobs** tab of **YARN application** details, the **Description** of **Job 0** is selected to see Job 0 details:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-1.png)

On the **Job 0** details page, information about individual job stages is expanded, and then the **Description** for Stage 1 is selected to see Stage 1 details:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-2.png)

On the **Stage 1** details page, key metrics for stage tasks and executors can be seen, and task and executor logs can be viewed using links:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-3.png)