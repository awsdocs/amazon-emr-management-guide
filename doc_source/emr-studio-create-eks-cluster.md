# Set Up Amazon EMR on EKS for Your Studio<a name="emr-studio-create-eks-cluster"></a>


|  | 
| --- |
| The Amazon EMR on EKS managed endpoint feature is in preview release and is subject to change\. The feature is provided as a Preview service as defined in the AWS Service Terms\. | 

## About Amazon EMR on EKS Clusters for EMR Studio<a name="emr-studio-about-eks-clusters"></a>

Amazon EMR Studio works with Amazon EMR on EKS so that Studio users can submit notebook code to Amazon Elastic Kubernetes Service \(EKS\)\. To provide Studio users access to EKS clusters, you must first create a virtual cluster using Amazon EMR on EKS\. After you create a virtual cluster, you must create one or more managed endpoints to which Studio users can connect a Workspace\. For more information about Amazon EMR on EKS, see the [https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/emr-eks.html](https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/emr-eks.html)\.

The following components work together to let Studio users submit jobs to Amazon EMR on EKS:

**Amazon EMR on EKS virtual cluster**  
A virtual cluster is a registered handle to the Kubernetes namespace on an EKS cluster, and is managed by Amazon EMR on EKS\. The handle allows Amazon EMR to use the Kubernetes namespace as a destination for running jobs and endpoints\. The EMR Studio user interface refers to virtual clusters as EMR on EKS clusters\.

**Amazon EMR on EKS managed endpoint**  
After you create a virtual cluster for Amazon EMR on EKS, you must set up one or more managed Jupyter Enterprise Gateway endpoints to which Studio users can connect a Workspace\. These HTTPS endpoints are only reachable from your EMR Studio, and are created in a private subnet of your EKS cluster's Amazon Virtual Private Cloud\.  
The Python and PySpark kernels use the permissions defined in your Amazon EMR on EKS job execution role to invoke other AWS services\. All kernels and users that connect to the managed endpoint use this same role\. We recommend creating separate endpoints for different users who should not share the same IAM role\. For more information, see [Update trust policy in IAM roles for Job execution](https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/setting-up.html#setting-up-trust-policy) in the *Amazon EMR on EKS Developer Guide*

**AWS Load Balancer Controller**  
The AWS Load Balancer Controller \(formerly called the AWS ALB Ingress Controller\) creates an Application Load Balancer \(ALB\) for each managed endpoint that you create\. An ALB balances application traffic across Kubernetes pods\. Each ALB is HTTPS\-enabled and uses a private IP address within its VPC\. You only need to set up one AWS Load Balancer Controller per EKS cluster\. For more information, see [Application load balancing on Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html)\.   
When you set up a managed endpoint for EMR Studio, Amazon EMR on EKS creates two default security groups to control inbound traffic and help ensure that only your EMR Studio can communicate with the ALB\. When you delete a managed endpoint, EMR on EKS deletes the ALB and default security groups\.  
Additional charges accrue for the load balancer that Amazon EMR on EKS provisions for each managed endpoint that you create\.

**AWS Certificate Manager \(ACM\) certificate**  
A TLS certificate used by the AWS Load Balancer Controller to enable secure HTTPS communication with an endpoint's application load balancer\. You specify this certificate when you create a managed endpoint\. 

## Prerequisites<a name="emr-studio-emr-on-eks-prereqs"></a>

Before you can set up Amazon EMR on EKS for Amazon EMR Studio, you must have the following items:
+ Make sure that the IAM principal \(user or role\) that you use to set up EMR on EKS for EMR Studio has the following Amazon EC2 permissions\. These permissions enable Amazon EMR on EKS to create, manage, and delete the security groups that limit inbound traffic to your managed endpoint's load balancer\.

  ```
  "ec2:CreateSecurityGroup",
  "ec2:DeleteSecurityGroup",
  "ec2:AuthorizeSecurityGroupEgress",
  "ec2:AuthorizeSecurityGroupIngress",
  "ec2:RevokeSecurityGroupEgress",
  "ec2:RevokeSecurityGroupIngress",
  "ec2:DeleteSecurityGroup"
  ```
+ A designated Amazon EKS cluster that you want to register as a virtual cluster with Amazon EMR on EKS\. Your cluster should be in the same Amazon Virtual Private Cloud as your Studio, and must have at least one private subnet to enable managed endpoints and linking Git\-based repositories\. For more information about EKS clusters, see [Creating an Amazon EKS cluster](https://docs.aws.amazon.com/eks/latest/userguide/create-cluster.html)\. 
**Note**  
There must be *at least one private subnet in common* between your EMR Studio and the Amazon EKS cluster that you use to register your virtual cluster\. Otherwise, your managed endpoint will not appear as an option in your Studio Workspaces\. You can create an Amazon EKS cluster and associate it with the subnets that belong to your Studio\. Alternatively, you can create a Studio and specify your EKS cluster's private subnets\.
[Amazon EKS optimized Arm Amazon Linux AMIs](https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html#arm-ami) are not supported for EMR on EKS managed endpoints\.
EMR Studio does not currently support Amazon EMR on EKS when you use an AWS Fargate\-only Amazon EKS cluster\.
+ An AWS Load Balancer Controller for your Amazon EKS cluster\. For instructions, see [Application load balancing on Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html)\. You only need to set up one AWS Load Balancer Controller per EKS cluster\.
+ A designated AWS Certificate Manager \(ACM\) certificate for EMR Studio\. For information about how to create a certificate, see [Issuing and Managing Certificates](https://docs.aws.amazon.com/acm/latest/userguide/gs.html) in the *AWS Certificate Manager User Guide*\. Note the Amazon Resource Name \(ARN\) of the certificate, which you use when you create a managed endpoint\.
+ The Amazon Resource Name \(ARN\) of your job execution IAM role for Amazon EMR on EKS\. For more information, see [Update trust policy in IAM roles for Job execution](https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/setting-up.html#setting-up-trust-policy)\. If you have never used Amazon EMR on EKS, you can retrieve the job execution role ARN in [Step 1: Create a Virtual Cluster](#emr-studio-set-up-virtual-cluster)\.

## Step 1: Create a Virtual Cluster<a name="emr-studio-set-up-virtual-cluster"></a>

You must create a virtual cluster using Amazon EMR on EKS before you create a managed endpoint\.


| If you have \.\.\. | Do this\.\.\. | 
| --- | --- | 
| Never used Amazon EMR on EKS | Follow the steps in [Setting Up](https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/setting-up.html) to configure Amazon EMR on EKS and create a virtual cluster\. Make sure you note the ARN of your job execution IAM role and your virtual cluster ID\. | 
| Previously set up Amazon EMR on EKS | Follow the steps in [Create a virtual cluster](https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/virtual-cluster.html#create-virtul-cluster) to register your Amazon EKS cluster with EMR on EKS\. Make sure you note your virtual cluster ID\. | 

For more information about virtual clusters, see the [Virtual Cluster](https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/virtual-cluster.html) topic in the *Amazon EMR on EKS Development Guide*\.

## Step 2: Create a Managed Endpoint for Your Virtual Cluster<a name="emr-studio-create-managed-endpoint"></a>

Create a managed endpoint using the `emr-containers create-managed-endpoint` command\. Specify values for the following options:
+ `--type` – Use `JUPYTER_ENTERPRISE_GATEWAY`\.
+ `--virtual-cluster-id` – The ID of the virtual cluster that you registered with EMR on EKS\.
+ `--name` – A descriptive name for the managed endpoint to help EMR Studio users select it from a list\.
+ `--execution-role-arn` – The Amazon Resource Name \(ARN\) of your job execution IAM role for EMR on EKS\.
+ `--release-label` – The release label of the Amazon EMR version to use when creating the endpoint\. Amazon EMR on EKS currently supports creating managed endpoints using the following Amazon EMR release labels: 5\.32\.0, emr\-6\.2\.0\-latest\.
+ `--certificate-arn` – The Amazon Resource Name \(ARN\) of the AWS Certificate Manager certificate that you want to associate with your load balancer to enable HTTPS communication\.

```
aws emr-containers create-managed-endpoint \
--type JUPYTER_ENTERPRISE_GATEWAY \
--virtual-cluster-id <0b0qvauoy3ch1nqodxxxxxxxx> \
--name <example-endpoint-name> \
--execution-role-arn arn:aws:iam::<aws-account-id>:role/<EKSClusterRole> \
--release-label <emr-6.2.0-latest>
--certificate-arn <arn:aws:acm:region:123xxxxxxxxx:certificate/12345678-xxxx-xxxx-xxxx-123456789012>
```

**Note**  
`emr-containers` is the name used to describe Amazon EMR on EKS within the AWS CLI context\.

You should see the following output in the terminal, which includes the name and ID of your new managed endpoint\.

```
{
    "arn": "arn:aws:emr-containers:us-west-2:061xxxxxxxxx:/virtualclusters/0b0qvauoy3ch1nqodxxxxxxxx/endpoints/8gai4l4exxxxx",
    "name": "example-endpoint-name",
    "virtualClusterId": "0b0qvauoy3ch1nqodxxxxxxxx",
    "id": "8gai4l4exxxxx"
}
```

## Fetch Managed Endpoint Details<a name="emr-studio-describe-managed-endpoint"></a>

After you create a managed endpoint, you can retrieve its details using the `describe-managed-endpoint` AWS CLI command\. Insert your own values for *<managed\-endpoint\-id>* and *<virtual\-cluster\-id>*\.

```
aws emr-containers describe-managed-endpoint --id <managed-endpoint-id> --virtual-cluster-id <virtual-cluster-id>
```

 The command returns details about the specified endpoint such as ARN, ID, and name\.

```
{
   "endpoint": { 
      "arn": "arn:aws:emr-containers:us-west-2:061xxxxxxxxx:/virtualclusters/0b0qvauoy3ch1nqodxxxxxxxx/endpoints/8gai4l4exxxxx",
      "certificateArn": "arn:aws:acm:region:123xxxxxxxxx:certificate/12345678-xxxx-xxxx-xxxx-123456789012",
      "configurationOverrides": { 
         "applicationConfiguration": [ 
            { 
               "classification": "string",
               "configurations": [ 
                  "Configuration"
               ],
               "properties": { 
                  "string" : "string" 
               }
            }
         ],
         "monitoringConfiguration": { 
            "cloudWatchMonitoringConfiguration": { 
               "logGroupName": "string",
               "logStreamNamePrefix": "string"
            },
            "persistentAppUI": "string",
            "s3MonitoringConfiguration": { 
               "logUri": "string"
            }
         }
      },
      "createdAt": number,
      "executionRoleArn": "string",
      "id": "8gai4l4exxxxx",
      "name": "example-endpoint-name",
      "releaseLabel": "emr-6.2.0",
      "securityGroup": "string",
      "serverUrl": "string",
      "state": "string",
      "subnetIds": [ "string" ],
      "tags": { 
         "string" : "string" 
      },
      "type": "string",
      "virtualClusterId": "0b0qvauoy3ch1nqodxxxxxxxx"
   }
}
```

## List All Managed Endpoints Associated with a Virtual Cluster<a name="emr-studio-list-managed-endpoints"></a>

Use the `list-managed-endpoints` AWS CLI command to fetch a list of all the managed endpoints associated with a specified virtual cluster\. Replace *<virtual\-cluster\-id>* with the ID of your virtual cluster\.

```
aws emr-containers list-managed-endpoints --virtual-cluster-id <virtual-cluster-id>
```

## Delete a Managed Endpoint<a name="emr-studio-delete-managed-endpoint"></a>

To delete a managed endpoint associated with an Amazon EMR on EKS virtual cluster, use the `delete-managed-endpoint` AWS CLI command\. When you delete a managed endpoint, Amazon EMR on EKS removes the default security groups that were created for that endpoint\. Specify values for the following options:
+ `--id` – The ID of the managed endpoint that you want to delete\.
+ `--virtual-cluster-id` – The ID of the virtual cluster associated with the managed endpoint that you want to delete\. This is the same virtual cluster ID that was specified when the managed endpoint was created\.

```
aws emr-containers delete-managed-endpoint --id <managed-endpoint-id> --virtual-cluster-id <virtual-cluster-id>
```

The command returns output similar to the following example to confirm that the managed endpoint was deleted\.

```
{
    "id":"8gai4l4exxxxx",
    "virtualClusterId":"0b0qvauoy3ch1nqodxxxxxxxx"
}
```