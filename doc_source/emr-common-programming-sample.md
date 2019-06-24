# End\-to\-End Amazon EMR Java Source Code Sample<a name="emr-common-programming-sample"></a>

Developers can call the Amazon EMR API using custom Java code to do the same things possible with the Amazon EMR console or CLI\. This section provides the end\-to\-end steps necessary to install the AWS Toolkit for Eclipse and run a fully\-functional Java source code sample that adds steps to an Amazon EMR cluster\.

**Note**  
This example focuses on Java, but Amazon EMR also supports several programming languages with a collection of Amazon EMR SDKs\. For more information, see [Use SDKs to Call Amazon EMR APIs](call-emr-using-sdks.md)\.

 This Java source code example demonstrates how to perform the following tasks using the Amazon EMR API: 
+ Retrieve AWS credentials and send them to Amazon EMR to make API calls
+ Configure a new custom step and a new predefined step
+ Add new steps to an existing Amazon EMR cluster
+ Retrieve cluster step IDs from a running cluster

**Note**  
This sample demonstrates how to add steps to an existing cluster and thus requires that you have an active cluster on your account\.

Before you begin, install a version of the **Eclipse IDE for Java EE Developers** that matches your computer platform\. For more information, go to [Eclipse Downloads](http://www.eclipse.org/downloads/)\.

Next, install the Database Development plug\-in for Eclipse\.

**To install the Database Development Eclipse plug\-in**

1. Open the Eclipse IDE\.

1. Choose **Help** and **Install New Software**\.

1. In the **Work with:** field, type **http://download\.eclipse\.org/releases/kepler** or the path that matches the version number of your Eclipse IDE\.

1. In the items list, choose **Database Development** and **Finish**\.

1. Restart Eclipse when prompted\.

Next, install the Toolkit for Eclipse to make the helpful, pre\-configured source code project templates available\.

**To install the Toolkit for Eclipse**

1. Open the Eclipse IDE\.

1. Choose **Help** and **Install New Software**\.

1. In the **Work with:** field, type **https://aws\.amazon\.com//eclipse**\.

1. In the items list, choose **AWS Toolkit for Eclipse** and **Finish**\.

1. Restart Eclipse when prompted\.

Next, create a new AWS Java project and run the sample Java source code\.

**To create a new AWS Java project**

1. Open the Eclipse IDE\.

1. Choose **File**, **New**, and **Other**\.

1. In the **Select a wizard** dialog, choose **AWS Java Project** and **Next**\.

1. In the **New AWS Java Project** dialog, in the **Project name:** field, enter the name of your new project, for example **EMR\-sample\-code**\.

1. Choose **Configure AWS accounts…**, enter your public and private access keys, and choose **Finish**\. For more information about creating access keys, see [How Do I Get Security Credentials?](https://docs.aws.amazon.com/general/latest/gr//getting-aws-sec-creds.html) in the *Amazon Web Services General Reference*\.
**Note**  
You should **not** embed access keys directly in code\. The Amazon EMR SDK allows you to put access keys in known locations so that you do not have to keep them in code\.

1. In the new Java project, right\-click the **src** folder, then choose **New** and **Class**\.

1. In the **Java Class** dialog, in the **Name** field, enter a name for your new class, for example **main**\.

1. In the **Which method stubs would you like to create?** section, choose **public static void main\(String\[\] args\)** and **Finish**\.

1. Enter the Java source code inside your new class and add the appropriate **import **statements for the classes and methods in the sample\. For your convenience, the full source code listing is shown below\. 
**Note**  
In the following sample code, replace the example cluster ID \(JobFlowId\), *`j-xxxxxxxxxxxx`*, with a valid cluster ID in your account found either in the AWS Management Console or by using the following AWS CLI command:   

   ```
   aws emr list-clusters --active | grep "Id"
   ```
In addition, replace the example Amazon S3 path, *`s3://path/to/my/jarfolder`*, with the valid path to your JAR\. Lastly, replace the example class name, *`com.my.Main1`*, with the correct name of the class in your JAR, if applicable\. 

   ```
   import com.amazonaws.AmazonClientException;
   import com.amazonaws.auth.AWSCredentials;
   import com.amazonaws.auth.AWSStaticCredentialsProvider;
   import com.amazonaws.auth.profile.ProfileCredentialsProvider;
   import com.amazonaws.services.elasticmapreduce.AmazonElasticMapReduce;
   import com.amazonaws.services.elasticmapreduce.AmazonElasticMapReduceClientBuilder;
   import com.amazonaws.services.elasticmapreduce.model.*;
   import com.amazonaws.services.elasticmapreduce.util.StepFactory;
   
   public class Main {
   
   	public static void main(String[] args) {
   		AWSCredentials credentials_profile = null;		
   		try {
   			credentials_profile = new ProfileCredentialsProvider("default").getCredentials();
           } catch (Exception e) {
               throw new AmazonClientException(
                       "Cannot load credentials from .aws/credentials file. " +
                       "Make sure that the credentials file exists and the profile name is specified within it.",
                       e);
           }
   		
   		AmazonElasticMapReduce emr = AmazonElasticMapReduceClientBuilder.standard()
   			.withCredentials(new AWSStaticCredentialsProvider(credentials_profile))
   			.withRegion(Regions.US_WEST_1)
   			.build();
           
   		// Run a bash script using a predefined step in the StepFactory helper class
   	    StepFactory stepFactory = new StepFactory();
   	    StepConfig runBashScript = new StepConfig()
   	    		.withName("Run a bash script") 
   	    		.withHadoopJarStep(stepFactory.newScriptRunnerStep("s3://jeffgoll/emr-scripts/create_users.sh"))
   	    		.withActionOnFailure("CONTINUE");
   
   	    // Run a custom jar file as a step
   	    HadoopJarStepConfig hadoopConfig1 = new HadoopJarStepConfig()
   	       .withJar("s3://path/to/my/jarfolder") // replace with the location of the jar to run as a step
   	       .withMainClass("com.my.Main1") // optional main class, this can be omitted if jar above has a manifest
   	       .withArgs("--verbose"); // optional list of arguments to pass to the jar
   	    StepConfig myCustomJarStep = new StepConfig("RunHadoopJar", hadoopConfig1);
   
   	    AddJobFlowStepsResult result = emr.addJobFlowSteps(new AddJobFlowStepsRequest()
   		  .withJobFlowId("j-xxxxxxxxxxxx") // replace with cluster id to run the steps
   		  .withSteps(runBashScript,myCustomJarStep));
   	    
             System.out.println(result.getStepIds());
   
   	}
   }
   ```

1. Choose **Run**, **Run As**, and **Java Application**\.

1. If the sample runs correctly, a list of IDs for the new steps appears in the Eclipse IDE console window\. The correct output is similar to the following:

   ```
   [s-39BLQZRJB2E5E, s-1L6A4ZU2SAURC]
   ```