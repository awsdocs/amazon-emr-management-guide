# Work with steps using the AWS CLI and console<a name="emr-work-with-steps"></a>

You can add steps to a cluster using the AWS Management Console, the AWS CLI, or the Amazon EMR API\. The maximum number of PENDING and RUNNING steps allowed in a cluster is 256, which includes system steps such as install Apache Pig, install Hive, install HBase, and configure debugging\. You can submit an unlimited number of steps over the lifetime of a long\-running cluster, but only 256 steps can be RUNNING or PENDING at any given time\. 

With Amazon EMR versions 4\.8\.0 and later, except version 5\.0\.0, you can cancel pending steps using the AWS Management Console, the AWS CLI, or the Amazon EMR API\. 

With Amazon EMR versions 5\.28\.0 and later, you can cancel both pending and running steps\. You can also choose to run multiple steps in parallel to improve cluster utilization and save cost\.

**Topics**
+ [Adding steps to a cluster using the console](emr-add-steps-console.md)
+ [Adding steps to a cluster using the AWS CLI](add-step-cli.md)
+ [Considerations for running multiple steps in parallel](emr-concurrent-steps.md)
+ [Viewing steps](emr-view-steps.md)
+ [Canceling steps](emr-cancel-steps.md)