# Option 2, Part 1: Set Up an SSH Tunnel to the Master Node Using Dynamic Port Forwarding<a name="emr-ssh-tunnel"></a>

To connect to the local web server on the master node, you create an SSH tunnel between your computer and the master node\. This is also known as *port forwarding*\. If you create your SSH tunnel using dynamic port forwarding, all traffic routed to a specified unused local port is forwarded to the local web server on the master node\. This creates a SOCKS proxy\. You can then configure your Internet browser to use an add\-on such as FoxyProxy or SwitchySharp to manage your SOCKS proxy settings\. Using a proxy management add\-on allows you to automatically filter URLs based on text patterns and to limit the proxy settings to domains that match the form of the master node's public DNS name\. The browser add\-on automatically handles turning the proxy on and off when you switch between viewing websites hosted on the master node, and those on the Internet\. 

Before you begin, you need the public DNS name of the master node and your key pair private key file\. For information about how to locate the master public DNS name, see [To retrieve the public DNS name of the master node using the Amazon EMR console](emr-connect-master-node-ssh.md#public-dns-name-master)\. For more information about accessing your key pair, see [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\. For more information about the sites you might want to view on the master node, see [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)\.

## Set Up an SSH Tunnel to the Master Node Using Dynamic Port Forwarding on Linux, Unix, and Mac OS X<a name="emr-ssh-tunnel-linux"></a><a name="emr-ssh-tunnel-unix"></a>

**To set up an SSH tunnel using dynamic port forwarding on Linux, Unix, and Mac OS X**

1. Open a terminal window\. On Mac OS X, choose **Applications > Utilities > Terminal**\. On other Linux distributions, terminal is typically found at **Applications > Accessories > Terminal**\.

1. Type the following command to open an SSH tunnel on your local machine\. Replace *\~/mykeypair\.pem* with the location and file name of your `.pem` file, replace *8157* with an unused, local port number, and replace *c2\-\#\#\#\-\#\#\-\#\#\-\#\#\#\.compute\-1\.amazonaws\.com* with the master public DNS name of your cluster\. 

   ```
   1. ssh -i ~/mykeypair.pem -N -D 8157 hadoop@ec2-###-##-##-###.compute-1.amazonaws.com
   ```

   After you issue this command, the terminal remains open and does not return a response\. 
**Note**  
`-D` signifies the use of dynamic port forwarding which allows you to specify a local port used to forward data to all remote ports on the master node's local web server\. Dynamic port forwarding creates a local SOCKS proxy listening on the port specified in the command\.

1. After the tunnel is active, configure a SOCKS proxy for your browser\. For more information, see [Option 2, Part 2: Configure Proxy Settings to View Websites Hosted on the Master Node](emr-connect-master-node-proxy.md)\.

1. When you are done working with the web interfaces on the master node, close the terminal window\.

## Set Up an SSH tunnel Using Dynamic Port Forwarding with the AWS CLI<a name="emr-ssh-tunnel-cli"></a>

You can create an SSH connection with the master node using the AWS CLI on Windows and on Linux, Unix, and Mac OS X\. If you are using the AWS CLI on Linux, Unix, or Mac OS X, you must set permissions on the `.pem` file as shown in [To configure the key pair private key file permissions](emr-connect-master-node-ssh.md#emr-keypair-file-permission-config)\. If you are using the AWS CLI on Windows, PuTTY must appear in the path environment variable or you may receive an error such as OpenSSH or PuTTY not available\.<a name="ssh-tunnel-cli"></a>

**To set up an SSH tunnel using dynamic port forwarding with the AWS CLI**

1. Create an SSH connection with the master node as shown in [Connect to the Master Node Using the AWS CLI](emr-connect-master-node-ssh.md#emr-connect-cli)\. 

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

1. Type the following command to open an SSH tunnel to the master node using dynamic port forwarding\. In the following example, replace *j\-2AL4XXXXXX5T9* with the cluster ID and replace *\~/mykeypair\.key* with the location and file name of your `.pem` file \(for Linux, Unix, and Mac OS X\) or `.ppk` file \(for Windows\)\.

   ```
   aws emr socks --cluster-id j-2AL4XXXXXX5T9 --key-pair-file ~/mykeypair.key						
   ```
**Note**  
The socks command automatically configures dynamic port forwarding on local port 8157\. Currently, this setting cannot be modified\.

1. After the tunnel is active, configure a SOCKS proxy for your browser\. For more information, see [Option 2, Part 2: Configure Proxy Settings to View Websites Hosted on the Master Node](emr-connect-master-node-proxy.md)\.

1. When you are done working with the web interfaces on the master node, close the AWS CLI window\. 

   For more information on using Amazon EMR commands in the AWS CLI, see [https://docs.aws.amazon.com/cli/latest/reference/emr](https://docs.aws.amazon.com/cli/latest/reference/emr)\.

## Set Up an SSH Tunnel to the Master Node Using Dynamic Port Forwarding on Windows<a name="emr-ssh-tunnel-win"></a>

Windows users can use an SSH client such as PuTTY to create an SSH tunnel to the master node\. Before connecting to the Amazon EMR master node, you should download and install PuTTY and PuTTYgen\. You can download these tools from the [PuTTY download page](http://www.chiark.greenend.org.uk/~sgtatham/putty/)\.

PuTTY does not natively support the key pair private key file format \(`.pem`\) generated by Amazon EC2\. You use PuTTYgen to convert your key file to the required PuTTY format \(`.ppk`\)\. You must convert your key into this format \(`.ppk`\) before attempting to connect to the master node using PuTTY\.

For more information about converting your key, see [Converting Your Private Key Using PuTTYgen](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) in the *Amazon EC2 User Guide for Linux Instances*\.<a name="emr-ssh-tunnel-putty"></a>

**To set up an SSH tunnel using dynamic port forwarding on Windows**

1. Double\-click `putty.exe` to start PuTTY\. You can also launch PuTTY from the Windows programs list\. 
**Note**  
If you already have an active SSH session with the master node, you can add a tunnel by right\-clicking the PuTTY title bar and choosing **Change Settings**\. 

1. If necessary, in the **Category** list, choose **Session**\.

1. In the **Host Name** field, type **hadoop@***MasterPublicDNS*\. For example: **hadoop@***ec2\-\#\#\#\-\#\#\-\#\#\-\#\#\#\.compute\-1\.amazonaws\.com*\. 

1. In the **Category** list, expand **Connection > SSH**, and then choose **Auth**\.

1. For **Private key file for authentication**, choose **Browse** and select the `.ppk` file that you generated\. 
**Note**  
PuTTY does not natively support the key pair private key file format \(`.pem`\) generated by Amazon EC2\. You use PuTTYgen to convert your key file to the required PuTTY format \(`.ppk`\)\. You must convert your key into this format \(`.ppk`\) before attempting to connect to the master node using PuTTY\.

1. In the **Category** list, expand **Connection > SSH**, and then choose **Tunnels**\. 

1. In the **Source port** field, type `8157` \(an unused local port\)\.

1. Leave the **Destination** field blank\.

1. Select the **Dynamic** and **Auto** options\.

1. Choose **Add** and **Open**\. 

1. Choose **Yes** to dismiss the PuTTY security alert\.
**Important**  
When you log in to the master node, type `hadoop` if you are prompted for a user name\.

1. After the tunnel is active, configure a SOCKS proxy for your browser\. For more information, see [Option 2, Part 2: Configure Proxy Settings to View Websites Hosted on the Master Node](emr-connect-master-node-proxy.md)\.

1. When you are done working with the web interfaces on the master node, close the PuTTY window\. 