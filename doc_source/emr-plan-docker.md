# Configure Docker<a name="emr-plan-docker"></a>

Amazon EMR 6\.x supports Hadoop 3, which allows the YARN NodeManager to launch containers either directly on the EMR cluster host or inside a Docker container\. Docker containers provide custom execution environments in which application code runs\. The custom execution environment is isolated from the execution environment of the YARN NodeManager and other applications\.

Docker containers can include special libraries used by the application and they can provide different versions of native tools and libraries, such as R and Python\. You can use familiar Docker tooling to define libraries and runtime dependencies for your applications\.

Amazon EMR 6\.x clusters are configured by default to allow YARN applications, such as Spark, to run using Docker containers\. To customize your container configuration, edit the Docker support options defined in the `yarn-site.xml` and `container-executor.cfg` files available in the `/etc/hadoop/conf` directory\. For details about each configuration option and how it is used, see [Launching Applications Using Docker Containers](https://hadoop.apache.org/docs/r3.1.0/hadoop-yarn/hadoop-yarn-site/DockerContainers.html)\. 

You can choose to use Docker when you submit a job\. Use the following variables to specify the Docker runtime and Docker image\.
+ `YARN_CONTAINER_RUNTIME_TYPE=docker`
+ `YARN_CONTAINER_RUNTIME_DOCKER_IMAGE={DOCKER_IMAGE_NAME}`

When you use Docker containers to run your YARN applications, YARN downloads the Docker image that you specify when you submit your job\. For YARN to resolve this Docker image, it must be configured with a Docker registry\. The configuration options for a Docker registry depend on whether you deploy the cluster using a public or private subnet\.

## Docker registries<a name="emr-docker-registries"></a>

A Docker registry is a storage and distribution system for Docker images\. For Amazon EMR 6\.x, the following Docker registries can be configured:
+ Docker Hub – A public Docker registry containing over 100,000 popular Docker images\.
+ Amazon ECR – A fully managed Docker container registry that allows you to create your own custom images and host them in a highly available and scalable architecture\.

**Deployment considerations**

Docker registries require network access from each host in the cluster\. This is because each host downloads images from the Docker registry when your YARN application is running on the cluster\. These network connectivity requirements may limit your choice of Docker registry, depending on whether you deploy your Amazon EMR cluster into a public or private subnet\. 

**Public subnet**

When EMR clusters are deployed in a public subnet, the nodes running YARN NodeManager can directly access any registry available over the internet, including Docker Hub, as shown in the following diagram\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/dockerhub_public_subnet.png)

**Private subnet**

When EMR clusters are deployed in a private subnet, the nodes running YARN NodeManager don’t have direct access to the internet\. Docker images can be hosted in Amazon ECR and accessed through AWS PrivateLink, as shown in the following diagram\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/emr/latest/ManagementGuide/images/dockerhub_private_subnet.png)

For more information about how to use AWS PrivateLink to allow access to Amazon ECR in a private subnet scenario, see [Setting up AWS PrivateLink for Amazon ECS, and Amazon ECR](https://aws.amazon.com/blogs/compute/setting-up-aws-privatelink-for-amazon-ecs-and-amazon-ecr/)\.

## Configuring Docker registries<a name="emr-docker-hub"></a>

To use Docker registries with Amazon EMR, you must configure Docker to trust the specific registry that you want to use to resolve Docker images\. The default trust registries are local \(private\) and centos \(on public Docker Hub\)\. To use other public repositories or Amazon ECR, you can override `docker.trusted.registries` settings in `/etc/hadoop/conf/container-executor.cfg` using the EMR Classification API with the `container-executor` classification key\.

The following example shows how to configure the cluster to trust both a public repository, named `your-public-repo`, and an ECR registry endpoint, `123456789123.dkr.ecr.us-east-1.amazonaws.com`\. If you use ECR, replace this endpoint with your specific ECR endpoint\. If you use Docker Hub, replace this repository name with your repository name\.

```
[
  {
    "Classification": "container-executor",
    "Configurations": [
        {
            "Classification": "docker",
            "Properties": {
                "docker.trusted.registries": "local,centos,your-public-repo,123456789123.dkr.ecr.us-east-1.amazonaws.com",
                "docker.privileged-containers.registries": "local,centos,your-public-repo,123456789123.dkr.ecr.us-east-1.amazonaws.com"
            }
        }
    ]
  }
]
```

To launch an Amazon EMR 6\.0\.0 cluster with this configuration using the AWS Command Line Interface \(AWS CLI\), create a file named `container-executor.json` with the contents of the preceding container\-executor JSON configuration\. Then, use the following commands to launch the cluster\.

```
export KEYPAIR=<Name of your Amazon EC2 key-pair>
export SUBNET_ID=<ID of the subnet to which to deploy the cluster>
export INSTANCE_TYPE=<Name of the instance type to use>
export REGION=<Region to which to deploy the cluster>

aws emr create-cluster \
    --name "EMR-6.0.0" \
    --region $REGION \
    --release-label emr-6.0.0 \
    --applications Name=Hadoop Name=Spark \
    --service-role EMR_DefaultRole \
    --ec2-attributes KeyName=$KEYPAIR,InstanceProfile=EMR_EC2_DefaultRole,SubnetId=$SUBNET_ID \
    --instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=$INSTANCE_TYPE InstanceGroupType=CORE,InstanceCount=2,InstanceType=$INSTANCE_TYPE \
    --configuration file://container-executor.json
```

## Configuring YARN to access Amazon ECR on EMR 6\.0\.0 and earlier<a name="emr-docker-ECR"></a>

If you’re new to Amazon ECR, follow the instructions in [Getting Started with Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/ECR_GetStarted.html) and verify that you have access to Amazon ECR from each instance in your Amazon EMR cluster\.

On EMR 6\.0\.0 and earlier, to access Amazon ECR using the Docker command, you must first generate credentials\. To verify that YARN can access images from Amazon ECR, use the container environment variable `YARN_CONTAINER_RUNTIME_DOCKER_CLIENT_CONFIG` to pass a reference to the credentials that you generated\.

Run the following command on one of the core nodes to get the login line for your ECR account\.

```
aws ecr get-login --region us-east-1 --no-include-email
```

The `get-login` command generates the correct Docker CLI command to run to create credentials\. Copy and run the output from `get-login`\.

```
sudo docker login -u AWS -p <password> https://<account-id>.dkr.ecr.us-east-1.amazonaws.com
```

This command generates a `config.json` file in the `/root/.docker` folder\. Copy this file to HDFS so that jobs submitted to the cluster can use it to authenticate to Amazon ECR\.

Run the commands below to copy the `config.json` file to your home directory\.

```
mkdir -p ~/.docker
sudo cp /root/.docker/config.json ~/.docker/config.json
sudo chmod 644 ~/.docker/config.json
```

Run the commands below to put the config\.json in HDFS so it may be used by jobs running on the cluster\.

```
hadoop fs -put ~/.docker/config.json /user/hadoop/
```

YARN can access ECR as a Docker image registry and pull containers during job execution\.

After configuring Docker registries and YARN, you can run YARN applications using Docker containers\. For more information, see [Run Spark applications with Docker using Amazon EMR 6\.0\.0](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-spark-docker.html)\.

In EMR 6\.1\.0 and later, you don't have to manually set up authentication to Amazon ECR\. If an Amazon ECR registry is detected in the `container-executor` classification key, the Amazon ECR auto authentication feature activates, and YARN handles the authentication process when you submit a Spark job with an ECR image\. You can confirm whether automatic authentication is enabled by checking `yarn.nodemanager.runtime.linux.docker.ecr-auto-authentication.enabled` in yarn\-site\. Automatic authentication is enabled and the YARN authentication setting is set to `true` if the `docker.trusted.registries` contains an ECR registry URL\.

**Prerequisites for using automatic authentication to Amazon ECR**
+ EMR version 6\.1\.0 or later
+ ECR registry included in configuration is in the same Region with the cluster
+ IAM role with permissions to get authorization token and pull any image

Refer to [Setting up with Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/get-set-up-for-amazon-ecr.html) for more information\.

**How to enable automatic authentication**

Follow [Configuring Docker registries ](#emr-docker-hub) to set an Amazon ECR registry as a trusted registry, and make sure the Amazon ECR repository and the cluster are in same Region\.

To enable this feature even when the ECR registry is not set in the trusted registry, use the configuration classification to set `yarn.nodemanager.runtime.linux.docker.ecr-auto-authentication.enabled` to `true`\.

**How to disable automatic authentication**

 By default, automatic authentication is disabled if no Amazon ECR registry is detected in the trusted registry\.

To disable automatic authentication, even when the Amazon ECR registry is set in the trusted registry, use the configuration classification to set `yarn.nodemanager.runtime.linux.docker.ecr-auto-authentication.enabled` to `false`\.

**How to check if automatic authentication is enabled on a  cluster**

On the master node, use a text editor such as `vi` to view the contents of the file: `vi /etc/hadoop/conf.empty/yarn-site.xml`\. Check the value of `yarn.nodemanager.runtime.linux.docker.ecr-auto-authentication.enabled`\.