# Tag Clusters<a name="emr-plan-tags"></a>

It can be convenient to categorize your AWS resources in different ways; for example, by purpose, owner, or environment\. You can achieve this in Amazon EMR by assigning custom metadata to your Amazon EMR clusters using tags\. A tag consists of a key and a value, both of which you define\. For Amazon EMR, the cluster is the resource\-level that you can tag\. For example, you could define a set of tags for your account's clusters that helps you track each cluster's owner or identify a production cluster versus a testing cluster\. We recommend that you create a consistent set of tags to meet your organization requirements\.

When you add a tag to an Amazon EMR cluster, the tag is also propagated to each active Amazon EC2 instance associated with the cluster\. Similarly, when you remove a tag from an Amazon EMR cluster, that tag is removed from each associated active Amazon EC2 instance\. 

**Important**  
Use the Amazon EMR console or CLI to manage tags on Amazon EC2 instances that are part of a cluster instead of the Amazon EC2 console or CLI, because changes that you make in Amazon EC2 do not synchronize back to the Amazon EMR tagging system\.

You can identify an Amazon EC2 instance that is part of an Amazon EMR cluster by looking for the following system tags\. In this example, *CORE* is the value for the instance group role and *j\-12345678* is an example job flow \(cluster\) identifier value:
+ aws:elasticmapreduce:instance\-group\-role=*CORE*
+ aws:elasticmapreduce:job\-flow\-id=*j\-12345678*

**Note**  
Amazon EMR and Amazon EC2 interpret your tags as a string of characters with no semantic meaning\.

You can work with tags using the AWS Management Console, the CLI, and the API\.

You can add tags when creating a new Amazon EMR cluster and you can add, edit, or remove tags from a running Amazon EMR cluster\. Editing a tag is a concept that applies to the Amazon EMR console, however using the CLI and API, to edit a tag you remove the old tag and add a new one\. You can edit tag keys and values, and you can remove tags from a resource at any time a cluster is running\. However, you cannot add, edit, or remove tags from a terminated cluster or terminated instances which were previously associated with a cluster that is still active\. In addition, you can set a tag's value to the empty string, but you can't set a tag's value to null\.

If you're using AWS Identity and Access Management \(IAM\) with your Amazon EC2 instances for resource\-based permissions by tag, your IAM policies are applied to tags that Amazon EMR propagates to a cluster’s Amazon EC2 instances\. For Amazon EMR tags to propagate to your Amazon EC2 instances, your IAM policy for Amazon EC2 needs to allow permissions to call the Amazon EC2 CreateTags and DeleteTags APIs\. Also, propagated tags can affect your Amazon EC2's resource\-based permissions\. Tags propagated to Amazon EC2 can be read as conditions in your IAM policy, just like other Amazon EC2 tags\. Keep your IAM policy in mind when adding tags to your Amazon EMR clusters to avoid IAM users having incorrect permissions for a cluster\. To avoid problems, make sure that your IAM policies do not include conditions on tags that you also plan to use on your Amazon EMR clusters\. For more information, see [Controlling Access to Amazon EC2 Resources](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide//UsingIAM.html)\. 