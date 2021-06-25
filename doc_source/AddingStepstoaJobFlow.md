# Submit work to a cluster<a name="AddingStepstoaJobFlow"></a>

This section describes the methods for submitting work to an Amazon EMR cluster\. You can submit work to a cluster by adding steps or by interactively submitting Hadoop jobs to the master node\. The maximum number of PENDING and RUNNING steps allowed in a cluster is 256\. You can submit jobs interactively to the master node even if you have 256 active steps running on the cluster\. You can submit an unlimited number of steps over the lifetime of a long\-running cluster, but only 256 steps can be RUNNING or PENDING at any given time\.

**Topics**
+ [Work with steps using the AWS CLI and console](emr-work-with-steps.md)
+ [Submit Hadoop jobs interactively](interactive-jobs.md)
+ [Add more than 256 steps to a cluster](AddMoreThan256Steps.md)