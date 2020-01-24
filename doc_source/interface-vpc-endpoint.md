# Connect to Amazon EMR Using an Interface VPC Endpoint<a name="interface-vpc-endpoint"></a>

You can connect directly to Amazon EMR using an [interface VPC endpoint \(AWS PrivateLink\)](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpce-interface.html) in your Virtual Private Cloud \(VPC\) instead of connecting over the internet\. When you use an interface VPC endpoint, communication between your VPC and Amazon EMR is conducted entirely within the AWS network\. Each VPC endpoint is represented by one or more [Elastic Network Interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) \(ENIs\) with private IP addresses in your VPC subnets\.

The interface VPC endpoint connects your VPC directly to Amazon EMR without an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. The instances in your VPC don't need public IP addresses to communicate with the Amazon EMR API\.

To use Amazon EMR through your VPC, you must connect from an instance that is inside the VPC or connect your private network to your VPC by using an Amazon Virtual Private Network \(VPN\) or AWS Direct Connect\. For information about Amazon VPN, see [VPN Connections](https://docs.aws.amazon.com/vpc/latest/userguide/vpn-connections.html) in the *Amazon Virtual Private Cloud User Guide*\. For information about AWS Direct Connect, see [Creating a Connection](https://docs.aws.amazon.com/directconnect/latest/UserGuide/create-connection.html) in the *AWS Direct Connect User Guide*\.

You can create an interface VPC endpoint to connect to Amazon EMR using the AWS console or AWS Command Line Interface \(AWS CLI\) commands\. For more information, see [Creating an Interface Endpoint](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpce-interface.html#create-interface-endpoint)\.

After you create an interface VPC endpoint, if you enable private DNS hostnames for the endpoint, the default Amazon EMR endpoint resolves to your VPC endpoint\. The default service name endpoint for Amazon EMR is in the following format\.

```
elasticmapreduce.Region.amazonaws.com
```

If you do not enable private DNS hostnames, Amazon VPC provides a DNS endpoint name that you can use in the following format\.

```
VPC_Endpoint_ID.elasticmapreduce.Region.vpce.amazonaws.com
```

For more information, see [Interface VPC Endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-interface.html) in the *Amazon VPC User Guide*\.

Amazon EMR supports making calls to all of its [API Actions](https://docs.aws.amazon.com/emr/latest/APIReference/API_Operations.html) inside your VPC\.

You can attach VPC endpoint policies to a VPC endpoint to control access for IAM principals\. You can also associate security groups with a VPC endpoint to control inbound and outbound access based on the origin and destination of network traffic, such as a range of IP addresses\. For more information, see [Controlling Access to Services with VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html)\.

## Create a VPC Endpoint Policy for Amazon EMR<a name="api-private-link-policy"></a>

You can create a policy for Amazon VPC endpoints for Amazon EMR to specify the following:
+ The principal that can or cannot perform actions
+ The actions that can be performed
+ The resources on which actions can be performed

For more information, see [Controlling Access to Services with VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-access.html) in the *Amazon VPC User Guide*\.

**Example – VPC Endpoint Policy to Deny All Access From a Specified AWS Account**  
The following VPC endpoint policy denies AWS account *123456789012* all access to resources using the endpoint\.  

```
{
    "Statement": [
        {
            "Action": "*",
            "Effect": "Allow",
            "Resource": "*",
            "Principal": "*"
        },
        {
            "Action": "*",
            "Effect": "Deny",
            "Resource": "*",
            "Principal": {
                "AWS": [
                    "123456789012"
                ]
            }
        }
    ]
}
```

**Example – VPC Endpoint Policy to Allow VPC Access Only to a Specified IAM Principal \(User\)**  
The following VPC endpoint policy allows full access only to the IAM user *lijuan* in AWS account *123456789012*\. All other IAM principals are denied access using the endpoint\.  

```
{
    "Statement": [
        {
            "Action": "*",
            "Effect": "Allow",
            "Resource": "*",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::123456789012:user/lijuan"
                ]
            }
        }]
}
```

**Example – VPC Endpoint Policy to Allow Read\-Only EMR Operations**  
The following VPC endpoint policy allows only AWS account *123456789012* to perform the specified Amazon EMR actions\.  
The actions specified provide the equivalent of read\-only access for Amazon EMR\. All other actions on the VPC are denied for the specified account\. All other accounts are denied any access\. For a list of Amazon EMR actions, see [Actions, Resources, and Condition Keys for Amazon EMR](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonelasticmapreduce.html)\.  

```
{
    "Statement": [
        {
            "Action": [
                "elasticmapreduce:DescribeSecurityConfiguration",
                "elasticmapreduce:GetBlockPublicAccessConfiguration",
                "elasticmapreduce:ListBootstrapActions",
                "elasticmapreduce:ViewEventsFromAllClustersInConsole",
                "elasticmapreduce:ListSteps",
                "elasticmapreduce:ListInstanceFleets",
                "elasticmapreduce:DescribeCluster",
                "elasticmapreduce:ListInstanceGroups",
                "elasticmapreduce:DescribeStep",
                "elasticmapreduce:ListInstances",
                "elasticmapreduce:ListSecurityConfigurations",
                "elasticmapreduce:DescribeEditor",
                "elasticmapreduce:ListClusters",
                "elasticmapreduce:ListEditors"            
            ],
            "Effect": "Allow",
            "Resource": "*",
            "Principal": {
                "AWS": [
                    "123456789012"
                ]
            }
        }
    ]
}
```

**Example – VPC Endpoint Policy Denying Access to a Specified Cluster**  

The following VPC endpoint policy allows full access for all accounts and principals, but denies any access for AWS account *123456789012* to actions performed on the Amazon EMR cluster with cluster ID *j\-A1B2CD34EF5G*\. Other Amazon EMR actions that don't support resource\-level permissions for clusters are still allowed\. For a list of Amazon EMR actions and their corresponding resource type, see [Actions, Resources, and Condition Keys for Amazon EMR](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonelasticmapreduce.html)\.

```
{
    "Statement": [
        {
            "Action": "*",
            "Effect": "Allow",
            "Resource": "*",
            "Principal": "*"
        },
        {
            "Action": "*",
            "Effect": "Deny",
            "Resource": "arn:aws:elasticmapreduce:us-west-2:123456789012:cluster/j-A1B2CD34EF5G",
            "Principal": {
                "AWS": [
                    "123456789012"
                ]
            }
        }
    ]
}
```