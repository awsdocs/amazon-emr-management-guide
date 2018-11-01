# Step 5: Check for Arrested Groups<a name="emr-troubleshoot-slow-5"></a>

 An instance group becomes arrested when it encounters too many errors while trying to launch nodes\. For example, if new nodes repeatedly fail while performing bootstrap actions, the instance group will — after some time — go into the `ARRESTED` state rather than continuously attempt to provision new nodes\. 

 A node could fail to come up if: 
+ Hadoop or the cluster is somehow broken and does not accept a new node into the cluster
+ A bootstrap action fails on the new node
+ The node is not functioning correctly and fails to check in with Hadoop

If an instance group is in the `ARRESTED` state, and the cluster is in a `WAITING` state, you can add a cluster step to reset the desired number of core and task nodes\. Adding the step resumes processing of the cluster and put the instance group back into a `RUNNING` state\. 

For more information about how to reset a cluster in an arrested state, see [Arrested State](emr-manage-resize.md#emr-manage-resizeArrested)\. 