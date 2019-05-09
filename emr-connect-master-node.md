# Connect to the Cluster<a name="emr-connect-master-node"></a>

When you run an Amazon EMR cluster, often all you need to do is run an application to analyze your data and then collect the output from an Amazon S3 bucket\. At other times, you may want to interact with the master node while the cluster is running\. For example, you may want to connect to the master node to run interactive queries, check log files, debug a problem with the cluster, monitor performance using an application such as Ganglia that runs on the master node, and so on\. The following sections describe techniques that you can use to connect to the master node\. 

In an EMR cluster, the master node is an Amazon EC2 instance that coordinates the EC2 instances that are running as task and core nodes\. The master node exposes a public DNS name that you can use to connect to it\. By default, Amazon EMR creates security group rules for the master node, and for core and task nodes, that determine how you access the nodes\.

**Note**  
You can connect to the master node only while the cluster is running\. When the cluster terminates, the EC2 instance acting as the master node is terminated and is no longer available\. To connect to the master node, you must also authenticate to the cluster\. You can either use Kerberos for authentication, or specify an Amazon EC2 key pair private key when you launch the cluster\. For more information about configuring Kerberos, and then connecting, see [Use Kerberos Authentication](emr-kerberos.md)\. When you launch a cluster from the console, the Amazon EC2 key pair private key is specified in the **Security and Access** section on the **Create Cluster** page\. 

By default, the ElasticMapReduce\-master security group does not permit inbound SSH access\. You may need to add an inbound rule that allows SSH access \(TCP port 22\) from the sources you want to have access\. For more information about modifying security group rules, see [Adding Rules to a Security Group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Important**  
Do not modify the remaining rules in the ElasticMapReduce\-master security group\. Modifying these rules may interfere with the operation of the cluster\. 

**Topics**
+ [Connect to the Master Node Using SSH](emr-connect-master-node-ssh.md)
+ [View Web Interfaces Hosted on Amazon EMR Clusters](emr-web-interfaces.md)