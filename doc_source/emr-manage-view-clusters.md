# View Cluster Status and Details<a name="emr-manage-view-clusters"></a>

After you create a cluster, you can monitor its status and get detailed information about its execution and errors that may have occurred, even after it has terminated\. Amazon EMR saves metadata about terminated clusters for your reference for two months, after which the metadata is deleted\. Application history is saved for one week from the time it is recorded, regardless of whether the cluster is running or terminated\. You can't delete clusters from the cluster history, but using the AWS Management Console, you can use the **Filter**, and using the AWS CLI, you can use options with the `list-clusters` command to focus on the clusters that you care about\.

## View Cluster Status Using the AWS Management Console<a name="emr-view-cluster-console"></a>

The **Clusters List** in the [Amazon EMR console](https://console.aws.amazon.com//elasticmapreduce/home) lists all the clusters in your account and AWS Region, including terminated clusters\. The list shows the following for each cluster: the **Name** and **ID**, the **Status**, the **Creation time**, the **Elapsed time** that the cluster was running, and the **Normalized instance hours** that have accrued for all EC2 instances in the cluster\. This list is the starting point for monitoring the status of your clusters\. It's designed so that you can drill down into each cluster's details for analysis and troubleshooting\.

**To view an abridged summary of cluster information**
+ Select the down arrow next to the link for the cluster under **Name**\.

  The cluster's row expands to provide more information about the cluster, hardware, steps, and bootstrap actions\. Use the links in this section to drill into specifics\. For example, click a link under **Steps** to access step log files, see the JAR associated with the step, drill into the step's jobs and tasks, and access log files\.  
![\[Cluster information\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/expanded-row.png)

**To view cluster status in depth**
+ Choose the cluster link under **Name** to open a cluster details page for the cluster\. Use each tab to view information as described in the following section\.

  Use each tab for the following information:  
![\[Cluster information\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/viewcluster_summary.png)    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-manage-view-clusters.html)

## View Cluster Status Using the AWS CLI<a name="view-cluser-cli"></a>

The following examples demonstrate how to retrieve cluster details using the AWS CLI\. For more information about available commands, see the [AWS CLI Command Reference for Amazon EMR](https://docs.aws.amazon.com/cli/latest/reference/emr)\. You can use the [describe\-cluster](https://docs.aws.amazon.com/cli/latest/reference/emr/describe-cluster.html) command to view cluster\-level details including status, hardware and software configuration, VPC settings, bootstrap actions, instance groups, and so on\. The following example demonstrates using the `describe-cluster` command, followed by examples of the [list\-clusters](https://docs.aws.amazon.com/cli/latest/reference/emr/describe-cluster.html) command\.

**Example Viewing Cluster Status**  
To use the `describe-cluster` command, you need the cluster ID\. This example demonstrates using to get a list of clusters created within a certain date range, and then using one of the cluster IDs returned to list more information about an individual cluster's status\.  
The following command describes cluster *j\-1K48XXXXXXHCB*, which you replace with your cluster ID\.  

```
aws emr describe-cluster --cluster-id j-1K48XXXXXXHCB
```
The output of your command is similar to the following:  

```
{
    "Cluster": {
        "Status": {
            "Timeline": {
                "ReadyDateTime": 1438281058.061, 
                "CreationDateTime": 1438280702.498
            }, 
            "State": "WAITING", 
            "StateChangeReason": {
                "Message": "Waiting for steps to run"
            }
        }, 
        "Ec2InstanceAttributes": {
            "EmrManagedMasterSecurityGroup": "sg-cXXXXX0", 
            "IamInstanceProfile": "EMR_EC2_DefaultRole", 
            "Ec2KeyName": "myKey", 
            "Ec2AvailabilityZone": "us-east-1c", 
            "EmrManagedSlaveSecurityGroup": "sg-example"
        }, 
        "Name": "Development Cluster", 
        "ServiceRole": "EMR_DefaultRole", 
        "Tags": [], 
        "TerminationProtected": false, 
        "ReleaseLabel": "emr-4.0.0", 
        "NormalizedInstanceHours": 16, 
        "InstanceGroups": [
            {
                "RequestedInstanceCount": 1, 
                "Status": {
                    "Timeline": {
                        "ReadyDateTime": 1438281058.101, 
                        "CreationDateTime": 1438280702.499
                    }, 
                    "State": "RUNNING", 
                    "StateChangeReason": {
                        "Message": ""
                    }
                }, 
                "Name": "CORE", 
                "InstanceGroupType": "CORE", 
                "Id": "ig-2EEXAMPLEXXP", 
                "Configurations": [], 
                "InstanceType": "m4.large", 
                "Market": "ON_DEMAND", 
                "RunningInstanceCount": 1
            }, 
            {
                "RequestedInstanceCount": 1, 
                "Status": {
                    "Timeline": {
                        "ReadyDateTime": 1438281023.879, 
                        "CreationDateTime": 1438280702.499
                    }, 
                    "State": "RUNNING", 
                    "StateChangeReason": {
                        "Message": ""
                    }
                }, 
                "Name": "MASTER", 
                "InstanceGroupType": "MASTER", 
                "Id": "ig-2A1234567XP", 
                "Configurations": [], 
                "InstanceType": "m4.large", 
                "Market": "ON_DEMAND", 
                "RunningInstanceCount": 1
            }
        ], 
        "Applications": [
            {
                "Version": "1.0.0", 
                "Name": "Hive"
            }, 
            {
                "Version": "2.6.0", 
                "Name": "Hadoop"
            }, 
            {
                "Version": "0.14.0", 
                "Name": "Pig"
            }, 
            {
                "Version": "1.4.1", 
                "Name": "Spark"
            }
        ], 
        "BootstrapActions": [], 
        "MasterPublicDnsName": "ec2-X-X-X-X.compute-1.amazonaws.com", 
        "AutoTerminate": false, 
        "Id": "j-jobFlowID", 
        "Configurations": [
            {
                "Properties": {
                    "hadoop.security.groups.cache.secs": "250"
                }, 
                "Classification": "core-site"
            }, 
            {
                "Properties": {
                    "mapreduce.tasktracker.reduce.tasks.maximum": "5", 
                    "mapred.tasktracker.map.tasks.maximum": "2", 
                    "mapreduce.map.sort.spill.percent": "90"
                }, 
                "Classification": "mapred-site"
            }, 
            {
                "Properties": {
                    "hive.join.emit.interval": "1000", 
                    "hive.merge.mapfiles": "true"
                }, 
                "Classification": "hive-site"
            }
        ]
    }
}
```

**Example Listing Clusters by Creation Date**  
To retrieve clusters created within a specific data range, use the `list-clusters` command with the `--created-after` and `--created-before` parameters\.  
The following command lists all clusters created between October 09, 2014 and October 12, 2014\.  

```
aws emr list-clusters --created-after 2014-10-09T00:12:00 --created-before 2014-10-12T00:12:00
```

**Example Listing Clusters by State**  
To list clusters by state, use the `list-clusters` command with the `--cluster-states` parameter\. Valid cluster states include: STARTING, BOOTSTRAPPING, RUNNING, WAITING, TERMINATING, TERMINATED, and TERMINATED\_WITH\_ERRORS\.   

```
aws emr list-clusters --cluster-states TERMINATED
```
You can also use the following shortcut parameters to list all clusters in the states specified\.:  
+ `--active` filters clusters in the STARTING,BOOTSTRAPPING, RUNNING, WAITING, or TERMINATING states\.
+ `--terminated` filters clusters in the TERMINATED state\.
+ `--failed` parameter filters clusters in the TERMINATED\_WITH\_ERRORS state\.
The following commands return the same result\.  

```
aws emr list-clusters --cluster-states TERMINATED
```

```
aws emr list-clusters --terminated
```