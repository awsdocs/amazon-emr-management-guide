# Option 1: Set up an SSH tunnel to the master node using local port forwarding<a name="emr-ssh-tunnel-local"></a>

To connect to the local web server on the master node, you create an SSH tunnel between your computer and the master node\. This is also known as *port forwarding*\. If you do not wish to use a SOCKS proxy, you can set up an SSH tunnel to the master node using local port forwarding\. With local port forwarding, you specify unused local ports that are used to forward traffic to specific remote ports on the master node's local web server\. 

Setting up an SSH tunnel using local port forwarding requires the public DNS name of the master node and your key pair private key file\. For information about how to locate the master public DNS name, see [To retrieve the public DNS name of the master node using the Amazon EMR console](emr-connect-master-node-ssh.md#public-dns-name-master)\. For more information about accessing your key pair, see [Amazon EC2 key pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\. For more information about the sites you might want to view on the master node, see [View web interfaces hosted on Amazon EMR clusters](emr-web-interfaces.md)\.

## Set up an SSH tunnel to the master node using local port forwarding with OpenSSH<a name="ssh-tunnel-local-linux"></a><a name="tunnel-local-linux"></a>

**To set up an SSH tunnel using local port forwarding in terminal**

1. Ensure you've allowed inbound SSH traffic\. For instructions, see [Before you connect: Authorize inbound traffic](emr-connect-ssh-prereqs.md)\.

1. Open a terminal window\. On Mac OS X, choose **Applications > Utilities > Terminal**\. On other Linux distributions, terminal is typically found at **Applications > Accessories > Terminal**\.

1. Type the following command to open an SSH tunnel on your local machine\. This example command accesses the ResourceManager web interface by forwarding traffic on local port 8157 \(a randomly chosen unused local port\) to port 8088 on the master node's local web server\. 

   In the command, replace *\~/mykeypair\.pem* with the location and file name of your `.pem` file and replace *ec2\-\#\#\#\-\#\#\-\#\#\-\#\#\#\.compute\-1\.amazonaws\.com* with the master public DNS name of your cluster\. To access a different web interface, replace `8088` with the appropriate port number\. For example, replace `8088` with `8890` for the Zeppelin interface\.

   ```
   ssh -i ~/mykeypair.pem -N -L 8157:ec2-###-##-##-###.compute-1.amazonaws.com:8088 hadoop@ec2-###-##-##-###.compute-1.amazonaws.com
   ```

   `-L` signifies the use of local port forwarding which allows you to specify a local port used to forward data to the identified remote port on the master node's local web server\.

   After you issue this command, the terminal remains open and does not return a response\. 

1. To open the ResourceManager web interface in your browser, type `http://localhost:8157/` in the address bar\. 

1. When you are done working with the web interfaces on the master node, close the terminal windows\.
