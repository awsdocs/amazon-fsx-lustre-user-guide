# Amazon FSx for Lustre Performance<a name="performance"></a>

Amazon FSx for Lustre, built on Lustre, the popular high\-performance file system, provides scale\-out performance that increases linearly with a ﬁle system’s size\. Lustre file systems scale horizontally across multiple file servers and disks\. This scaling gives each client direct access to the data stored on each disk to remove many of the bottlenecks present in traditional file systems\. Amazon FSx for Lustre builds on Lustre’s scalable architecture to support high levels of performance across large numbers of clients\.

## Aggregate File System Performance<a name="fsx-aggregate-perf"></a>

Amazon FSx for Lustre file systems scale to hundreds of GBps of throughput and millions of IOPS\. Amazon FSx for Lustre also supports concurrent access to the same file or directory from thousands of compute instances\. This access enables rapid data checkpointing from application memory to storage, which is a common technique in high\-performance computing \(HPC\)\.

The throughput that a scratch Amazon FSx for Lustre file system can support is based on its size\. With scratch file systems, each tebibyte \(TiB\) of storage capacity supports a baseline of 200 MBps of throughput for file system read operations\. This throughput exists in aggregate across all client connections to the file system\. The latest generation of scratch file systems can burst up to six times this baseline bandwidth to meet the performance needs of spiky workloads\. They can burst up to 1,200 MBps of throughput per TiB of storage capacity, as shown in the following table\.


****  

| File System Size \(TiB\) | Baseline Throughput \(MBps\) | Burst Throughput \(MBps\) | 
| --- | --- | --- | 
| 1\.2 | 234 | Up to 1,404 | 
| 2\.4 | 468 | Up to 2,808 | 
| 4\.8 | 937 | Up to 5,622 | 
| 9\.6 | 1,875 | Up to 11,250 | 
| 19\.2 | 3,750 | Up to 22,500 | 
| 38\.4 | 7,500 | Up to 45,000 | 

For a persistent Amazon FSx for Lustre file system, you can specify the amount of throughput capacity for each TiB of storage when you create one\. You can choose either 50 MB/sec/TiB, 100 MB/sec/TiB, or 200 MB/sec/TiB of throughput capacity\.

Regardless of file system size, Amazon FSx for Lustre provides consistent, submillisecond latencies for file operations\.

## File System Storage Layout<a name="storage-layout"></a>

All file data in Lustre is stored on disks called object storage targets \(OSTs\)\. All file metadata \(including file names, timestamps, permissions, and more\) is stored on disks called metadata targets \(MDTs\)\. Amazon FSx for Lustre file systems are composed of a single MDT and multiple OSTs, each of which is built on SSD storage\. Each OST is approximately 1\.17 TiB in size\. Amazon FSx for Lustre automatically spreads your file data across the OSTs that make up your file system to balance storage capacity with throughput and IOPS load\. 

To view the listing and storage usage of the MDT and OSTs that make up your file system, run the following command from a client that has the file system mounted\. To view the storage capacity and consumption of each disk, run the following command\.

```
lfs df -h mount/path
```

The output of this command looks like the following\.

**Example**  

```
UUID                             bytes       Used   Available Use% Mounted on
mountname-MDT0000_UUID           68.7G       5.4M       68.7G   0% /fsx[MDT:0]
mountname-OST0000_UUID            1.1T       4.5M        1.1T   0% /fsx[OST:0]
mountname-OST0001_UUID            1.1T       4.5M        1.1T   0% /fsx[OST:1]

filesystem_summary:               2.2T       9.0M        2.2T   0% /fsx
```

## Striping Data in Your File System<a name="striping-data"></a>

Using Lustre, you can configure how files are striped across OSTs\. When a file is striped across multiple OSTs, read or write requests to the file are spread across those OSTs, increasing the aggregate throughput or IOPS your applications can drive through it\. 

By default, each file created in Amazon FSx for Lustre using standard Linux tools is stored on a single disk\. For files imported from Amazon S3, the file system’s `ImportedFileChunkSize` parameter determines how many OSTs imported files will be striped across\. Files larger than the `ImportedFileChunkSize` will be stored on multiple OSTs\.

You can view the striping configuration of a file or directory using the following command:

```
lfs getstripe filename
```

This command reports a file’s stripe count, stripe size, and stripe offset\. The *stripe count *is how many OSTs the file is striped across\. The *stripe size *is how much continuous data is stored on an OST\. The *stripe offset *is the index of the first OST that the file is striped across\. For more information, see [Configuring Lustre File Striping](http://wiki.lustre.org/Configuring_Lustre_File_Striping) on wiki\.lustre\.org\.

A file’s striping parameters are set when the file is first created\. Use the following command to create a new, empty file with a determined striping configuration\.

```
lfs setstripe filename --stripe-count # of OSTs --stripe-size # of bytes
```

To modify the striping of an existing file, you can create a new file with the desired striping configuration using the `lfs setstripe` command\. You then copy the original file into this new file\.

## Monitoring Performance and Usage<a name="performance-monitoring"></a>

Every minute, Amazon FSx for Lustre emits usage metrics for each disk \(MDT and OST\) to Amazon CloudWatch\.

To view aggregate file system usage details, you can look at the Sum statistic of each metric\. For example, the Sum of the `DataReadBytes` statistic reports the total read throughput seen by all the OSTs in a file system\. Similarly, the Sum of the `FreeDataStorageCapacity` statistic reports the total available storage capacity for file data in the file system\.

For more information on monitoring your file system’s performance, see [Monitoring Amazon FSx for Lustre](monitoring_overview.md)\.

## Performance Tips<a name="performance-tips"></a>

When using Amazon FSx for Lustre, keep the following performance tips in mind\. For service limits, see [Quotas](limits.md)\.
+ **Average I/O size** – Because Amazon FSx for Lustre is a network file system, each file operation goes through a round trip between the client and Amazon FSx for Lustre, incurring a small latency overhead\. Due to this per\-operation latency, overall throughput generally increases as the average I/O size increases, because the overhead is amortized over a larger amount of data\.
+ **Request model** – By enabling asynchronous writes to your file system, pending write operations are buffered on the Amazon EC2 instance before they are written to Amazon FSx for Lustre asynchronously\. Asynchronous writes typically have lower latencies\. When performing asynchronous writes, the kernel uses additional memory for caching\. A file system that has enabled synchronous writes issues synchronous requests to Amazon FSx for Lustre\. Every operation goes through a round trip between the client and Amazon FSx for Lustre\.
**Note**  
Your chosen request model has tradeoffs in consistency \(if you're using multiple Amazon EC2 instances\) and speed\.
+ **Amazon EC2 instances** – Applications that perform a large number of read and write operations likely need more memory or computing capacity than applications that don't\. When launching your Amazon EC2 instances for your compute\-intensive workload, choose instance types that have the amount of these resources that your application needs\. The performance characteristics of Amazon FSx for Lustre file systems don't depend on the use of Amazon EBS–optimized instances\.
+ **Workload balance across OSTs** – In some cases, your workload isn’t driving the aggregate throughput that your file system can provide \(200 MB/s per TiB of storage\)\. If so, you can use CloudWatch metrics to troubleshoot if performance is affected by an imbalance in your workload’s I/O patterns\. To identify if this is the cause, look at the Maximum CloudWatch metric for Amazon FSx for Lustre\.

  In some cases, this statistic shows a load at or above 240 MBps of throughput \(the throughput capacity of a single 1\.2\-TiB Amazon FSx for Lustre disk\)\. In such cases, your workload is not evenly spread out across your disks\. If this is the case, you can use the `lfs setstripe` command to modify the striping of files your workload is most frequently accessing\. For optimal performance, stripe files with high throughput requirements across all the OSTs comprising your file system\.

  If your files are imported from a data repository, you can take another approach to stripe your high\-throughput files evenly across your OSTs\. To do this, you can modify the `ImportedFileChunkSize` parameter when creating your next Amazon FSx for Lustre file system\.

  For example, suppose that your workload uses a 7\.0\-TiB file system \(which is made up of 6x 1\.17\-TiB OSTs\) and needs to drive high throughput across 2\.4\-GiB files\. In this case, you can set the `ImportedFileChunkSize` value to `(2.4 GiB / 6 OSTs) = 400 MiB` so that your files are spread evenly across your file system’s OSTs\.