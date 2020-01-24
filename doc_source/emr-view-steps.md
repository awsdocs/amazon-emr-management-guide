# Viewing Steps<a name="emr-view-steps"></a>

The total number of step records you can view \(regardless of status\) is 1,000\. This total includes both user\-submitted and system steps\. As the status of user\-submitted steps changes to COMPLETED or FAILED, additional user\-submitted steps can be added to the cluster until the 1,000 step limit is reached\. After 1,000 steps have been added to a cluster, the submission of additional steps causes the removal of older, user\-submitted step records\. These records are not removed from the log files\. They are removed from the console display, and they do not appear when you use the AWS CLI or API to retrieve cluster information\. System step records are never removed\.

The step information you can view depends on the mechanism used to retrieve cluster information\. The following table indicates the step information returned by each of the available options\. 


| Option | DescribeJobFlow or \-\-describe \-\-jobflow | ListSteps or list\-steps | 
| --- | --- | --- | 
| SDK | 256 steps | 1,000 steps | 
| Amazon EMR CLI | 256 steps | NA | 
| AWS CLI | NA | 1,000 steps | 
| API | 256 steps | 1,000 steps | 