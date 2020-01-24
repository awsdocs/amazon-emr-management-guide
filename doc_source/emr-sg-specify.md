# Specifying Amazon EMR\-Managed and Additional Security Groups<a name="emr-sg-specify"></a>

You can specify security groups using the AWS Management Console, the AWS CLI, or the EMR API\. If you don't specify security groups, Amazon EMR creates default security groups\. Specifying additional security groups is optional\. You can assign additional security groups for master instances, core and task instances, and service access \(private subnets only\)\.

**To specify security groups using the console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Create cluster**, **Go to advanced options**\.

1. Choose options for your cluster until you reach **Step 4: Security**\.

1. Choose **EC2 Security Groups** to expand the section\.

   Under **EMR managed security groups**, the default managed security groups are selected by default\. If a default doesn't exist in the VPC for **Master**, **Core & Task**, or **Service Access** \(private subnet only\), **Create** appears before the associated security group name\.

1. If you use custom managed security groups, select them from the **EMR managed security groups** lists\.

   If you select a custom managed security group, a message notifies you to select a custom security group for other instances\. You can use only custom or only default managed security groups for a cluster\.

1. Optionally, under **Additional security groups**, choose the pencil icon, select up to four security groups from the list, and then choose **Assign security groups**\. Repeat for each of **Master**, **Core & Task**, and **Service Access** as desired\.

1. Choose **Create Cluster**\.

## Specifying Security Groups Using the AWS CLI<a name="emr-sg-specify-cli"></a>

To specify security groups using the AWS CLI you use the `create-cluster` command with the following parameters of the `--ec2-attributes` option:


| Parameter | Description | 
| --- | --- | 
|  `EmrManagedMasterSecurityGroup`  |  Use this parameter to specify a custom managed security group for the master instance\. If this parameter is specified, `EmrManagedSlaveSecurityGroup` you must also be specified\. For clusters in private subnets, `ServiceAccessSecurityGroup` must also be specified\.  | 
|  `EmrManagedSlaveSecurityGroup`  |  Use this parameter to specify a custom managed security group for core and task instances\. If this parameter is specified, `EmrManagedMasterSecurityGroup` you must also be specified\. For clusters in private subnets, `ServiceAccessSecurityGroup` must also be specified\.  | 
|  `ServiceAccessSecurityGroup`  |  Use this parameter to specify a custom managed security group for service access, which applies only to clusters in private subnets\. If this parameter is specified, `EmrManagedMasterSecurityGroup` must also be specified\.  | 
|  `AdditionalMasterSecurityGroups`  |  Use this parameter to specify up to four additional security groups for the master instance\.  | 
|  `AdditionalSlaveSecurityGroups`  |  Use this parameter to specify up to four additional security groups for core and task instances\.  | 

**Example — Specify Custom Amazon EMR\-Managed Security Groups and Additional Security Groups**  
The following example specifies custom Amazon EMR managed security groups for a cluster in a private subnet, multiple additional security groups for the master instance, and a single additional security group for core and task instances\.  
Linux line continuation characters \(\\\) are included for readability\. They can be removed or used in Linux commands\. For Windows, remove them or replace with a caret \(^\)\.

```
 1. aws emr create-cluster --name "ClusterCustomManagedAndAdditionalSGs" \
 2. --release-label emr-emr-5.29.0 --applications Name=Hue Name=Hive \
 3. Name=Pig --use-default-roles --ec2-attributes \
 4. SubnetIds=subnet-xxxxxxxxxxxx,KeyName=myKey,\
 5. ServiceAccessSecurityGroup=sg-xxxxxxxxxxxx,\
 6. EmrManagedMasterSecurityGroup=sg-xxxxxxxxxxxx,\
 7. EmrManagedSlaveSecurityGroup=sg-xxxxxxxxxxx,\
 8. AdditionalMasterSecurityGroups=['sg-xxxxxxxxxxx',\
 9. 'sg-xxxxxxxxxxx','sg-xxxxxxxxxx'],\
10. AdditionalSlaveSecurityGroups=sg-xxxxxxxxxxx \
11. --instance-type m5.xlarge
```

For more information, see [create\-cluster](https://docs.aws.amazon.com/cli/latest/reference/emr/create-cluster.html) in the *AWS CLI Command Reference*\.