# EC2 QUOTA EXCEEDED<a name="emr-EC2"></a>

If you get an `EC2 QUOTA EXCEEDED` message, there may be several causes\. Depending on configuration differences, it may take up to 5\-20 minutes for previous clusters to terminate and release allocated resources\. If you are getting an `EC2 QUOTA EXCEEDED` error when you attempt to launch a cluster, it may be because resources from a recently terminated cluster have not yet been released\. This message can also be caused by the resizing of an instance group or instance fleet to a target size that is greater than the current instance quota for the account\. This can happen manually or automatically through automatic scaling\.

Consider the following options to resolve the issue:
+ [Create a support case](https://console.aws.amazon.com/support/v1#/case/create) to request a quota increase\. 
+ If one or more running clusters are not at capacity, resize instance groups or reduce target capacities on instance fleets for running clusters\.
+ Create clusters with fewer EC2 instances or reduced target capacity\.