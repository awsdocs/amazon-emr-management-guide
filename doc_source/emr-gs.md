# Tutorial: Getting Started with Amazon EMR<a name="emr-gs"></a>

## Overview<a name="emr-getting-started-overview"></a>

With Amazon EMR, you can set up a cluster to process and analyze data with big data frameworks in just a few minutes\. This tutorial shows you how to launch a sample cluster using Spark, and how to run a simple PySpark script that you'll store in an Amazon S3 bucket\. It covers essential Amazon EMR tasks in three main workflow categories: Plan and Configure, Manage, and Clean Up\. You can also adapt this process for your own workloads\.

![\[Workflow diagram for Amazon EMR that outlines the three major workflow categories of Plan and Configure, Manage, and Clean Up.\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/emr-workflow.png)

**Prerequisites**
+ Before you launch an Amazon EMR cluster, make sure you complete the tasks in [Setting Up Amazon EMR](emr-setting-up.md)\.

**This tutorial introduces you to the following Amazon EMR tasks:**
+ [Step 1: Plan and Configure](#emr-getting-started-plan-and-configure)
  + [Prepare Storage for Cluster Input and Output](#emr-getting-started-prepare-storage)
  + [Develop and Prepare an Application for Amazon EMR](#emr-getting-started-prepare-app)
  + [Launch an Amazon EMR Cluster](#emr-getting-started-launch-sample-cluster)
+ [Step 2: Manage](#emr-getting-started-manage)
  + [Submit Work to Amazon EMR](#emr-getting-started-submit-spark-step)
  + [View Results](#emr-getting-started-view-results)
  + [\(Optional\) Set Up Cluster Connections](#emr-getting-started-connect-to-cluster)
+ [Step 3: Clean Up](#emr-getting-started-clean-up)
  + [Shut down your cluster](#emr-getting-started-stop-cluster)
  + [Delete S3 resources](#emr-getting-started-delete-bucket)

You'll find links to more detailed topics as you work through this tutorial, and ideas for additional steps in the [Next Steps](#emr-gs-next-steps) section\. If you have questions or get stuck, contact the Amazon EMR team on our [Discussion Forum](https://forums.aws.amazon.com/forum.jspa?forumID=52)\.

**Cost**
+ The sample cluster that you create runs in a live environment\. The cluster will accrue minimal charges and will only run for the duration of this tutorial as long as you complete the cleanup tasks\. Charges accrue at the per\-second rate for Amazon EMR pricing and vary by Region\. For more information, see [Amazon EMR Pricing](https://aws.amazon.com/emr/pricing)\.
+ Minimal charges might also accrue for small files that you store in Amazon S3 for the tutorial\. Some or all of your charges for Amazon S3 might be waived if you are within the usage limits of the AWS Free Tier\. For more information, see [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing) and [AWS Free Tier](https://aws.amazon.com/free/)\.

## Step 1: Plan and Configure an Amazon EMR Cluster<a name="emr-getting-started-plan-and-configure"></a>

In this step, you plan for and launch a simple Amazon EMR cluster with Apache Spark installed\. The setup process includes creating an Amazon S3 bucket to store a sample PySpark script, an input dataset, and cluster output\.

### Prepare Storage for Cluster Input and Output<a name="emr-getting-started-prepare-storage"></a>

Create an Amazon S3 bucket to store an example PySpark script, input data, and output data\. Create the bucket in the same AWS Region where you plan to launch your Amazon EMR cluster\. For example, US West \(Oregon\) us\-west\-2\. Buckets and folders that you use with Amazon EMR have the following limitations:
+ Names can consist of only lowercase letters, numbers, periods \(\.\), and hyphens \(\-\)\.
+ Names cannot end in numbers\.
+ A bucket name must be unique *across all AWS accounts\.*
+ An output folder must be empty\.

To create a bucket for this tutorial, see [How do I create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html) in the *Amazon Simple Storage Service Console User Guide*\.

### Develop and Prepare an Application for Amazon EMR<a name="emr-getting-started-prepare-app"></a>

In this step, you upload a sample PySpark script to Amazon S3\. This is the most common way to prepare an application for Amazon EMR\. EMR lets you specify the Amazon S3 location of the script when you submit work to your cluster\. You also upload sample input data to Amazon S3 for the PySpark script to process\.

We've provided the following PySpark script for you to use\. The script processes food establishment inspection data and outputs a file listing the top ten establishments with the most "Red" type violations to your S3 bucket\. 

**To prepare the example PySpark script for EMR**

1. Copy the example code below into a new file in your editor of choice\.

1. Save the file as `health_violations.py`\.

1. Upload `health_violations.py` to Amazon S3 into the bucket you designated for this tutorial\. For information about how to upload objects to Amazon S3, see [ Uploading an object to a bucket](http://docs.aws.amazon.com/AmazonS3/latest/gsg/PuttingAnObjectInABucket.html) in the *Amazon Simple Storage Service Getting Started Guide*\.

```
import argparse

from pyspark.sql import SparkSession

def calculate_red_violations(data_source, output_uri):
    """
    Processes sample food establishment inspection data and queries the data to find the top 10 establishments
    with the most Red violations from 2006 to 2020.

    :param data_source: The URI where the food establishment data CSV is saved, typically
			  an Amazon S3 bucket, such as 's3://DOC-EXAMPLE-BUCKET/food-establishment-data.csv'.
    :param output_uri: The URI where the output is written, typically an Amazon S3
                       bucket, such as 's3://DOC-EXAMPLE-BUCKET/restaurant_violation_results'.
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
        '--data_source', help="The URI where the CSV restaurant data is saved, typically an S3 bucket.")
    parser.add_argument(
        '--output_uri', help="The URI where output is saved, typically an S3 bucket.")
    args = parser.parse_args()

    calculate_red_violations(args.data_source, args.output_uri)
```

**Input arguments**

You must include values for the following arguments when you run the PySpark script as a step\.
+ `--data_source` – The Amazon S3 URI of the food establishment data CSV file\. You will prepare this file below\.
+ `--output_uri` – The URI of the Amazon S3 bucket where the output results will be saved\.

The input data is a modified version of a publicly available food establishment inspection dataset with Health Department inspection results in King County, Washington, from 2006 to 2020\. For more information, see [King County Open Data: Food Establishment Inspection Data](https://data.kingcounty.gov/Health-Wellness/Food-Establishment-Inspection-Data/f29f-zza5)\. Following are sample rows from the dataset\.

```
name, inspection_result, inspection_closed_business, violation_type, violation_points
100 LB CLAM, Unsatisfactory, FALSE, BLUE, 5
100 PERCENT NUTRICION, Unsatisfactory, FALSE, BLUE, 5
7-ELEVEN #2361-39423A, Complete, FALSE, , 0
```

**To prepare the sample input data for EMR**

1. Download the zip file, [food\_establishment\_data\.zip](samples/food_establishment_data.zip)\.

1. Unzip the content and save it locally as `food_establishment_data.csv`\.

1. Upload the CSV file to the S3 bucket that you created for this tutorial\. For step\-by\-step instructions, see [How do I upload files and folders to an S3 bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-objects.html) in the *Amazon Simple Storage Service Console User Guide*\.

For more information about setting up data for EMR, see [Prepare Input Data](emr-plan-input.md)\.

### Launch an Amazon EMR Cluster<a name="emr-getting-started-launch-sample-cluster"></a>

Now that you've completed the prework, you can launch a sample cluster with Apache Spark installed using the latest [Amazon EMR release](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-release-components.html)\.

**Note**  
If you created your AWS account after December 04, 2013, Amazon EMR sets up a cluster in the [default Amazon Virtual Private Cloud \(VPC\)](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) for your selected Region when none is specified\.

------
#### [ Console ]

**To launch a cluster with Spark installed using **Quick Options****

1. Sign in to the AWS Management Console and open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster** to open the **Quick Options wizard**\.

1. On the **Create Cluster \- Quick Options** page, note the default values for **Release**, **Instance type**, **Number of instances**, and **Permissions**\. These fields autopopulate with values chosen for general\-purpose clusters\. For more information about the **Quick Options** configuration settings, see [Summary of Quick Options](emr-launch-with-quick-options.md#emr-quick-cluster-options)\.

1. Change the following fields: 
   + Enter a **Cluster name** to help you identify the cluster\. For example, *My First EMR Cluster*\.
   + Leave **Logging** enabled, but replace the **S3 folder** value with the Amazon S3 bucket you created, followed by **/logs**\. For example, **s3://DOC\-EXAMPLE\-BUCKET/logs**\. This will create a new folder called 'logs' in your bucket, where EMR will copy the log files of your cluster\.
   + Under **Applications**, choose the **Spark** option\. **Quick Options** lets you select from the most common application combinations to install on your cluster\.
   + Under **Security and access**, choose the **EC2 key pair** that you designated or created in [Create an Amazon EC2 Key Pair for SSH](emr-setting-up.md#emr-setting-up-key-pair)\.

1. Choose **Create cluster** to launch the cluster and open the cluster status page\.

1. On the cluster status page, find the **Status** next to the cluster name\. The status should change from **Starting** to **Running** to **Waiting** during the cluster creation process\. You may need to choose the refresh icon on the right or refresh your browser to receive updates\.

When the status progresses to **Waiting**, your cluster is up, running, and ready to accept work\.

------
#### [ CLI ]

**To launch a cluster with Spark installed using the AWS CLI**

1. Create a Spark cluster with the following command\. Enter a name for your cluster with the `--name` option, and specify the name of your EC2 key pair with the `--ec2-attributes` option\.

   ```
   aws emr create-cluster \
   --name "My First EMR Cluster" \
   --release-label emr-5.32.0 \
   --applications Name=Spark \
   --ec2-attributes KeyName=myEMRKeyPairName \
   --instance-type m5.xlarge \
   --instance-count 3 \
   --use-default-roles
   ```

   Note the other required values for `--instance-type`, `--instance-count`, and `--use-default-roles`\. These values have been chosen for general\-purpose clusters\. For information about `create-cluster` used here, see the [AWS CLI reference](https://docs.aws.amazon.com/cli/latest/reference/emr/create-cluster.html)\.
**Note**  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

   You should see output that includes the `ClusterId` and `ClusterArn` of your new cluster\. Note your `ClusterId`, which you will use to check on the cluster status and later to submit work\. Following is an example of `create-cluster` output in JSON format\.

   ```
   {
       "ClusterId": "myClusterId",
       "ClusterArn": "myClusterArn"
   }
   ```

1. Check your cluster status with the following command\.

   ```
   aws emr describe-cluster --cluster-id myClusterId
   ```

   You should see output with the `Status` of your new cluster\. Following is an example of `describe-cluster` output in JSON format\.

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

   The status `State` should change from `STARTING` to `RUNNING` to `WAITING` during the cluster creation process\.

When the cluster status progresses to `WAITING`, your cluster is up, running, and ready to accept work\.

------

For more information about reading the cluster summary, see [View Cluster Status and Details](emr-manage-view-clusters.md)\. For information about cluster status, see [Understanding the Cluster Lifecycle](emr-overview.md#emr-overview-cluster-lifecycle)\.

## Step 2: Manage Amazon EMR Clusters<a name="emr-getting-started-manage"></a>

Now that your cluster is up and running, you can connect to it and manage it\. You can also submit work to your running cluster to process and analyze data\.

### Submit Work to Amazon EMR<a name="emr-getting-started-submit-spark-step"></a>

With your cluster up and running, you can submit `health_violations.py` as a *step*\. A step is a unit of cluster work made up of one or more jobs\. For example, you might submit a step to compute values, or to transfer and process data\. 

You can submit multiple steps to accomplish a set of tasks on a cluster when you create the cluster, or after it's already running\. For more information, see [Submit Work to a Cluster](AddingStepstoaJobFlow.md)\.

------
#### [ Console ]

**To submit a Spark application as a step using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. In **Cluster List**, select the name of your cluster\. Make sure the cluster is in a **Waiting** state\.

1. Choose **Steps**, and then choose **Add step**\.

1. Configure the step according to the following guidelines:
   + For **Step type**, choose **Spark application**\. You should see additional fields for **Deploy Mode**, **Spark\-submit options**, and **Application location** appear\.
   + For **Name**, leave the default value or type a new name\. If you have many steps in a cluster, naming each step helps you keep track of them\.
   + For **Deploy mode**, leave the default value **Cluster**\. For more information about Spark deployment modes, see [Cluster Mode Overview](https://spark.apache.org/docs/latest/cluster-overview.html) in the Apache Spark documentation\.
   + Leave the **Spark\-submit options** field blank\. For more information about `spark-submit` options, see [Launching Applications with spark\-submit](https://spark.apache.org/docs/latest/submitting-applications.html#launching-applications-with-spark-submit)\. 
   + For **Application location**, enter the location of your `health_violations.py` script in Amazon S3\. For example, *s3://DOC\-EXAMPLE\-BUCKET/health\_violations\.py*\.
   + In the **Arguments** field, enter the following arguments and values:

     ```
     --data_source s3://DOC-EXAMPLE-BUCKET/food_establishment_data.csv
     --output_uri s3://DOC-EXAMPLE-BUCKET/myOutputFolder
     ```

     Replace *s3://DOC\-EXAMPLE\-BUCKET/food\_establishment\_data\.csv* with the S3 URI of the input data you prepared in [Develop and Prepare an Application for Amazon EMR](#emr-getting-started-prepare-app)\.

     Replace *DOC\-EXAMPLE\-BUCKET* with the name of the bucket you created for this tutorial, and *myOutputFolder* with a name for your cluster output folder\.
   + For **Action on failure**, accept the default option **Continue** so that if the step fails, the cluster continues to run\.

1. Choose **Add** to submit the step\. The step should appear in the console with a status of **Pending**\.

1. The status of the step should change from **Pending** to **Running** to **Completed** as it runs\. To update the status in the console, choose the refresh icon to the right of the **Filter**\. The script takes approximately one minute to run\.

You will know that the step finished successfully when the status changes to **Completed**\.

------
#### [ CLI ]

**To submit a Spark application as a step using the AWS CLI**

1.  Make sure you have the `ClusterId` of the cluster you launched in [Launch an Amazon EMR Cluster](#emr-getting-started-launch-sample-cluster)\. You can also retrieve your cluster ID with the following command\.

   ```
   aws emr list-clusters --cluster-states WAITING
   ```

1.  Submit `health_violations.py` as a step with the `add-steps` command with your `ClusterId`\.
   + You can specify a name for your step by replacing *"My Spark Application"*\. In the `Args` array, replace *s3://DOC\-EXAMPLE\-BUCKET/health\_violations\.py* with the location of your `health_violations.py` application\.
   + Replace *s3://DOC\-EXAMPLE\-BUCKET/food\_establishment\_data\.csv* with the S3 location of your `food_establishment_data.csv` dataset\.
   + Replace *s3://DOC\-EXAMPLE\-BUCKET/MyOutputFolder* with the S3 path of your designated bucket and a name for your cluster output folder\.
   + `ActionOnFailure=CONTINUE` means the cluster will continue running if the step fails\.

   ```
   aws emr add-steps \
   --cluster-id myClusterId \
   --steps Type=Spark,Name="My Spark Application",ActionOnFailure=CONTINUE,Args=[s3://DOC-EXAMPLE-BUCKET/health_violations.py,--data_source,s3://DOC-EXAMPLE-BUCKET/food_establishment_data.csv,--output_uri,s3://DOC-EXAMPLE-BUCKET/MyOutputFolder]
   ```

   For more information about submitting steps using the CLI, see the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/emr/add-steps.html)\.

   After you submit the step, you should see output with a list of `StepIds`\. Since you submitted one step, there should be just one ID in the list\. Copy your step ID, which you will use to check the status of the step\.

   Following is an example of console output in JSON format that you should see after you submit a step\.

   ```
   {
       "StepIds": [
           "s-1XXXXXXXXXXA"
       ]
   }
   ```

1. Query the status of the step with your step ID and the `describe-step` command\. Replace *myClusterId* with your cluster ID\. 

   ```
   aws emr describe-step --cluster-id myClusterId --step-id s-1XXXXXXXXXXA
   ```

   You should see output with information about your step, as well as a `Status` section\. The following is example `describe-step` output in JSON format\.

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

   The `State` of the step changes from `PENDING` to `RUNNING` to `COMPLETED` as the step runs\. The step takes approximately one minute to run, so you might need to check the status a few times\.

You will know that the step was successful when the `State` changes to `COMPLETED`\.

------

For more information about the step lifecycle, see [Running Steps to Process Data](emr-overview.md#emr-overview-steps)\.

### View Results<a name="emr-getting-started-view-results"></a>

After a step runs successfully, you can view its output results in the Amazon S3 output folder you specified when you submitted the step\.

**To view the results of `health_violations.py`**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose the **Bucket name** and then the output folder that you specified when you submitted the step\. For example, *DOC\-EXAMPLE\-BUCKET* and then *myOutputFolder*\. 

1. Verify that the following items are in your output folder:
   + A small\-sized object called `_SUCCESS`, indicating the success of your step\.
   + A CSV file starting with the prefix `part-`\. This is the object with your results\.

1. Choose the object with your results, then choose **Download** to save it to your local file system\.

1. Open the results in your editor of choice\. The output file lists the top ten food establishments with the most Red violations\.

   Following is an example of `health_violations.py` results\.

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

For more information about Amazon EMR cluster output, see [Configure an Output Location](emr-plan-output.md)\.

### \(Optional\) Set Up Cluster Connections<a name="emr-getting-started-connect-to-cluster"></a>

This step is not required, but you have the option to connect to cluster nodes with Secure Shell \(SSH\) for tasks like issuing commands, running applications interactively, and reading log files\.

#### Configure security group rules<a name="emr-getting-started-restrict-ssh"></a>

Before you connect to your cluster, you must set up a Port 22 inbound rule to allow SSH connections\.

Security groups act as virtual firewalls to control inbound and outbound traffic to your cluster\. When you create a cluster with the default security groups, Amazon EMR creates the following groups:

**ElasticMapReduce\-master**  
The default Amazon EMR\-managed security group associated with the master instance\.

**ElasticMapReduce\-slave**  
The default security group associated with core and task nodes\.

**To allow SSH access for trusted sources for the ElasticMapReduce\-master security group**

You must first be logged in to AWS as a root user or as an IAM principal that is allowed to manage security groups for the VPC that the cluster is in\. For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) and the [Example Policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_ec2_securitygroups-vpc.html) that allows managing EC2 security groups in the *IAM User Guide*\.

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Clusters**\.

1. Choose the **Name** of the cluster\.

1. Under **Security and access** choose the **Security groups for Master** link\.

1. Choose **ElasticMapReduce\-master** from the list\.

1. Choose **Inbound**, **Edit**\.

1. Check for an inbound rule that allows public access with the following settings\. If it exists, choose **Delete** to remove it\.
   + **Type**

     SSH
   + **Port**

     22
   + **Source**

     Custom 0\.0\.0\.0/0
**Warning**  
 Before December 2020, the default EMR\-managed security group for the master instance in public subnets was created with a pre\-configured rule to allow inbound traffic on Port 22 from all sources\. This rule was created to simplify initial SSH connections to the master node\. We strongly recommend that you remove this inbound rule and restrict traffic only from trusted sources\.

1. Scroll to the bottom of the list of rules and choose **Add Rule**\.

1. For **Type**, select **SSH**\.

   This automatically enters **TCP** for **Protocol** and **22** for **Port Range**\.

1. For source, select **My IP**\.

   This automatically adds the IP address of your client computer as the source address\. Alternatively, you can add a range of **Custom** trusted client IP addresses and choose **Add rule** to create additional rules for other clients\. Many network environments dynamically allocate IP addresses, so you might need to periodically edit security group rules to update IP addresses for trusted clients\.

1. Choose **Save**\.

1. Optionally, choose **ElasticMapReduce\-slave** from the list and repeat the steps above to allow SSH client access to core and task nodes from trusted clients\.

#### Connect to the Cluster<a name="emr-getting-started-connect-ssh"></a>

After you configure your SSH rules, go to [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md) and follow the instructions to:
+ Retrieve the public DNS name of the node to which you want to connect\.
+ Connect to your cluster using SSH\.

For more information on how to authenticate to cluster nodes, see [Authenticate to Amazon EMR Cluster Nodes](emr-authenticate-cluster-connections.md)\.

## Step 3: Clean Up Amazon EMR Cluster Resources<a name="emr-getting-started-clean-up"></a>

Now that you've submitted work to your cluster and viewed the results of your PySpark application, you can shut the cluster down and delete your designated Amazon S3 bucket to avoid additional charges\. 

### Shut down your cluster<a name="emr-getting-started-stop-cluster"></a>

Shutting down a cluster stops all of its associated Amazon EMR charges and Amazon EC2 instances\.

Amazon EMR retains metadata about your cluster for two months at no charge after you terminate the cluster\. This makes it easy to [clone the cluster](clone-console.md) for a new job or revisit its configuration for reference purposes\. Metadata does *not* include data that the cluster might have written to S3, or that was stored in HDFS on the cluster while it was running\. 

**Note**  
The Amazon EMR console does not let you delete a cluster from the list view after you shut down the cluster\. A terminated cluster disappears from the console when Amazon EMR clears its metadata\. 

------
#### [ Console ]

**To shut down the cluster using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Clusters**, then choose the cluster you want to shut down\. For example, *My First EMR Cluster*\.

1. Choose **Terminate** to open the **Terminate cluster** prompt\.

1. In the open prompt, choose **Terminate** again to shut down the cluster\. Depending on the cluster configuration, it may take 5 to 10 minutes to completely terminate and release allocated EC2 resources\. For more information about shutting down Amazon EMR clusters, see [Terminate a Cluster](UsingEMR_TerminateJobFlow.md)\.
**Note**  
Clusters are often created with termination protection on to prevent accidental shutdown\. If you followed the tutorial closely, termination protection should be off\. If termination protection is on, you will see a prompt to change the setting before terminating the cluster\. Choose **Change**, then **Off**\.

------
#### [ CLI ]

**To shut down the cluster using the AWS CLI**

1. Initiate the cluster termination process with the following command, replacing *myClusterId* with the ID of your sample cluster\.

   ```
   aws emr terminate-clusters --cluster-ids myClusterId
   ```

   You should not see any output\.

1. To check that the cluster termination process has begun, check the cluster status with the following command\.

   ```
   aws emr describe-cluster --cluster-id myClusterId
   ```

   Following is example output in JSON format\. The cluster `Status` should change from **`TERMINATING`** to **`TERMINATED`**\. Depending on the cluster configuration, it may take 5 to 10 minutes to completely terminate and release allocated EC2 resources\. For more information about shutting down Amazon EMR clusters, see [Terminate a Cluster](UsingEMR_TerminateJobFlow.md)\.\.

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

Delete the bucket you created earlier to remove all of the Amazon S3 objects used in this tutorial\. This bucket should contain your input dataset, cluster output, PySpark script, and log files\. You might need to take extra steps to delete stored files if you saved your PySpark script or output in an alternative location\.

**Note**  
Your cluster must be completely shut down before you delete your bucket\. Otherwise, you might run into issues when you try to empty the bucket\.

Follow the instructions in [How Do I Delete an S3 Bucket](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/delete-bucket.html) in the *Amazon Simple Storage Service Getting Started Guide* to empty your bucket and delete it from S3\.

## Next Steps<a name="emr-gs-next-steps"></a>

You've now launched your first Amazon EMR cluster from start to finish and walked through essential EMR tasks like preparing and submitting big data applications, viewing results, and shutting down a cluster\. 

Here are some suggested topics to learn more about tailoring your Amazon EMR workflow\.

### Explore Big Data Applications for Amazon EMR<a name="emr-gs-next-explore-apps"></a>

Discover and compare the big data applications you can install on a cluster in the [Amazon EMR Release Guide](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-release-components.html)\. The Release Guide also contains details about each EMR version and tips on how to configure and use frameworks such as Spark and Hadoop on Amazon EMR\.

### Plan Cluster Hardware, Networking, and Security<a name="emr-gs-next-plan-clusters"></a>

In this tutorial, you create a simple EMR cluster without configuring advanced options such as instance types, networking, and security\. For more information on planning and launching a cluster that meets *your* speed, capacity, and security requirements, see [Plan and Configure Clusters](emr-plan.md) and [Security in Amazon EMR](emr-security.md)\.

### Manage Clusters<a name="emr-gs-next-manage-clusters"></a>

Dive deeper into working with running clusters in [Manage Clusters](emr-manage.md), which covers how to connect to clusters, debug steps, and track cluster activities and health\. You can also learn more about adjusting cluster resources in response to workload demands with [EMR managed scaling](emr-managed-scaling.md)\.

### Use a Different Interface<a name="emr-gs-next-interfaces"></a>

In addition to the Amazon EMR console, you can manage Amazon EMR using the AWS Command Line Interface, the web service API, or one of the many supported AWS SDKs\. For more information, see [Management Interfaces](emr-overview-benefits.md#emr-what-tools)\.

There are many ways you can interact with applications installed on Amazon EMR clusters\. Some applications like Apache Hadoop publish web interfaces that you can view on cluster instances\. For more information, see [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)\. With Amazon EMR clusters running Apache Spark, you can use an EMR notebook in the Amazon EMR console to run queries and code\. For more information, see [Amazon EMR Notebooks](emr-managed-notebooks.md)\.

### Browse the EMR Technical Blog<a name="emr-gs-next-browse-blogs"></a>

For sample walkthroughs and in\-depth technical discussion of EMR features, see the [AWS Big Data Blog](http://aws.amazon.com/blogs/big-data/tag/amazon-emr/)\.