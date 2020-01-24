# Submit Work to a Cluster<a name="AddingStepstoaJobFlow"></a>

This section describes the methods for submitting work to an Amazon EMR cluster\. You can submit work to a cluster by adding steps or by interactively submitting Hadoop jobs to the master node\. The maximum number of PENDING and ACTIVE steps allowed in a cluster is 256\. You can submit jobs interactively to the master node even if you have 256 active steps running on the cluster\. You can submit an unlimited number of steps over the lifetime of a long\-running cluster, but only 256 steps can be ACTIVE or PENDING at any given time\.

**Topics**
+ [Work with Steps Using the AWS CLI and Console](emr-work-with-steps.md)
+ [Submit Hadoop Jobs Interactively](interactive-jobs.md)
+ [Add More than 256 Steps to a Cluster](AddMoreThan256Steps.md)