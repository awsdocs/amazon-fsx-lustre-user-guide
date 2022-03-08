# Deployment options for FSx for Lustre file systems<a name="using-fsx-lustre"></a>

FSx for Lustre provides a high performance, parallel file system that stores data across multiple network file servers to maximize performance and reduce bottlenecks\. These servers have multiple disks\. To spread load, Amazon FSx shards file system data into smaller chunks and spreads them across disks and servers using a process called striping\. For more information about FSx for Lustre data striping, see [Striping data in your file system](performance.md#striping-data)\.

It's a best practice to link a highly durable long\-term data repository residing on Amazon S3 with your FSx for Lustre high\-performance file system\.

In this scenario, you store your datasets on the linked Amazon S3 data repository\. When you create your FSx for Lustre file system, you link it to your S3 data repository\. At this point, the objects in your S3 bucket are listed as files and directories on your FSx file system\. Amazon FSx then automatically copies the file contents from S3 to your Lustre file system when a file is accessed for the first time on the Amazon FSx file system\. After your compute workload runs, or at any time, you can use a data repository task to export changes back to S3\. For more information, see [Using data repositories with Amazon FSx for Lustre](fsx-data-repositories.md) and [Using data repository tasks to export data and metadata changes](export-data-repo-task-dra.md)\. 

## File system deployment options for FSx for Lustre<a name="lustre-deployment-types"></a>

Amazon FSx for Lustre provides two file system deployment options: **scratch** and **persistent**\. 

**Note**  
Both deployment options support solid state drive \(SSD\) storage\. However, hard disk drive \(HDD\) storage is supported only in one of the persistent deployment types\.

You choose the file system deployment type when you create a new file system, using the AWS Management Console, the AWS Command Line Interface \(AWS CLI\), or the Amazon FSx for Lustre API\. For more information, see [Step 1: Create your Amazon FSx for Lustre file system](getting-started-step1.md) and [CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html) in the *Amazon FSx API Reference*\.

Encryption of data at rest is automatically enabled when you create an Amazon FSx for Lustre file system, regardless of the deployment type you use\. Scratch 2 and persistent file systems automatically encrypt data in transit when they are accessed from Amazon EC2 instances that support encryption in transit\. For more information on encryption, see [Data encryption in Amazon FSx for Lustre](encryption-fsxl.md)\.

## Scratch file systems<a name="scratch-file-system"></a>

*Scratch file systems* are designed for temporary storage and shorter\-term processing of data\. Data isn't replicated and doesn't persist if a file server fails\. Scratch file systems provide high burst throughput of up to six times the baseline throughput of 200 MBps per TiB of storage capacity\. For more information, see [Aggregate file system performance](performance.md#fsx-aggregate-perf)\. 

Scratch deployment types are built on Lustre 2\.10\. Use scratch file systems when you need cost\-optimized storage for short\-term, processing\-heavy workloads\.

The following diagram shows the architecture for an Amazon FSx for Lustre scratch file system\.

![\[FSx for Lustre scratch file system architecture\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/fsx-lustre-scratch-architecture.png)

On a scratch file system, file servers aren't replaced if they fail and data isn't replicated\. If a file server or a storage disk becomes unavailable on a scratch file system, files stored on other servers are still accessible\. If clients try to access data that is on the unavailable server or disk, clients experience an immediate I/O error\. 

The following table illustrates the availability or durability that scratch file systems of example sizes are designed for, over the course of a day and a week\. Because larger file systems have more file servers and more disks, the probabilities of failure are increased\. 


| File system size \(TiB\) | Number of file servers | Availability/durability over one day | Availability/durability over one week | 
| --- | --- | --- | --- | 
|  1\.2  | 2 | 99\.9% | 99\.4% | 
| 2\.4 | 2 | 99\.9% | 99\.4% | 
| 4\.8 | 3 | 99\.8% | 99\.2% | 
|  9\.6  | 5 | 99\.8% | 98\.6% | 
| 50\.4 | 22 | 99\.1% | 93\.9% | 

## Persistent file systems<a name="persistent-file-system"></a>

*Persistent file systems* are designed for longer\-term storage and workloads\. The file servers are highly available, and data is automatically replicated within the same Availability Zone in which the file system is located\. The data volumes attached to the file servers are replicated independently from the file servers to which they are attached\.

Amazon FSx continuously monitors persistent file systems for hardware failures, and automatically replaces infrastructure components in the event of a failure\. On a persistent file system, if a file server becomes unavailable, it's replaced automatically within minutes of failure\. During that time, client requests for data on that server transparently retry and eventually succeed after the file server is replaced\. Data on persistent file systems is replicated on disks, and any failed disks are automatically replaced transparently\. 

Use persistent file systems for longer\-term storage and for throughput\-focused workloads that run for extended periods or indefinitely, and that might be sensitive to disruptions in availability\.

The following diagram shows the architecture for an Amazon FSx for Lustre persistent file system, with replicated, highly available file servers and data volumes within a single Availability Zone\.

Persistent deployment types automatically encrypt data in transit when they are accessed from Amazon EC2 instances that support encryption in transit\. 

![\[FSx for Lustre persistent file system architecture.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/fsx-lustre-persistent-architecture.png)

Amazon FSx for Lustre supports two persistent deployment types, Persistent\_1 and Persistent\_2\.

### Persistent 1 deployment type<a name="persistent-1-lustre"></a>

Persistent\_1 deployment types can be built on Lustre 2\.10 or 2\.12, and support SSD \(solid state drive\) and HDD \(hard disk drive\) storage types\. The Persistent\_1 deployment type is well\-suited for use cases that require longer\-term storage, and have throughput\-focused workloads that aren't latency\-sensitive\.

For a Persistent\_1 file system with SSD storage, the throughput per unit of storage is either 50, 100, or 200 MB/s per tebibyte \(TiB\)\. For HDD storage, Persistent\_1 throughput per unit of storage is 12 or 40 MB/s per TiB\.

You can create Persistent\_1 deployment types only using the AWS CLI and the Amazon FSx API\. Persistent\_1 deployment types are available in all AWS Regions\.

### Persistent 2 deployment type<a name="persistent-2-lustre"></a>

Persistent\_2 is the latest generation of Persistent deployment type, and is best\-suited for use cases that require longer\-term storage, and have latency\-sensitive workloads that require the highest levels of IOPS and throughput\. Persistent\_2 deployment types are built on Lustre v2\.12 and support SSD storage\. They support higher levels of throughput per unit storage as compared to Persistent\_1 file systems, with options of 125, 250, 500, and 1000 MB/s/TiB\.

You can create Persistent\_2 deployment types using the Amazon FSx console, AWS Command Line Interface, and API\. Persistent\_2 deployment types are available in the following AWS Regions\.
+ US East \(N\. Virginia\)
+ US East \(Ohio\)
+ US West \(Oregon\)
+ Canada \(Central\)
+ Europe \(Ireland\)
+ Europe \(Frankfurt\)
+ Asia Pacific \(Tokyo\)

For more information on FSx for Lustre performance, see [Aggregate file system performance](performance.md#fsx-aggregate-perf)\.