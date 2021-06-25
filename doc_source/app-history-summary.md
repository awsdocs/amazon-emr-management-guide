# View a high\-level application history<a name="app-history-summary"></a>

With Amazon EMR versions 5\.8\.0 and later, you can view a high\-level application history from the **Application user interfaces** tab in the Amazon EMR console\. Amazon EMR keeps the summary of application history for seven days after an application has completed\. 

## Considerations and limitations<a name="app-history-limitations"></a>

Consider the following limitations when you use the **Application user interfaces** tab in the Amazon EMR console\.
+ Amazon EMR only supports the high\-level application history feature for Amazon EMR versions 5\.8\.0 and later\.
+ The high\-level application history feature does not support Spark Streaming applications\.

## Example: View a high\-level application history<a name="app-history-example"></a>

The following sequence demonstrates a drill\-down through a Spark or YARN application into job details using the **Application user interfaces** tab on the cluster details page\. 

To view cluster details, select a cluster **Name** from the **Clusters** list\. To view information about YARN container logs, you must enable logging for your cluster\. For more information, see [Configure cluster logging and debugging](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-debugging.html)\. For Spark application history, the information provided in the summary table is only a subset of the information available through the Spark history server UI\.

In the **Application user interfaces** tab under **High\-level application history**, you can expand a row to show the diagnostic summary for a Spark application or select an **Application ID** link to view details about a different application\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-app.png)

When you select an **Application ID** link, the UI changes to show the **YARN application** details for that application\. In the **Jobs** tab of **YARN application** details, you can choose the **Description** link for a job to display details for that job\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-1.png)

On the job details page, you can expand information about individual job stages, and then select the **Description** link to see stage details\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-2.png)

On the stage details page, you can view key metrics for stage tasks and executors\. You can also view task and executor logs using the **View logs** links\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/app-history-job-3.png)