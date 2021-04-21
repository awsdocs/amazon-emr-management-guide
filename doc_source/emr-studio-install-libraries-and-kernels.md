# Install and Use Kernels and Libraries<a name="emr-studio-install-libraries-and-kernels"></a>

Each EMR Studio Workspace comes with a set of pre\-installed libraries and kernels\. You can also customize the environment for EMR Studio in the following ways when you use EMR clusters running on Amazon EC2:
+ **Install Jupyter Notebook kernels and Python libraries on a cluster master node** – When you install libraries using this option, the libraries are shared by all Workspaces attached to the same cluster and are only available on the master node\. You can install kernels or libraries from within a notebook cell or while connected using SSH to the master node of a cluster\.
+ **Use notebook\-scoped libraries** – This option lets Workspace users install and use libraries from within a notebook cell\. Notebook\-scoped libraries are only available to the notebook that installs them\. This option lets different notebooks that are attached to the same cluster work independently without worrying about conflicting library versions\.

EMR Studio Workspaces have the same underlying architecture as EMR notebooks\. You can install and use Jupyter Notebook kernels and Python libraries with EMR Studio in the same way you would with EMR notebooks\. For instructions, see [Installing and Using Kernels and Libraries](emr-managed-notebooks-installing-libraries-and-kernels.md)\. 

## Kernels and Libraries on Amazon EMR on EKS Clusters<a name="emr-studio-eks-kernels-libraries"></a>

Amazon EMR on EKS clusters include the PySpark and Python 3\.7 kernels with a set of pre\-installed libraries\. Amazon EMR on EKS does not currently support installing additional libraries or clusters\.

Each Amazon EMR on EKS cluster comes with the following Python and PySpark libraries installed:
+ **Python**– boto3, cffi, future, ggplot, jupyter, kubernetes, matplotlib, numpy, pandas, plotly, pycryptodomex, py4j, requests, scikit\-learn, scipy, seaborn
+ **PySpark** – ggplot, jupyter, matplotlib, numpy, pandas, plotly, pycryptodomex, py4j, requests, scikit\-learn, scipy, seaborn