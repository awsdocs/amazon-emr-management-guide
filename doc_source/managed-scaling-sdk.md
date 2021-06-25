# Using AWS SDK for Java to configure managed scaling<a name="managed-scaling-sdk"></a>

The following program excerpt shows how to configure managed scaling using the AWS SDK for Java:

```
package com.amazonaws.emr.sample;

import java.util.ArrayList;
import java.util.List;

import com.amazonaws.AmazonClientException;
import com.amazonaws.auth.AWSCredentials;
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.elasticmapreduce.AmazonElasticMapReduce;
import com.amazonaws.services.elasticmapreduce.AmazonElasticMapReduceClientBuilder;
import com.amazonaws.services.elasticmapreduce.model.Application;
import com.amazonaws.services.elasticmapreduce.model.ComputeLimits;
import com.amazonaws.services.elasticmapreduce.model.ComputeLimitsUnitType;
import com.amazonaws.services.elasticmapreduce.model.InstanceGroupConfig;
import com.amazonaws.services.elasticmapreduce.model.JobFlowInstancesConfig;
import com.amazonaws.services.elasticmapreduce.model.ManagedScalingPolicy;
import com.amazonaws.services.elasticmapreduce.model.RunJobFlowRequest;
import com.amazonaws.services.elasticmapreduce.model.RunJobFlowResult;

public class CreateClusterWithManagedScalingWithIG {

	public static void main(String[] args) {
		AWSCredentials credentialsFromProfile = getCreadentials("AWS-Profile-Name-Here");
		
		/**
		 * Create an EMR client using the credentials and region specified in order to create the cluster
		 */
		AmazonElasticMapReduce emr = AmazonElasticMapReduceClientBuilder.standard()
			.withCredentials(new AWSStaticCredentialsProvider(credentialsFromProfile))
			.withRegion(Regions.US_EAST_1)
			.build();
		
		/**
		 * Create Instance Groups - Master, Core, Task
		 */
		InstanceGroupConfig instanceGroupConfigMaster = new InstanceGroupConfig()
				.withInstanceCount(1)
				.withInstanceRole("MASTER")
				.withInstanceType("m4.large")
				.withMarket("ON_DEMAND"); 
				
		InstanceGroupConfig instanceGroupConfigCore = new InstanceGroupConfig()
			.withInstanceCount(4)
			.withInstanceRole("CORE")
			.withInstanceType("m4.large")
			.withMarket("ON_DEMAND");
			
		InstanceGroupConfig instanceGroupConfigTask = new InstanceGroupConfig()
			.withInstanceCount(5)
			.withInstanceRole("TASK")
			.withInstanceType("m4.large")
			.withMarket("ON_DEMAND");

		List<InstanceGroupConfig> igConfigs = new ArrayList<>();
		igConfigs.add(instanceGroupConfigMaster);
		igConfigs.add(instanceGroupConfigCore);
		igConfigs.add(instanceGroupConfigTask);
		
        /**
         *  specify applications to be installed and configured when EMR creates the cluster
         */
		Application hive = new Application().withName("Hive");
		Application spark = new Application().withName("Spark");
		Application ganglia = new Application().withName("Ganglia");
		Application zeppelin = new Application().withName("Zeppelin");
		
		/** 
		 * Managed Scaling Configuration - 
         * Using UnitType=Instances for clusters composed of instance groups
		 *
         * Other options are: 
         * UnitType = VCPU ( for clusters composed of instance groups)
         * UnitType = InstanceFleetUnits ( for clusters composed of instance fleets)
         **/
		ComputeLimits computeLimits = new ComputeLimits()
				.withMinimumCapacityUnits(1)
				.withMaximumCapacityUnits(20)
				.withUnitType(ComputeLimitsUnitType.Instances);
		
		ManagedScalingPolicy managedScalingPolicy = new ManagedScalingPolicy();
		managedScalingPolicy.setComputeLimits(computeLimits);
		
		// create the cluster with a managed scaling policy
		RunJobFlowRequest request = new RunJobFlowRequest()
	       		.withName("EMR_Managed_Scaling_TestCluster")
	       		.withReleaseLabel("emr-5.33.0")          // Specifies the EMR release version label, we recommend the latest release
	       		.withApplications(hive,spark,ganglia,zeppelin)
	       		.withLogUri("s3://path/to/my/emr/logs")  // A URI in S3 for log files is required when debugging is enabled.
	       		.withServiceRole("EMR_DefaultRole")      // If you use a custom IAM service role, replace the default role with the custom role.
	       		.withJobFlowRole("EMR_EC2_DefaultRole")  // If you use a custom EMR role for EC2 instance profile, replace the default role with the custom EMR role.
	       		.withInstances(new JobFlowInstancesConfig().withInstanceGroups(igConfigs)
	       	   		.withEc2SubnetId("subnet-123456789012345")
	           		.withEc2KeyName("my-ec2-key-name") 
	           		.withKeepJobFlowAliveWhenNoSteps(true))    
	       		.withManagedScalingPolicy(managedScalingPolicy);
	   RunJobFlowResult result = emr.runJobFlow(request); 
	   
	   System.out.println("The cluster ID is " + result.toString());
	}
	
	public static AWSCredentials getCredentials(String profileName) {
		// specifies any named profile in .aws/credentials as the credentials provider
		try {
			return new ProfileCredentialsProvider("AWS-Profile-Name-Here")
					.getCredentials(); 
        } catch (Exception e) {
            throw new AmazonClientException(
                    "Cannot load credentials from .aws/credentials file. " +
                    "Make sure that the credentials file exists and that the profile name is defined within it.",
                    e);
        }
	}
	
	public CreateClusterWithManagedScalingWithIG() { }
}
```