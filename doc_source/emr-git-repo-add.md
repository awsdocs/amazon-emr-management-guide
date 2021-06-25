# Add a Git\-based repository to Amazon EMR<a name="emr-git-repo-add"></a>

**To add a Git\-based repository as a resource in your Amazon EMR account**

1. Open the Amazon EMR console at [https://console.aws.amazon.com/elasticmapreduce/](https://console.aws.amazon.com/elasticmapreduce/)\.

1. Choose **Git repositories**, and then choose **Add repository**\.

1. For **Repository name**, enter a name to use for the repository in Amazon EMR\. 

   Names may only contain alphanumeric characters, hyphens \(\-\), or underscores \(\_\)\.

1. For **Git repository URL**, enter the URL for the repository\. When using a CodeCommit repository, this is the URL that is copied when you choose **Clone URL** and then **Clone HTTPS**, for example, `https://git-codecommit.us-west-2.amazonaws.com/v1/repos/MyCodeCommitRepoName`\.

1. For **Branch**, enter a branch name\. 

1. For **Git credentials**, choose options according to the following guidelines\. You can use a Git user name and password or a personal access token \(PAT\) to authenticate to your repository\. EMR Notebooks accesses your Git credentials using secrets stored in Secrets Manager\.
**Note**  
If you use a GitHub repository, we recommend that you use a personal access token \(PAT\) to authenticate\. Beginning August 13, 2021, GitHub will no longer accept passwords when authenticating Git operations\. For more information, see the [Token authentication requirements for Git operations](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/) post in *The GitHub Blog*\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-git-repo-add.html)

1. Choose **Add repository**\. 