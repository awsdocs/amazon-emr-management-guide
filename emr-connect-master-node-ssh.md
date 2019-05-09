# Connect to the Master Node Using SSH<a name="emr-connect-master-node-ssh"></a>

Secure Shell \(SSH\) is a network protocol you can use to create a secure connection to a remote computer\. After you make a connection, the terminal on your local computer behaves as if it is running on the remote computer\. Commands you issue locally run on the remote computer, and the command output from the remote computer appears in your terminal window\. 

When you use SSH with AWS, you are connecting to an EC2 instance, which is a virtual server running in the cloud\. When working with Amazon EMR, the most common use of SSH is to connect to the EC2 instance that is acting as the master node of the cluster\.

Using SSH to connect to the master node gives you the ability to monitor and interact with the cluster\. You can issue Linux commands on the master node, run applications such as Hive and Pig interactively, browse directories, read log files, and so on\. You can also create a tunnel in your SSH connection to view the web interfaces hosted on the master node\. For more information, see [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)\.

To connect to the master node using SSH, you need the public DNS name of the master node\. In addition, the security group associated with the master node must have an inbound rule that allows SSH \(TCP port 22\) traffic from a source that includes the client where the SSH connection originates\. The default ElasticMapReduce\-master security group does not permit inbound SSH access by default\. You may need to the rule\. For more information about modifying security group rules, see [Adding Rules to a Security Group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide for Linux Instances*\.

## Retrieve the Public DNS Name of the Master Node<a name="emr-connect-master-dns"></a>

You can retrieve the master public DNS name using the Amazon EMR console and the AWS CLI\. <a name="public-dns-name-master"></a>

**To retrieve the public DNS name of the master node using the Amazon EMR console**

1. Open the Amazon EMR console at [https://console\.aws\.amazon\.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. On the **Cluster List** page, select the link for your cluster\.

1. Note the **Master public DNS** value that appears at the top of the **Cluster Details** page\.   
![\[Get the master public DNS name\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/master-dns-setting-new.png)
**Note**  
You may also choose the **SSH** link beside the master public DNS name for instructions on creating an SSH connection with the master node\.   

![\[SSH instructions\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/master-dns-setting-newcon.png)<a name="emr-connect-master-dns-cli"></a>

**To retrieve the public DNS name of the master node using the AWS CLI**

1. To retrieve the cluster identifier, type the following command\.

   ```
   1. aws emr list-clusters
   ```

   The output lists your clusters including the cluster IDs\. Note the cluster ID for the cluster to which you are connecting\.

   ```
   "Status": {
       "Timeline": {
           "ReadyDateTime": 1408040782.374,
           "CreationDateTime": 1408040501.213
       },
       "State": "WAITING",
       "StateChangeReason": {
           "Message": "Waiting after step completed"
       }
   },
   "NormalizedInstanceHours": 4,
   "Id": "j-2AL4XXXXXX5T9",
   "Name": "My cluster"
   ```

1. To list the cluster instances including the master public DNS name for the cluster, type one of the following commands\. Replace *j\-2AL4XXXXXX5T9* with the cluster ID returned by the previous command\.

   ```
   1. aws emr list-instances --cluster-id j-2AL4XXXXXX5T9
   ```

   Or:

   ```
   aws emr describe-cluster --cluster-id j-2AL4XXXXXX5T9
   ```

   The output lists the cluster instances including DNS names and IP addresses\. Note the value for `PublicDnsName`\. 

   ```
   "Status": {
       "Timeline": {
           "ReadyDateTime": 1408040779.263,
           "CreationDateTime": 1408040515.535
       },
       "State": "RUNNING",
       "StateChangeReason": {}
   },
   "Ec2InstanceId": "i-e89b45e7",
   "PublicDnsName": "ec2-###-##-##-###.us-west-2.compute.amazonaws.com"
   
   "PrivateDnsName": "ip-###-##-##-###.us-west-2.compute.internal",
   "PublicIpAddress": "##.###.###.##",
   "Id": "ci-12XXXXXXXXFMH",
   "PrivateIpAddress": "###.##.#.###"
   ```

For more information, see [Amazon EMR commands in the AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

## Connect to the Master Node Using SSH and an Amazon EC2 Private Key on Linux, Unix, and Mac OS X<a name="emr-connect-linux"></a>

To create an SSH connection authenticated with a private key file, you need to specify the Amazon EC2 key pair private key when you launch a cluster\. If you launch a cluster from the console, the Amazon EC2 key pair private key is specified in the **Security and Access** section on the **Create Cluster** page\. For more information about accessing your key pair, see [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

Your Linux computer most likely includes an SSH client by default\. For example, OpenSSH is installed on most Linux, Unix, and macOS operating systems\. You can check for an SSH client by typing ssh at the command line\. If your computer does not recognize the command, install an SSH client to connect to the master node\. The OpenSSH project provides a free implementation of the full suite of SSH tools\. For more information, see the [OpenSSH](http://www.openssh.org/) website\.

The following instructions demonstrate opening an SSH connection to the Amazon EMR master node on Linux, Unix, and Mac OS X\. <a name="emr-keypair-file-permission-config"></a>

**To configure the key pair private key file permissions**

Before you can use your Amazon EC2 key pair private key to create an SSH connection, you must set permissions on the `.pem` file so that only the key owner has permission to access the file\. This is required for creating an SSH connection using terminal or the AWS CLI\.

1. Locate your `.pem` file\. These instructions assume that the file is named `mykeypair.pem` and that it is stored in the current user's home directory\.

1. Type the following command to set the permissions\. Replace *\~/mykeypair\.pem* with the location and file name of your key pair private key file\.

   ```
   1. chmod 400 ~/mykeypair.pem
   ```

   If you do not set permissions on the `.pem` file, you will receive an error indicating that your key file is unprotected and the key will be rejected\. To connect, you only need to set permissions on the key pair private key file the first time you use it\.<a name="emr-ssh"></a>

**To connect to the master node using the terminal**

1. Open a terminal window\. On Mac OS X, choose **Applications > Utilities > Terminal**\. On other Linux distributions, terminal is typically found at **Applications > Accessories > Terminal**\.

1. To establish a connection to the master node, type the following command\. Replace *ec2\-\#\#\#\-\#\#\-\#\#\-\#\#\#\.compute\-1\.amazonaws\.com* with the master public DNS name of your cluster and replace *\~/mykeypair\.pem* with the location and file name of your `.pem` file\.

   ```
   1. ssh hadoop@ec2-###-##-##-###.compute-1.amazonaws.com -i ~/mykeypair.pem
   ```
**Important**  
You must use the login name `hadoop` when you connect to the Amazon EMR master node; otherwise, you may see an error similar to `Server refused our key`\.

1. A warning states that the authenticity of the host you are connecting to cannot be verified\. Type `yes` to continue\.

1.  When you are done working on the master node, type the following command to close the SSH connection\. 

   ```
   exit
   ```

## Connect to the Master Node Using the AWS CLI<a name="emr-connect-cli"></a>

You can create an SSH connection with the master node using the AWS CLI on Windows and on Linux, Unix, and Mac OS X\. Regardless of the platform, you need the public DNS name of the master node and your Amazon EC2 key pair private key\. If you are using the AWS CLI on Linux, Unix, or Mac OS X, you must also set permissions on the private key \(`.pem` or `.ppk`\) file as shown in [To configure the key pair private key file permissions](#emr-keypair-file-permission-config)\.<a name="emr-ssh-cli"></a>

**To connect to the master node using the AWS CLI**

1. To retrieve the cluster identifier, type:

   ```
   1. aws emr list-clusters
   ```

   The output lists your clusters including the cluster IDs\. Note the cluster ID for the cluster to which you are connecting\.

   ```
   "Status": {
       "Timeline": {
           "ReadyDateTime": 1408040782.374,
           "CreationDateTime": 1408040501.213
       },
       "State": "WAITING",
       "StateChangeReason": {
           "Message": "Waiting after step completed"
       }
   },
   "NormalizedInstanceHours": 4,
   "Id": "j-2AL4XXXXXX5T9",
   "Name": "AWS CLI cluster"
   ```

1. Type the following command to open an SSH connection to the master node\. In the following example, replace *j\-2AL4XXXXXX5T9* with the cluster ID and replace *\~/mykeypair\.key* with the location and file name of your `.pem` file \(for Linux, Unix, and Mac OS X\) or `.ppk` file \(for Windows\)\.

   ```
   aws emr ssh --cluster-id j-2AL4XXXXXX5T9 --key-pair-file ~/mykeypair.key						
   ```

1. When you are done working on the master node, close the AWS CLI window\. 

   For more information, see [Amazon EMR commands in the AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

## Connect to the Master Node Using SSH on Windows<a name="emr-connect-win"></a>

Windows users can use an SSH client such as PuTTY to connect to the master node\. Before connecting to the Amazon EMR master node, you should download and install PuTTY and PuTTYgen\. You can download these tools from the [PuTTY download page](http://www.chiark.greenend.org.uk/~sgtatham/putty/)\.

PuTTY does not natively support the key pair private key file format \(`.pem`\) generated by Amazon EC2\. You use PuTTYgen to convert your key file to the required PuTTY format \(`.ppk`\)\. You must convert your key into this format \(`.ppk`\) before attempting to connect to the master node using PuTTY\.

For more information about converting your key, see [Converting Your Private Key Using PuTTYgen](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) in the *Amazon EC2 User Guide for Linux Instances*\.<a name="emr-ssh-windows"></a>

**To connect to the master node using PuTTY**

1. Open `putty.exe`\. You can also launch PuTTY from the Windows programs list\. 

1. If necessary, in the **Category** list, choose **Session**\.

1. For **Host Name \(or IP address\)**, type `hadoop@`*MasterPublicDNS*\. For example: `hadoop@`*ec2\-\#\#\#\-\#\#\-\#\#\-\#\#\#\.compute\-1\.amazonaws\.com*\. 

1. In the **Category** list, choose **Connection > SSH**, **Auth**\.

1. For **Private key file for authentication**, choose **Browse** and select the `.ppk` file that you generated\. 

1. Choose **Open** and then **Yes** to dismiss the PuTTY security alert\. 
**Important**  
When logging into the master node, type `hadoop` if you are prompted for a user name \.

1. When you are done working on the master node, you can close the SSH connection by closing PuTTY\.
**Note**  
To prevent the SSH connection from timing out, you can choose **Connection** in the **Category** list and select the option **Enable TCP\_keepalives**\. If you have an active SSH session in PuTTY, you can change your settings by opening the context \(right\-click\) for the PuTTY title bar and choosing **Change Settings**\.