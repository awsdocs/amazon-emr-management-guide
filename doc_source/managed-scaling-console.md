# Use the AWS Management Console to configure managed scaling<a name="managed-scaling-console"></a>

When you create a cluster, you can configure managed scaling using either quick options or advanced cluster configuration options\. You can also create or modify a managed scaling policy for a running cluster by modifying the **Managed Scaling** settings on the **Summary** or **Hardware** page\.

**To use quick options to configure managed scaling when you create a cluster**

1. Open the Amazon EMR console, choose **Create cluster** and open **Create Cluster \- Quick options**\. 

1. In the **Hardware configuration** section next to **Cluster scaling**, choose the checkbox to enable **scale cluster nodes based on workload**\.   
![\[Managed Scaling Quick Options\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/Managed_Scaling_Quick_Options.png)

1. Under **Core and task units**, specify the **Minimum** and **Maximum** number of core and task instances\.

**To use the advanced option to configure managed scaling when you create a cluster**

1. In the Amazon EMR console, select **Create cluster**, select **Go to advanced options**, choose options for **Step 1: Software and Steps**, and then go to **Step 2: Hardware Configuration**\.

1. In the **Cluster composition** section, select **Instance fleets** or **Uniform instance groups**\.

1. Under **Cluster scaling**, select **Enable cluster scaling**\. Then select **Use EMR\-managed scaling**\. Under **Core and task units**, specify the **Minimum** and **Maximum** number of instances or instance fleet units, the **On\-Demand limit**, and **Maximum Core Node** count\.  
![\[Managed Scaling Advanced Option\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/Managed_Scaling_Advanced_Option.png)

   For clusters composed of instance groups, you can also choose **Create a custom automatic scaling policy** if you want to define custom automatic scaling policies for each instance group\. For more information, see [Using automatic scaling with a custom policy for instance groups ](emr-automatic-scaling.md)\. 

**To modify an existing cluster**

1. Open the Amazon EMR console, select your cluster from the cluster list, and then choose the **Hardware** tab\.

1. In the **Cluster scaling** section, select **Edit** for EMR managed scaling\.   
![\[Modifying Console options for cluster scaling\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/Managed_Scaling_Hardware.png)

1. In the **Cluster scaling** section, specify new values for the **Minimum** and **Maximum** number of instances and the **On\-Demand limit**\. 