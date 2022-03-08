# What is Amazon FSx for Lustre?<a name="what-is"></a>

FSx for Lustre makes it easy and cost\-effective to launch and run the popular, high\-performance Lustre file system\. You use Lustre for workloads where speed matters, such as machine learning, high performance computing \(HPC\), video processing, and financial modeling\.

The open\-source Lustre file system is designed for applications that require fast storage—where you want your storage to keep up with your compute\. Lustre was built to solve the problem of quickly and cheaply processing the world's ever\-growing datasets\. It's a widely used file system designed for the fastest computers in the world\. It provides sub\-millisecond latencies, up to hundreds of GBps of throughput, and up to millions of IOPS\. For more information on Lustre, see the [Lustre website](http://lustre.org/)\. 

As a fully managed service, Amazon FSx makes it easier for you to use Lustre for workloads where storage speed matters\. FSx for Lustre eliminates the traditional complexity of setting up and managing Lustre file systems, enabling you to spin up and run a battle\-tested high\-performance file system in minutes\. It also provides multiple deployment options so you can optimize cost for your needs\.

 FSx for Lustre is POSIX\-compliant, so you can use your current Linux\-based applications without having to make any changes\. FSx for Lustre provides a native file system interface and works as any file system does with your Linux operating system\. It also provides read\-after\-write consistency and supports file locking\. 

**Topics**
+ [Multiple deployment options](#deployment-options)
+ [Multiple storage options](#storage-options)
+ [FSx for Lustre and data repositories](#data-repo-features)
+ [Accessing FSx for Lustre file systems](#compute-access)
+ [Integrations with AWS services](#integration-aws-services)
+ [Security and compliance](#security-compliance)
+ [Assumptions](#assumptions)
+ [Pricing for Amazon FSx for Lustre](#pricing)
+ [Amazon FSx for Lustre forums](#fsx-forums)
+ [Are you a first\-time user of Amazon FSx for Lustre?](#first-time-user)

## Multiple deployment options<a name="deployment-options"></a>

Amazon FSx for Lustre offers a choice of *scratch* and *persistent* file systems to accommodate different data processing needs\. Scratch file systems are ideal for temporary storage and shorter\-term processing of data\. Data is not replicated and does not persist if a file server fails\. Persistent file systems are ideal for longer\-term storage and throughput\-focused workloads\. In persistent file systems, data is replicated, and file servers are replaced if they fail\. For more information, see [Deployment options for FSx for Lustre file systems](using-fsx-lustre.md)\.

## Multiple storage options<a name="storage-options"></a>

Amazon FSx for Lustre offers a choice of solid state drive \(SSD\) and hard disk drive \(HDD\) storage types that are optimized for different data processing requirements:
+ SSD storage options – For low\-latency, IOPS\-intensive workloads that typically have small, random file operations, choose one of the SSD storage options\.
+ HDD storage options – For throughput\-intensive workloads that typically have large, sequential file operations, choose one of the HDD storage options\.

If you are provisioning a file system with the HDD storage option, you can optionally provision a read\-only SSD cache that is sized to 20 percent of your HDD storage capacity\. This provides sub\-millisecond latencies and higher IOPS for frequently accessed files\. Both SSD\-based and HDD\-based file systems are provisioned with SSD\-based metadata servers\. As a result, all metadata operations, which represent the majority of file system operations, are delivered with sub\-millisecond latencies\. 

For more information about performance of these storage options, see [Amazon FSx for Lustre performance](performance.md)\.

## FSx for Lustre and data repositories<a name="data-repo-features"></a>

You can link FSx for Lustre file systems to data repositories on Amazon S3 or to on\-premises data stores\.

### FSx for Lustre S3 data repository integration<a name="s3-integration"></a>

FSx for Lustre integrates with Amazon S3, making it easier for you to process cloud datasets using the Lustre high\-performance file system\. When linked to an Amazon S3 bucket, an FSx for Lustre file system transparently presents S3 objects as files\. Amazon FSx imports listings of all existing files in your S3 bucket at file system creation\. Amazon FSx can also import listings of files added to the data repository after the file system is created\. You can set the import preferences to match your workflow needs\. The file system also makes it possible for you to write file system data back to S3\. Data repository tasks simplify the transfer of data and metadata between your FSx for Lustre file system and its durable data repository on Amazon S3\. For more information, see [Using data repositories with Amazon FSx for Lustre](fsx-data-repositories.md) and [Data repository tasks](data-repository-tasks.md)\. 

### FSx for Lustre and on\-premises data repositories<a name="on-prem-repo"></a>

 With Amazon FSx for Lustre, you can burst your data processing workloads from on\-premises into the AWS Cloud by importing data using AWS Direct Connect or AWS VPN\. For more information, see [Using Amazon FSx with your on\-premises data repository](fsx-on-premises.md)\.

## Accessing FSx for Lustre file systems<a name="compute-access"></a>

You can mix and match the compute instance types and Linux Amazon Machine Images \(AMIs\) that are connected to a single FSx for Lustre file system\.

 Amazon FSx for Lustre file systems are accessible from compute workloads running on Amazon Elastic Compute Cloud \(Amazon EC2\) instances, on Amazon Elastic Container Service \(Amazon ECS\) Docker containers, and containers running on Amazon Elastic Kubernetes Service \(Amazon EKS\)\.
+ **Amazon EC2** – You access your file system from your Amazon EC2 compute instances using the open\-source Lustre client\. Amazon EC2 instances can access your file system from other Availability Zones within the same Amazon Virtual Private Cloud \(Amazon VPC\), provided your networking configuration provides for access across subnets within the VPC\. After your Amazon FSx for Lustre file system is mounted, you can work with its files and directories just as you do using a local file system\.
+ **Amazon EKS** – You access Amazon FSx for Lustre from containers running on Amazon EKS using the open\-source [FSx for Lustre CSI driver](https://docs.aws.amazon.com/eks/latest/userguide/fsx-csi.html), as described in **Amazon EKS User Guide**\. Your containers running on Amazon EKS can use high\-performance persistent volumes \(PVs\) backed by Amazon FSx for Lustre\.
+ **Amazon ECS** – You access Amazon FSx for Lustre from Amazon ECS Docker containers on Amazon EC2 instances\. For more information, see [Mounting from Amazon Elastic Container Service](mounting-ecs.md)\.

Amazon FSx for Lustre is compatible with the most popular Linux\-based AMIs, including Amazon Linux 2 and Amazon Linux, Red Hat Enterprise Linux \(RHEL\), CentOS, Ubuntu, and SUSE Linux\. The Lustre client is included with Amazon Linux 2 and Amazon Linux\. For RHEL, CentOS, and Ubuntu, an AWS Lustre client repository provides clients that are compatible with these operating systems\. 

Using FSx for Lustre, you can burst your compute\-intensive workloads from on\-premises into the AWS Cloud by importing data over AWS Direct Connect or AWS Virtual Private Network\. You can access your Amazon FSx file system from on\-premises, copy data into your file system as\-needed, and run compute\-intensive workloads on in\-cloud instances\.

For more information on the clients, compute instances, and environments from which you can access FSx for Lustre file systems, see [Accessing file systems](accessing-fs.md)\.

## Integrations with AWS services<a name="integration-aws-services"></a>

 Amazon FSx for Lustre integrates with SageMaker as an input data source\. When using SageMaker with FSx for Lustre, your machine learning training jobs are accelerated by eliminating the initial download step from Amazon S3\. Additionally, your total cost of ownership \(TCO\) is reduced by avoiding the repetitive download of common objects for iterative jobs on the same dataset as you save on S3 requests costs\. For more information, see [What Is SageMaker?](https://docs.aws.amazon.com/sagemaker/latest/dg/whatis.html) in the *Amazon SageMaker Developer Guide*\. 

FSx for Lustre integrates with AWS Batch using EC2 Launch Templates\. AWS Batch enables you to run batch computing workloads on the AWS Cloud, including high performance computing \(HPC\), machine learning \(ML\), and other asynchronous workloads\. AWS Batch automatically and dynamically sizes instances based on job resource requirements\. For more information, see [What Is AWS Batch?](https://docs.aws.amazon.com/batch/latest/userguide/what-is-batch.html) in the *AWS Batch User Guide*\. 

 FSx for Lustre integrates with AWS ParallelCluster\. AWS ParallelCluster is an AWS\-supported open\-source cluster management tool used to deploy and manage HPC clusters\. It can automatically create FSx for Lustre file systems or use existing file systems during the cluster creation process\. 

## Security and compliance<a name="security-compliance"></a>

FSx for Lustre file systems support encryption at rest and in transit\. Amazon FSx automatically encrypts file system data at rest using keys managed in AWS Key Management Service \(AWS KMS\)\. Data in transit is also automatically encrypted on file systems in certain AWS Regions when accessed from supported Amazon EC2 instances\. For more information about data encryption in FSx for Lustre, including AWS Regions where encryption of data in transit is supported, see [Data encryption in Amazon FSx for Lustre](encryption-fsxl.md)\. Amazon FSx has been assessed to comply with ISO, PCI\-DSS, and SOC certifications, and is HIPAA eligible\. For more information, see [Security in FSx for Lustre](security.md)\.

## Assumptions<a name="assumptions"></a>

In this guide, we make the following assumptions:
+ If you use Amazon Elastic Compute Cloud \(Amazon EC2\), we assume that you're familiar with that service\. For more information on how to use Amazon EC2, see the [Amazon EC2 documentation](https://docs.aws.amazon.com/ec2)\.
+ We assume that you are familiar with using Amazon Virtual Private Cloud \(Amazon VPC\)\. For more information on how to use Amazon VPC, see the [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\.
+ We assume that you haven't changed the rules on the default security group for your VPC based on the Amazon VPC service\. If you have, make sure that you add the necessary rules to allow network traffic from your Amazon EC2 instance to your Amazon FSx for Lustre file system\. For more details, see [File System Access Control with Amazon VPC](limit-access-security-groups.md)\.

## Pricing for Amazon FSx for Lustre<a name="pricing"></a>

With Amazon FSx for Lustre, there are no upfront hardware or software costs\. You pay for only the resources used, with no minimum commitments, setup costs, or additional fees\. For information about the pricing and fees associated with the service, see [Amazon FSx for Lustre Pricing](http://aws.amazon.com/fsx/lustre/pricing)\.

## Amazon FSx for Lustre forums<a name="fsx-forums"></a>

If you encounter issues while using Amazon FSx for Lustre, check the [forums](https://forums.aws.amazon.com/forum.jspa?forumID=311)\.

## Are you a first\-time user of Amazon FSx for Lustre?<a name="first-time-user"></a>

If you are a first\-time user of Amazon FSx for Lustre, we recommend that you read the following sections in order:

1. If you're ready to create your first Amazon FSx for Lustre file system, try [Getting started with Amazon FSx for Lustre](getting-started.md)\.

1. For information on performance, see [Amazon FSx for Lustre performance](performance.md)\.

1. For information on linking your file system to an Amazon S3 bucket data repository, see [Using data repositories with Amazon FSx for Lustre](fsx-data-repositories.md)\.

1. For Amazon FSx for Lustre security details, see [Security in FSx for Lustre](security.md)\.

1. For information on the scalability limits of Amazon FSx for Lustre, including throughput and file system size, see [Quotas](limits.md)\.

1. For information on the Amazon FSx for Lustre API, see the [Amazon FSx for Lustre API Reference](https://docs.aws.amazon.com/fsx/latest/APIReference/Welcome.html)\.