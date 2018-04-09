# Using the AWS SDK for Java to Create an Amazon EMR Cluster<a name="calling-emr-with-java-sdk"></a>

The AWS SDK for Java provides three packages with Amazon EMR functionality:
+  [com\.amazonaws\.services\.elasticmapreduce](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc//com/amazonaws/services/elasticmapreduce/package-summary.html) 
+  [com\.amazonaws\.services\.elasticmapreduce\.model](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc//com/amazonaws/services/elasticmapreduce/model/package-summary.html) 
+  [com\.amazonaws\.services\.elasticmapreduce\.util](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc//com/amazonaws/services/elasticmapreduce/util/package-summary.html) 

For more information about these packages, see the [AWS SDK for Java API Reference](http://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/)\.

 The following example illustrates how the SDKs can simplify programming with Amazon EMR The code sample below uses the `StepFactory` object, a helper class for creating common Amazon EMR step types, to create an interactive Hive cluster with debugging enabled\. 

**Note**  
If you are adding IAM user visibility to a new cluster, call [http://docs.aws.amazon.com/ElasticMapReduce/latest/API//API_RunJobFlow.html](http://docs.aws.amazon.com/ElasticMapReduce/latest/API//API_RunJobFlow.html) and set `VisibleToAllUsers=true`, otherwise IAM users cannot view the cluster\.

```
 1.    AWSCredentials credentials = new BasicAWSCredentials(accessKey, secretKey);
 2.    AmazonElasticMapReduceClient emr = new AmazonElasticMapReduceClient(credentials);
 3.    String COMMAND_RUNNER = "command-runner.jar";
 4.    String DEBUGGING_COMMAND = "state-pusher-script";
 5.    String DEBUGGING_NAME = "Setup Hadoop Debugging";   
 6. 
 7.    StepFactory stepFactory = new StepFactory();
 8. 
 9.    StepConfig enabledebugging = new StepConfig()
10.        .withName(DEBUGGING_NAME)
11.        .withActionOnFailure(ActionOnFailure.TERMINATE_CLUSTER)
12.        .withHadoopJarStep(new HadoopJarStepConfig()
13.            .withJar(COMMAND_RUNNER)
14.            .withArgs(DEBUGGING_COMMAND));
15. 
16.    RunJobFlowRequest request = new RunJobFlowRequest()
17.        .withName("Hive Interactive")
18.        .withReleaseLabel("emr-4.1.0")
19.        .withSteps(enabledebugging)
20.        .withApplications(myApp)
21.        .withLogUri("s3://myawsbucket/")
22.        .withServiceRole("service_role")
23.        .withJobFlowRole("jobflow_role")
24.        .withInstances(new JobFlowInstancesConfig()
25.            .withEc2KeyName("keypair")
26.            .withInstanceCount(5)
27.            .withKeepJobFlowAliveWhenNoSteps(true)
28.            .withMasterInstanceType("m3.xlarge")
29.            .withSlaveInstanceType("m3.xlarge"));
30. 
31.    RunJobFlowResult result = emr.runJobFlow(request);
```

At minimum, you must pass a service role and jobflow role corresponding to EMR\_DefaultRole and EMR\_EC2\_DefaultRole, respectively\. You can do this by invoking this AWS CLI command for the same account\. First, look to see if the roles already exist: 

```
aws iam list-roles | grep EMR
```

Both the instance profile \(EMR\_EC2\_DefaultRole\) and the service role \(EMR\_DefaultRole\) will be displayed if they exist: 

```
"RoleName": "EMR_DefaultRole", 
            "Arn": "arn:aws:iam::AccountID:role/EMR_DefaultRole"
            "RoleName": "EMR_EC2_DefaultRole", 
            "Arn": "arn:aws:iam::AccountID:role/EMR_EC2_DefaultRole"
```

If the default roles do not exist, you can use the following AWS CLI command to create them:

```
aws emr create-default-roles
```