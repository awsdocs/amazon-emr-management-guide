# Configure Consistency Notifications for CloudWatch and Amazon SQS<a name="emrfs-configure-sqs-cw"></a>

You can enable CloudWatch metrics and Amazon SQS messages in EMRFS for Amazon S3 eventual consistency issues\. 

**CloudWatch**  
When CloudWatch metrics are enabled, a metric named **Inconsistency** is pushed each time a `FileSystem` API call fails due to Amazon S3 eventual consistency\. 

**To view CloudWatch metrics for Amazon S3 eventual consistency issues**

To view the **Inconsistency** metric in the CloudWatch console, select the EMRFS metrics and then select a **JobFlowId**/**Metric Name** pair\. For example: `j-162XXXXXXM2CU ListStatus`, `j-162XXXXXXM2CU GetFileStatus`, and so on\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the **Dashboard**, in the **Metrics** section, choose **EMRFS**\. 

1. In the **Job Flow Metrics** pane, select one or more **JobFlowId**/**Metric Name** pairs\. A graphical representation of the metrics appears in the window below\.

**Amazon SQS**  
When Amazon SQS notifications are enabled, an Amazon SQS queue with the name `EMRFS-Inconsistency-<jobFlowId>` is created when EMRFS is initialized\. Amazon SQS messages are pushed into the queue when a `FileSystem` API call fails due to Amazon S3 eventual consistency\. The message contains information such as JobFlowId, API, a list of inconsistent paths, a stack trace, and so on\. Messages can be read using the Amazon SQS console or using the EMRFS `read-sqs` command\.

**To manage Amazon SQS messages for Amazon S3 eventual consistency issues**

Amazon SQS messages for Amazon S3 eventual consistency issues can be read using the EMRFS CLI\. To read messages from an EMRFS Amazon SQS queue, type the `read-sqs` command and specify an output location on the master node's local file system for the resulting output file\. 

You can also delete an EMRFS Amazon SQS queue using the `delete-sqs` command\.

1. To read messages from an Amazon SQS queue, type the following command\. Replace *queuename* with the name of the Amazon SQS queue that you configured and replace */path/filename* with the path to the output file:

   ```
   emrfs read-sqs --queue-name queuename --output-file /path/filename
   ```

   For example, to read and output Amazon SQS messages from the default queue, type:

   ```
   emrfs read-sqs --queue-name EMRFS-Inconsistency-j-162XXXXXXM2CU --output-file /path/filename
   ```
**Note**  
You can also use the `-q` and `-o` shortcuts instead of `--queue-name` and `--output-file` respectively\.

1. To delete an Amazon SQS queue, type the following command:

   ```
   emrfs delete-sqs --queue-name queuename
   ```

   For example, to delete the default queue, type:

   ```
   emrfs delete-sqs --queue-name EMRFS-Inconsistency-j-162XXXXXXM2CU
   ```
**Note**  
You can also use the `-q` shortcut instead of `--queue-name`\.