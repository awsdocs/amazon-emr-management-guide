# EMR integration with EC2 placement groups<a name="emr-plan-ha-placementgroup"></a>

When you launch an Amazon EMR multiple master node cluster on Amazon EC2, you have the option to use placement group strategies to specify how you want the master node instances deployed to protect against hardware failure\.

Placement group strategies are supported starting with Amazon EMR version 5\.23\.0 as an option for multiple master node clusters\. Currently, only master node types are supported by the placement group strategy, and the `SPREAD` strategy is applied to those master nodes\. The `SPREAD` strategy places a small group of instances across separate underlying hardware to guard against the loss of multiple master nodes in the event of a hardware failure\. Note that an instance launch request could fail if there is insufficient unique hardware to fulfill the request\. For more information about EC2 placement strategies and limitations, see [Placement groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html) in the *EC2 User Guide for Linux Instances*\.

There is an initial limit from Amazon EC2 of 500 placement group strategy\-enabled clusters that can be launched per AWS region\. Contact AWS support to request an increase in the number of allowed placement groups\. You can identify EC2 placement groups EMR creates by tracking the key\-value pair that Amazon EMR associates with the EMR placement group strategy\. For more information about EC2 cluster instance tags, see [View cluster instances in Amazon EC2](UsingEMR_Tagging.md)\.

## Attaching the placement group managed policy to the EMR role<a name="emr-plan-ha-launch-pg-policy"></a>

The placement group strategy requires a managed policy called `AmazonElasticMapReducePlacementGroupPolicy` in order to allow Amazon EMR to create, delete, and describe placement groups on Amazon EC2\. The managed policy must be attached to the Amazon EMR role before you launch the Amazon EMR multiple master cluster\. The Amazon EMR managed policy, `AmazonEMRServicePolicy_v2`, can be attached to the Amazon EMR role instead of the placement group managed policy\. `AmazonEMRServicePolicy_v2` allows the same access to placement groups on Amazon EC2 as the `AmazonElasticMapReducePlacementGroupPolicy`\. For more information, see [Service role for Amazon EMR \(EMR role\)](emr-iam-role.md)\.

The `AmazonElasticMapReducePlacementGroupPolicy` managed policy is the following JSON text that is created and administered by Amazon EMR\.

**Note**  
Because the `AmazonElasticMapReducePlacementGroupPolicy` managed policy is updated automatically, the policy shown here may be out\-of\-date\. Use the AWS Management Console to view the current policy\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Resource":"*",
         "Effect":"Allow",
         "Action":[
            "ec2:DeletePlacementGroup",
            "ec2:DescribePlacementGroups"
         ]
      },
      {
         "Resource":"arn:aws:ec2:*:*:placement-group/EMR_*",
         "Effect":"Allow",
         "Action":[
            "ec2:CreatePlacementGroup"
         ]
      }
   ]
}
```

## Launch an Amazon EMR multiple master cluster with placement group strategy<a name="emr-plan-ha-launch-pg-strategy"></a>

To launch an Amazon EMR multiple master cluster with a placement group strategy, attach the placement group managed policy `AmazonElasticMapReducePlacementGroupPolicy` to the EMR role\. For more information, see [Attaching the placement group managed policy to the EMR role](#emr-plan-ha-launch-pg-policy)\.

Every time you use this role to start an Amazon EMR multiple master cluster, EMR attempts to launch a cluster with `SPREAD` strategy applied to its master nodes\. If you use a role that does not have the placement group managed policy `AmazonElasticMapReducePlacementGroupPolicy` attached to it, EMR attempts to launch an Amazon EMR multiple master cluster without a placement group strategy\.

If you launch an Amazon EMR multiple master cluster with the `placement-group-configs` parameter using the EMR API or CLI, EMR only launches the cluster if the EMR role has the placement group managed policy `AmazonElasticMapReducePlacementGroupPolicy` attached\. If the EMR role does not have the policy attached, the Amazon EMR multiple master cluster start fails\.

**Example – Launching an Amazon EMR multiple master cluster with placement group strategy using the EMR API\.**  
When you use the RunJobFlow action to create an Amazon EMR multiple master cluster, set the `PlacementGroupConfigs` property to the following\. Currently, the `MASTER` instance role automatically uses `SPREAD` as the placement group strategy\.  

```
{
   "Name":"ha-cluster",
   "PlacementGroupConfigs":[
      {
         "InstanceRole":"MASTER"
      }
   ],
   "ReleaseLabel":"emr-5.30.1",
   "Instances":{
      "ec2SubnetId":"subnet-22XXXX01",
      "ec2KeyName":"ec2_key_pair_name",
      "InstanceGroups":[
         {
            "InstanceCount":3,
            "InstanceRole":"MASTER",
            "InstanceType":"m5.xlarge"
         },
         {
            "InstanceCount":4,
            "InstanceRole":"CORE",
            "InstanceType":"m5.xlarge"
         }
      ]
   },
   "JobFlowRole":"EMR_EC2_DefaultRole",
   "ServiceRole":"EMR_DefaultRole"
}
```
+ Replace *ha\-cluster* with the name of your high\-availability cluster\.
+ Replace *subnet\-22XXXX01* with your subnet ID\.
+ Replace the *ec2\_key\_pair\_name* with the name of your EC2 key pair for this cluster\. EC2 key pair is optional and only required if you want to use SSH to access your cluster\.

**Example – Launching an Amazon EMR multiple master cluster with a placement group strategy using the EMR CLI\.**  

```
aws emr create-cluster \
--name "ha-cluster" \
--placement-group-configs InstanceRole=MASTER \
--release-label emr-5.30.1 \
--instance-groups InstanceGroupType=MASTER,InstanceCount=3,InstanceType=m5.xlarge InstanceGroupType=CORE,InstanceCount=4,InstanceType=m5.xlarge \
--ec2-attributes KeyName=ec2_key_pair_name,InstanceProfile=EMR_EC2_DefaultRole,SubnetId=subnet-22XXXX01 \
--service-role EMR_DefaultRole \
--applications Name=Hadoop Name=Spark
```
+ Replace *ha\-cluster* with the name of your high\-availability cluster\.
+ Replace *subnet\-22XXXX01* with your subnet ID\.
+ Replace the *ec2\_key\_pair\_name* with the name of your EC2 key pair for this cluster\. EC2 key pair is optional and only required if you want to use SSH to access your cluster\.

## Launch an Amazon EMR multiple master cluster without a placement group strategy<a name="emr-plan-ha-launch-no-pg-strategy"></a>

For an Amazon EMR multiple master cluster to launch master nodes without the placement group strategy, you need to do one of the following:
+ Remove the placement group managed policy `AmazonElasticMapReducePlacementGroupPolicy`from the EMR role, or
+ Launch an Amazon EMR multiple master cluster with the `placement-group-configs` parameter using the EMR API or CLI choosing `NONE` as the placement group strategy\.

**Example – Launching an Amazon EMR multiple master cluster without placement group strategy using the EMR API\.**  
When using the RunJobFlow action to create an Amazon EMR multiple master cluster, set the `PlacementGroupConfigs` property to the following\.  

```
{
   "Name":"ha-cluster",
   "PlacementGroupConfigs":[
      {
         "InstanceRole":"MASTER",
         "PlacementStrategy":"NONE"
      }
   ],
   "ReleaseLabel":"emr-5.30.1",
   "Instances":{
      "ec2SubnetId":"subnet-22XXXX01",
      "ec2KeyName":"ec2_key_pair_name",
      "InstanceGroups":[
         {
            "InstanceCount":3,
            "InstanceRole":"MASTER",
            "InstanceType":"m5.xlarge"
         },
         {
            "InstanceCount":4,
            "InstanceRole":"CORE",
            "InstanceType":"m5.xlarge"
         }
      ]
   },
   "JobFlowRole":"EMR_EC2_DefaultRole",
   "ServiceRole":"EMR_DefaultRole"
}
```
+ Replace *ha\-cluster* with the name of your high\-availability cluster\.
+ Replace *subnet\-22XXXX01* with your subnet ID\.
+ Replace the *ec2\_key\_pair\_name* with the name of your EC2 key pair for this cluster\. EC2 key pair is optional and only required if you want to use SSH to access your cluster\.

**Example – Launching an Amazon EMR multiple master cluster without a placement group strategy using the EMR CLI\.**  

```
aws emr create-cluster \
--name "ha-cluster" \
--placement-group-configs InstanceRole=MASTER,PlacementStrategy=NONE \
--release-label emr-5.30.1 \
--instance-groups InstanceGroupType=MASTER,InstanceCount=3,InstanceType=m5.xlarge InstanceGroupType=CORE,InstanceCount=4,InstanceType=m5.xlarge \
--ec2-attributes KeyName=ec2_key_pair_name,InstanceProfile=EMR_EC2_DefaultRole,SubnetId=subnet-22XXXX01 \
--service-role EMR_DefaultRole \
--applications Name=Hadoop Name=Spark
```
+ Replace *ha\-cluster* with the name of your high\-availability cluster\.
+ Replace *subnet\-22XXXX01* with your subnet ID\.
+ Replace the *ec2\_key\_pair\_name* with the name of your EC2 key pair for this cluster\. EC2 key pair is optional and only required if you want to use SSH to access your cluster\.

## Checking placement group strategy configuration attached to the Amazon EMR multiple master cluster<a name="emr-plan-ha-check-pg-using-api"></a>

You can use the Amazon EMR describe cluster API to see the placement group strategy configuration attached to the Amazon EMR multiple master cluster\.

**Example**  

```
aws emr describe-cluster --cluster-id "j-xxxxx"
{
   "Cluster":{
      "Id":"j-xxxxx",
      ...
      ...
      "PlacementGroups":[
         {
            "InstanceRole":"MASTER",
            "PlacementStrategy":"SPREAD"
         }
      ]
   }
}
```