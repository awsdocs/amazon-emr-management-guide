# Using the AWS CLI to Configure Managed Scaling<a name="managed-scaling-cli"></a>

You can use AWS CLI commands for Amazon EMR to configure managed scaling when you create a cluster\. You can use a shorthand syntax, specifying the JSON configuration inline within the relevant commands, or you can reference a file containing the configuration JSON\. You can also apply a managed scaling policy to an existing cluster and remove a managed scaling policy that was previously applied\. In addition, you can retrieve details of a scaling policy configuration from a running cluster\.

**Enabling Managed Scaling During Cluster Launch**

You can enable managed scaling during cluster launch as the following example demonstrates\.

```
aws emr create-cluster \
 --service-role EMR_DefaultRole \
 --release-label emr-5.32.0 \
 --name EMR_Managed_Scaling_Enabled_Cluster \
 --applications Name=Spark Name=Hbase \
 --ec2-attributes KeyName=keyName,InstanceProfile=EMR_EC2_DefaultRole \
 --instance-groups InstanceType=m4.xlarge,InstanceGroupType=MASTER,InstanceCount=1 InstanceType=m4.xlarge,InstanceGroupType=CORE,InstanceCount=2 \
 --region us-east-1 \
 --managed-scaling-policy ComputeLimits='{MinimumCapacityUnits=2,MaximumCapacityUnits=4,UnitType=Instances}'
```

You can also specify a managed policy configuration using the \-\-managed\-scaling\-policy option when you use `create-cluster`\. 

**Applying a Managed Scaling Policy to an Existing Cluster**

You can apply a managed scaling policy to an existing cluster as the following example demonstrates\.

```
aws emr put-managed-scaling-policy  
--cluster-id j-123456  
--managed-scaling-policy ComputeLimits='{MinimumCapacityUnits=1,
MaximumCapacityUnits=10,  MaximumOnDemandCapacityUnits=10, UnitType=Instances}'
```

You can also apply a managed scaling policy to an existing cluster by using the `aws emr put-managed-scaling-policy` command\. The following example uses a reference to a JSON file, `managedscaleconfig.json`, that specifies the managed scaling policy configuration\.

```
aws emr put-managed-scaling-policy --cluster-id j-123456 --managed-scaling-policy file://./managedscaleconfig.json
```

The following example shows the contents of the `managedscaleconfig.json` file, which defines the managed scaling policy\.

```
{
    "ComputeLimits": {
        "UnitType": "Instances",
        "MinimumCapacityUnits": 1,
        "MaximumCapacityUnits": 10,
        "MaximumOnDemandCapacityUnits": 10
    }
}
```

**Retrieving a Managed Scaling Policy Configuration**

The `GetManagedScalingPolicy` command retrieves the policy configuration\. For example, the following command retrieves the configuration for the cluster with a cluster ID of `j-123456`\.

```
aws emr get-managed-scaling-policy --cluster-id j-123456
```

The command produces the following example output\.

```
 1. {
 2.    "ManagedScalingPolicy": { 
 3.       "ComputeLimits": { 
 4.          "MinimumCapacityUnits": 1,
 5.          "MaximumOnDemandCapacityUnits": 10,
 6.          "MaximumCapacityUnits": 10,
 7.          "UnitType": "Instances"
 8.       }
 9.    }
10. }
```

For more information about using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

**Removing Managed Scaling Policy**

The `RemoveManagedScalingPolicy` command removes the policy configuration\. For example, the following command removes the configuration for the cluster with a cluster ID of `j-123456`\.

```
aws emr remove-managed-scaling-policy --cluster-id j-123456
```