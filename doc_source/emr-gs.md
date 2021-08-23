# Tutorial: Getting started with Amazon EMR<a name="emr-gs"></a>

## Overview<a name="emr-getting-started-overview"></a>

With Amazon EMR you can set up a cluster to process and analyze data with big data frameworks in just a few minutes\. This tutorial shows you how to launch a sample cluster using Spark, and how to run a simple PySpark script stored in an Amazon S3 bucket\. It covers essential Amazon EMR tasks in three main workflow categories: Plan and Configure, Manage, and Clean Up\.

You'll find links to more detailed topics as you work through the tutorial, and ideas for additional steps in the [Next steps](#emr-gs-next-steps) section\. If you have questions or get stuck, contact the Amazon EMR team on our [Discussion forum](https://forums.aws.amazon.com/forum.jspa?forumID=52)\.

![\[Workflow diagram for Amazon EMR that outlines the three major workflow categories of Plan and Configure, Manage, and Clean Up.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/emr-workflow.png)

**Prerequisites**
+ Before you launch an Amazon EMR cluster, make sure you complete the tasks in [Setting up Amazon EMR](emr-setting-up.md)\.

**Cost**
+ The sample cluster that you create runs in a live environment\. The cluster accrues minimal charges\. To avoid additional charges, make sure you complete the cleanup tasks in the last step of this tutorial\. Charges accrue at the per\-second rate according to Amazon EMR pricing\. Charges also vary by Region\. For more information, see [Amazon EMR pricing](https://aws.amazon.com/emr/pricing)\.
+ Minimal charges might accrue for small files that you store in Amazon S3\. Some or all of the charges for Amazon S3 might be waived if you are within the usage limits of the AWS Free Tier\. For more information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing) and [AWS Free Tier](https://aws.amazon.com/free/)\.

## Step 1: Plan and configure an Amazon EMR cluster<a name="emr-getting-started-plan-and-configure"></a>

### Prepare storage for Amazon EMR<a name="emr-getting-started-prepare-storage"></a>

When you use Amazon EMR, you can choose from a variety of file systems to store input data, output data, and log files\. In this tutorial, you use EMRFS to store data in an S3 bucket\. EMRFS is an implementation of the Hadoop file system that lets you read and write regular files to Amazon S3\. For more information, see [Work with storage and file systems](emr-plan-file-systems.md)\.

To create a bucket for this tutorial, follow the instructions in [How do I create an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service Console User Guide*\. Create the bucket in the same AWS Region where you plan to launch your Amazon EMR cluster\. For example, US West \(Oregon\) us\-west\-2\. 

Buckets and folders that you use with Amazon EMR have the following limitations:
+ Names can consist of lowercase letters, numbers, periods \(\.\), and hyphens \(\-\)\.
+ Names cannot end in numbers\.
+ A bucket name must be unique *across all AWS accounts\.*
+ An output folder must be empty\.

### Prepare an application with input data for Amazon EMR<a name="emr-getting-started-prepare-app"></a>

The most common way to prepare an application for Amazon EMR is to upload the application and its input data to Amazon S3\. Then, when you submit work to your cluster you specify the Amazon S3 locations for your script and data\.

In this step, you upload a sample PySpark script to your Amazon S3 bucket\. We've provided a PySpark script for you to use\. The script processes food establishment inspection data and returns a results file in your S3 bucket\. The results file lists the top ten establishments with the most "Red" type violations\.

You also upload sample input data to Amazon S3 for the PySpark script to process\. The input data is a modified version of Health Department inspection results in King County, Washington, from 2006 to 2020\. For more information, see [King County Open Data: Food Establishment Inspection Data](https://data.kingcounty.gov/Health-Wellness/Food-Establishment-Inspection-Data/f29f-zza5)\. Following are sample rows from the dataset\.

```
name, inspection_result, inspection_closed_business, violation_type, violation_points
100 LB CLAM, Unsatisfactory, FALSE, BLUE, 5
100 PERCENT NUTRICION, Unsatisfactory, FALSE, BLUE, 5
7-ELEVEN #2361-39423A, Complete, FALSE, , 0
```

**To prepare the example PySpark script for EMR**

1. Copy the example code below into a new file in your editor of choice\.

   ```
   import argparse
   
   from pyspark.sql import SparkSession
   
   def calculate_red_violations(data_source, output_uri):
       """
       Processes sample food establishment inspection data and queries the data to find the top 10 establishments
       with the most Red violations from 2006 to 2020.
   
       :param data_source: The URI of your food establishment data CSV, such as 's3://DOC-EXAMPLE-BUCKET/food-establishment-data.csv'.
       :param output_uri: The URI where output is written, such as 's3://DOC-EXAMPLE-BUCKET/restaurant_violation_results'.
       """
       with SparkSession.builder.appName("Calculate Red Health Violations").getOrCreate() as spark:
           # Load the restaurant violation CSV data
           if data_source is not None:
               restaurants_df = spark.read.option("header", "true").csv(data_source)
   
           # Create an in-memory DataFrame to query
           restaurants_df.createOrReplaceTempView("restaurant_violations")
   
           # Create a DataFrame of the top 10 restaurants with the most Red violations
           top_red_violation_restaurants = spark.sql("SELECT name, count(*) AS total_red_violations " +
             "FROM restaurant_violations " +
             "WHERE violation_type = 'RED' " +
             "GROUP BY name " +
             "ORDER BY total_red_violations DESC LIMIT 10 ")
   
           # Write the results to the specified output URI
           top_red_violation_restaurants.write.option("header", "true").mode("overwrite").csv(output_uri)
   
   if __name__ == "__main__":
       parser = argparse.ArgumentParser()
       parser.add_argument(
           '--data_source', help="The URI for you CSV restaurant data, like an S3 bucket location.")
       parser.add_argument(
           '--output_uri', help="The URI where output is saved, like an S3 bucket location.")
       args = parser.parse_args()
   
       calculate_red_violations(args.data_source, args.output_uri)
   ```

1. Save the file as `health_violations.py`\.

1. Upload `health_violations.py` to Amazon S3 into the bucket you created for this tutorial\. For instructions, see [ Uploading an object to a bucket](http://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

**To prepare the sample input data for EMR**

1. Download the zip file, [food\_establishment\_data\.zip](samples/food_establishment_data.zip)\.

1. Unzip and save `food_establishment_data.zip` as `food_establishment_data.csv` on your machine\.

1. Upload the CSV file to the S3 bucket that you created for this tutorial\. For instructions, see [ Uploading an object to a bucket](http://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

For more information about setting up data for EMR, see [Prepare input data](emr-plan-input.md)\.

### Launch an Amazon EMR cluster<a name="emr-getting-started-launch-sample-cluster"></a>

After you prepare a storage location and your application, you can launch a sample Amazon EMR cluster\. In this step, you launch an Apache Spark cluster using the latest [Amazon EMR release version](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-release-components.html)\.

------
#### [ Console ]

**To launch a cluster with Spark installed using **Quick Options****

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster** to open the **Quick Options wizard**\.

1. Note the default values for **Release**, **Instance type**, **Number of instances**, and **Permissions** on the **Create Cluster \- Quick Options** page\. These fields autofill with values that work for general\-purpose clusters\. For more information about the **Quick Options** configuration settings, see [Summary of Quick Options](emr-launch-with-quick-options.md#emr-quick-cluster-options)\.

1. Enter a **Cluster name** to help you identify the cluster\. For example, *My First EMR Cluster*\.

1. Leave **Logging** enabled, but replace the **S3 folder** value with the Amazon S3 bucket you created, followed by **/logs**\. For example, **s3://DOC\-EXAMPLE\-BUCKET/logs**\. Adding **/logs** creates a new folder called 'logs' in your bucket, where EMR can copy the log files of your cluster\.

1. Choose the **Spark** option under **Applications** to install Spark on your cluster\.
**Note**  
Choose the applications you want on your Amazon EMR cluster before you launch the cluster\. You can't add or remove applications from a cluster after launch\.

1. Choose your **EC2 key pair** under **Security and access**\.

1. Choose **Create cluster** to launch the cluster and open the cluster status page\.

1. Find the cluster **Status** next to the cluster name\. The status changes from **Starting** to **Running** to **Waiting** as Amazon EMR provisions the cluster\. You may need to choose the refresh icon on the right or refresh your browser to see status updates\.

Your cluster status changes to **Waiting** when the cluster is up, running, and ready to accept work\. For more information about reading the cluster summary, see [View cluster status and details](emr-manage-view-clusters.md)\. For information about cluster status, see [Understanding the cluster lifecycle](emr-overview.md#emr-overview-cluster-lifecycle)\.

------
#### [ CLI ]

**To launch a cluster with Spark installed using the AWS CLI**

1. Create a Spark cluster with the following command\. Enter a name for your cluster with the `--name` option, and specify the name of your EC2 key pair with the `--ec2-attributes` option\.

   ```
   aws emr create-cluster \
   --name "<My First EMR Cluster>" \
   --release-label <emr-5.33.0> \
   --applications Name=Spark \
   --ec2-attributes KeyName=<myEMRKeyPairName> \
   --instance-type m5.xlarge \
   --instance-count 3 \
   --use-default-roles
   ```

   Note the other required values for `--instance-type`, `--instance-count`, and `--use-default-roles`\. These values have been chosen for general\-purpose clusters\. For more information about `create-cluster`, see the [AWS CLI reference](https://docs.aws.amazon.com/cli/latest/reference/emr/create-cluster.html)\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

   You should see output like the following\. The output shows the `ClusterId` and `ClusterArn` of your new cluster\. Note your `ClusterId`\. You use the `ClusterId` to check on the cluster status and to submit work\.

   ```
   {
       "ClusterId": "myClusterId",
       "ClusterArn": "myClusterArn"
   }
   ```

1. Check your cluster status with the following command\.

   ```
   aws emr describe-cluster --cluster-id <myClusterId>
   ```

   You should see output like the following with the `Status` object for your new cluster\.

   ```
   {
       "Cluster": {
           "Id": "myClusterId",
           "Name": "My First EMR Cluster",
           "Status": {
               "State": "STARTING",
               "StateChangeReason": {
                   "Message": "Configuring cluster software"
               },
               ...
           },
   		...
   	}
   {
   ```

   The `State` value changes from `STARTING` to `RUNNING` to `WAITING` as Amazon EMR provisions the cluster\.

Cluster status changes to `WAITING` when a cluster is up, running, and ready to accept work\. For information about cluster status, see [Understanding the cluster lifecycle](emr-overview.md#emr-overview-cluster-lifecycle)\.

------

## Step 2: Manage your Amazon EMR cluster<a name="emr-getting-started-manage"></a>

### Submit work to Amazon EMR<a name="emr-getting-started-submit-spark-step"></a>

After you launch a cluster, you can submit work to the running cluster to process and analyze data\. You submit work to an Amazon EMR cluster as a *step*\. A step is a unit of work made up of one or more actions\. For example, you might submit a step to compute values, or to transfer and process data\. You can submit steps when you create a cluster, or to a running cluster\. In this part of the tutorial, you submit `health_violations.py` as a step to your running cluster\. To learn more about steps, see [Submit work to a cluster](AddingStepstoaJobFlow.md)\.

------
#### [ Console ]

**To submit a Spark application as a step using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Select the name of your cluster from the **Cluster List**\. The cluster state must be **Waiting**\.

1. Choose **Steps**, and then choose **Add step**\.

1. Configure the step according to the following guidelines:
   + For **Step type**, choose **Spark application**\. You should see additional fields for **Deploy Mode**, **Spark\-submit options**, and **Application location** appear\.
   + For **Name**, leave the default value or type a new name\. If you have many steps in a cluster, naming each step helps you keep track of them\.
   + For **Deploy mode**, leave the default value **Cluster**\. For more information about Spark deployment modes, see [Cluster mode overview](https://spark.apache.org/docs/latest/cluster-overview.html) in the Apache Spark documentation\.
   + Leave the **Spark\-submit options** field blank\. For more information about `spark-submit` options, see [Launching applications with spark\-submit](https://spark.apache.org/docs/latest/submitting-applications.html#launching-applications-with-spark-submit)\. 
   + For **Application location**, enter the location of your `health_violations.py` script in Amazon S3\. For example, *s3://DOC\-EXAMPLE\-BUCKET/health\_violations\.py*\.
   + In the **Arguments** field, enter the following arguments and values:

     ```
     --data_source s3://DOC-EXAMPLE-BUCKET/food_establishment_data.csv
     --output_uri s3://DOC-EXAMPLE-BUCKET/myOutputFolder
     ```

     Replace *s3://DOC\-EXAMPLE\-BUCKET/food\_establishment\_data\.csv* with the S3 URI of the input data you prepared in [Prepare an application with input data for Amazon EMR](#emr-getting-started-prepare-app)\.

     Replace *DOC\-EXAMPLE\-BUCKET* with the name of the bucket you created for this tutorial, and *myOutputFolder* with a name for your cluster output folder\.
   + For **Action on failure**, accept the default option **Continue** so that if the step fails, the cluster continues to run\.

1. Choose **Add** to submit the step\. The step should appear in the console with a status of **Pending**\.

1. Check for the step status to change from **Pending** to **Running** to **Completed**\. To refresh the status in the console, choose the refresh icon to the right of the **Filter**\. The script takes about one minute to run\.

You will know that the step finished successfully when the status changes to **Completed**\.

------
#### [ CLI ]

**To submit a Spark application as a step using the AWS CLI**

1. Make sure you have the `ClusterId` of the cluster you launched in [Launch an Amazon EMR cluster](#emr-getting-started-launch-sample-cluster)\. You can also retrieve your cluster ID with the following command\.

   ```
   aws emr list-clusters --cluster-states WAITING							
   ```

1.  Submit `health_violations.py` as a step with the `add-steps` command and your `ClusterId`\.
   + You can specify a name for your step by replacing *"My Spark Application"*\. In the `Args` array, replace *s3://DOC\-EXAMPLE\-BUCKET/health\_violations\.py* with the location of your `health_violations.py` application\.
   + Replace *s3://DOC\-EXAMPLE\-BUCKET/food\_establishment\_data\.csv* with the S3 location of your `food_establishment_data.csv` dataset\.
   + Replace *s3://DOC\-EXAMPLE\-BUCKET/MyOutputFolder* with the S3 path of your designated bucket and a name for your cluster output folder\.
   + `ActionOnFailure=CONTINUE` means the cluster continues to run if the step fails\.

   ```
   aws emr add-steps \
   --cluster-id <myClusterId> \
   --steps Type=Spark,Name="<My Spark Application>",ActionOnFailure=CONTINUE,Args=[<s3://DOC-EXAMPLE-BUCKET/health_violations.py>,--data_source,<s3://DOC-EXAMPLE-BUCKET/food_establishment_data.csv>,--output_uri,<s3://DOC-EXAMPLE-BUCKET/MyOutputFolder>]
   ```

   For more information about submitting steps using the CLI, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/emr/add-steps.html)\.

   After you submit the step, you should see output like the following with a list of `StepIds`\. Since you submitted one step, you will see just one ID in the list\. Copy your step ID\. You use your step ID to check the status of the step\.

   ```
   {
       "StepIds": [
           "s-1XXXXXXXXXXA"
       ]
   }
   ```

1. Query the status of your step with the `describe-step` command\.

   ```
   aws emr describe-step --cluster-id <myClusterId> --step-id <s-1XXXXXXXXXXA>
   ```

   You should see output like the following with information about your step\.

   ```
    
   {
       "Step": {
           "Id": "s-1XXXXXXXXXXA",
           "Name": "My Spark Application",
           "Config": {
               "Jar": "command-runner.jar",
               "Properties": {},
               "Args": [
                   "spark-submit",
                   "s3://DOC-EXAMPLE-BUCKET/health_violations.py",
                   "--data_source",
                   "s3://DOC-EXAMPLE-BUCKET/food_establishment_data.csv",
                   "--output_uri",
                   "s3://DOC-EXAMPLE-BUCKET/myOutputFolder"
               ]
           },
           "ActionOnFailure": "CONTINUE",
           "Status": {
               "State": "COMPLETED",
               ...
           }
       }
   }
   ```

   The `State` of the step changes from `PENDING` to `RUNNING` to `COMPLETED` as the step runs\. The step takes about one minute to run, so you might need to check the status a few times\.

You will know that the step was successful when the `State` changes to `COMPLETED`\.

------

For more information about the step lifecycle, see [Running steps to process data](emr-overview.md#emr-overview-steps)\.

### View results<a name="emr-getting-started-view-results"></a>

After a step runs successfully, you can view its output results in your Amazon S3 output folder\.

**To view the results of `health_violations.py`**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **Bucket name** and then the output folder that you specified when you submitted the step\. For example, *DOC\-EXAMPLE\-BUCKET* and then *myOutputFolder*\. 

1. Verify that the following items appear in your output folder:
   + A small\-sized object called `_SUCCESS`\.
   + A CSV file starting with the prefix `part-` that contains your results\.

1. Choose the object with your results, then choose **Download** to save the results to your local file system\.

1. Open the results in your editor of choice\. The output file lists the top ten food establishments with the most red violations\. The output file also shows the total number of red violations for each establishment\.

   The following is an example of `health_violations.py` results\.

   ```
   name, total_red_violations
   SUBWAY, 322
   T-MOBILE PARK, 315
   WHOLE FOODS MARKET, 299
   PCC COMMUNITY MARKETS, 251
   TACO TIME, 240
   MCDONALD'S, 177
   THAI GINGER, 153
   SAFEWAY INC #1508, 143
   TAQUERIA EL RINCONSITO, 134
   HIMITSU TERIYAKI, 128
   ```

For more information about Amazon EMR cluster output, see [Configure an output location](emr-plan-output.md)\.

### \(Optional\) Connect to your running Amazon EMR cluster<a name="emr-getting-started-connect-to-cluster"></a>

When you use Amazon EMR, you may want to connect to a running cluster to read log files, debug the cluster, or use CLI tools like the Spark shell\. Amazon EMR lets you connect to a cluster using the Secure Shell \(SSH\) protocol\. This section covers how to configure SSH, connect to your cluster, and view log files for Spark\. For more information about connecting to a cluster, see [Authenticate to Amazon EMR cluster nodes](emr-authenticate-cluster-connections.md)\.

#### Authorize SSH connections to your cluster<a name="emr-getting-started-restrict-ssh"></a>

Before you connect to your cluster, you need to modify your cluster security groups to authorize inbound SSH connections\. Amazon EC2 security groups act as virtual firewalls to control inbound and outbound traffic to your cluster\. When you created your cluster for this tutorial, Amazon EMR created the following security groups on your behalf:

**ElasticMapReduce\-master**  
The default Amazon EMR managed security group associated with the master node\. In an Amazon EMR cluster, the master node is an Amazon EC2 instance that manages the cluster\.

**ElasticMapReduce\-slave**  
The default security group associated with core and task nodes\.

**To allow SSH access for trusted sources for the ElasticMapReduce\-master security group**

To edit your security groups, you must have permission to manage security groups for the VPC that the cluster is in\. For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) and the [Example Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_ec2_securitygroups-vpc.html) that allows managing EC2 security groups in the *IAM User Guide*\.

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Clusters**\.

1. Choose the **Name** of the cluster you want to modify\.

1. Choose the **Security groups for Master** link under **Security and access**\.

1. Choose **ElasticMapReduce\-master** from the list\.

1. Choose the **Inbound rules** tab and then **Edit inbound rules**\.

1. Check for an inbound rule that allows public access with the following settings\. If it exists, choose **Delete** to remove it\.
   + **Type**

     SSH
   + **Port**

     22
   + **Source**

     Custom 0\.0\.0\.0/0
**Warning**  
Before December 2020, the ElasticMapReduce\-master security group had a pre\-configured rule to allow inbound traffic on Port 22 from all sources\. This rule was created to simplify initial SSH connections to the master node\. We strongly recommend that you remove this inbound rule and restrict traffic to trusted sources\.

1. Scroll to the bottom of the list of rules and choose **Add Rule**\.

1. For **Type**, select **SSH**\.

   Selecting SSH automatically enters **TCP** for **Protocol** and **22** for **Port Range**\.

1. For source, select **My IP** to automatically add your IP address as the source address\. You can also add a range of **Custom** trusted client IP addresses, or create additional rules for other clients\. Many network environments dynamically allocate IP addresses, so you might need to update your IP addresses for trusted clients in the future\.

1. Choose **Save**\.

1. Optionally, choose **ElasticMapReduce\-slave** from the list and repeat the steps above to allow SSH client access to core and task nodes\.

#### Connect to your cluster using the AWS CLI<a name="emr-getting-started-connect-ssh"></a>

Regardless of your operating system, you can create an SSH connection to your cluster using the AWS CLI\.

**To connect to your cluster an view log files using the AWS CLI**

1. Use the following command to open an SSH connection to your cluster\. Replace *<mykeypair\.key>* with the full path and file name of your key pair file\. For example, `C:\Users\<username>\.ssh\mykeypair.pem`\.

   ```
   aws emr ssh --cluster-id <j-2AL4XXXXXX5T9> --key-pair-file <~/mykeypair.key>						
   ```

1. Navigate to `/mnt/var/log/spark` to access the Spark logs on your cluster's master node\. Then view the files in that location\. For a list of additional log files on the master node, see [View log files on the master node](emr-manage-view-web-log-files.md#emr-manage-view-web-log-files-master-node)\.

   ```
   cd /mnt/var/log/spark
   ls
   ```

## Step 3: Clean up your Amazon EMR resources<a name="emr-getting-started-clean-up"></a>

### Terminate your cluster<a name="emr-getting-started-stop-cluster"></a>

Now that you've submitted work to your cluster and viewed the results of your PySpark application, you can terminate the cluster\. Terminating a cluster stops all of the cluster's associated Amazon EMR charges and Amazon EC2 instances\.

When you terminate a cluster, Amazon EMR retains metadata about the cluster for two months at no charge\. Archived metadata helps you [clone the cluster](clone-console.md) for a new job or revisit the cluster configuration for reference purposes\. Metadata does *not* include data that the cluster writes to S3, or data stored in HDFS on the cluster\. 

**Note**  
The Amazon EMR console does not let you delete a cluster from the list view after you terminate the cluster\. A terminated cluster disappears from the console when Amazon EMR clears its metadata\. 

------
#### [ Console ]

**To terminate the cluster using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Clusters**, then choose the cluster you want to terminate\. For example, *My First EMR Cluster*\.

1. Choose **Terminate** to open the **Terminate cluster** prompt\.

1. Choose **Terminate** in the open prompt\. Depending on the cluster configuration, termination may take 5 to 10 minutes\. For more information about terminating Amazon EMR clusters, see [Terminate a cluster](UsingEMR_TerminateJobFlow.md)\.
**Note**  
If you followed the tutorial closely, termination protection should be off\. Cluster termination protection prevents accidental termination\. If termination protection is on, you will see a prompt to change the setting before terminating the cluster\. Choose **Change**, then **Off**\.

------
#### [ CLI ]

**To terminate the cluster using the AWS CLI**

1. Initiate the cluster termination process with the following command\. Replace *<myClusterId>* with the ID of your sample cluster\. The command does not return output\.

   ```
   aws emr terminate-clusters --cluster-ids <myClusterId>
   ```

1. To check that the cluster termination process is in progress, check the cluster status with the following command\.

   ```
   aws emr describe-cluster --cluster-id <myClusterId>									
   ```

   Following is example output in JSON format\. The cluster `Status` should change from **`TERMINATING`** to **`TERMINATED`**\. Termination may take 5 to 10 minutes depending on your cluster configuration\. For more information about terminating an Amazon EMR cluster, see [Terminate a cluster](UsingEMR_TerminateJobFlow.md)\.

   ```
   {
       "Cluster": {
           "Id": "j-xxxxxxxxxxxxx",
           "Name": "My Cluster Name",
           "Status": {
               "State": "TERMINATED",
               "StateChangeReason": {
                   "Code": "USER_REQUEST",
                   "Message": "Terminated by user request"
               },
                   ...
           },
               ...
       },
           ...
   }
   ```

------

### Delete S3 resources<a name="emr-getting-started-delete-bucket"></a>

To avoid additional charges, you should delete your Amazon S3 bucket\. Deleting the bucket removes all of the Amazon S3 resources for this tutorial\. Your bucket should contain:
+ The PySpark script
+ The input dataset
+ Your output results folder
+ Your log files folder

You might need to take extra steps to delete stored files if you saved your PySpark script or output in a different location\.

**Note**  
Your cluster must be terminated before you delete your bucket\. Otherwise, you may not be allowed to empty the bucket\.

To delete your bucket, follow the instructions in [How do I delete an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/delete-bucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

## Next steps<a name="emr-gs-next-steps"></a>

You have now launched your first Amazon EMR cluster from start to finish\. You have also completed essential EMR tasks like preparing and submitting big data applications, viewing results, and terminating a cluster\. 

Use the following topics to learn more about how you can customize your Amazon EMR workflow\.

### Explore big data applications for Amazon EMR<a name="emr-gs-next-explore-apps"></a>

Discover and compare the big data applications you can install on a cluster in the [Amazon EMR Release Guide](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-release-components.html)\. The Release Guide details each EMR release version and includes tips for using frameworks such as Spark and Hadoop on Amazon EMR\.

### Plan cluster hardware, networking, and security<a name="emr-gs-next-plan-clusters"></a>

In this tutorial, you created a simple EMR cluster without configuring advanced options\. Advanced options let you specify Amazon EC2 instance types, cluster networking, and cluster security\. For more information about planning and launching a cluster that meets your requirements, see [Plan and configure clusters](emr-plan.md) and [Security in Amazon EMR](emr-security.md)\.

### Manage clusters<a name="emr-gs-next-manage-clusters"></a>

Dive deeper into working with running clusters in [Manage clusters](emr-manage.md)\. To manage a cluster, you can connect to the cluster, debug steps, and track cluster activities and health\. You can also adjust cluster resources in response to workload demands with [EMR managed scaling](emr-managed-scaling.md)\.

### Use a different interface<a name="emr-gs-next-interfaces"></a>

In addition to the Amazon EMR console, you can manage Amazon EMR using the AWS Command Line Interface, the web service API, or one of the many supported AWS SDKs\. For more information, see [Management interfaces](emr-overview-benefits.md#emr-what-tools)\.

You can also interact with applications installed on Amazon EMR clusters in many ways\. Some applications like Apache Hadoop publish web interfaces that you can view\. For more information, see [View web interfaces hosted on Amazon EMR clusters](emr-web-interfaces.md)\.

### Browse the EMR technical blog<a name="emr-gs-next-browse-blogs"></a>

For sample walkthroughs and in\-depth technical discussion of new Amazon EMR features, see the [AWS big data blog](http://aws.amazon.com/blogs/big-data/tag/amazon-emr/)\.