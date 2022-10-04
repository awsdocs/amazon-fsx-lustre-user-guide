# Amazon FSx for Lustre performance<a name="performance"></a>

Amazon FSx for Lustre, built on Lustre, the popular high\-performance file system, provides scale\-out performance that increases linearly with a ﬁle system’s size\. Lustre file systems scale horizontally across multiple file servers and disks\. This scaling gives each client direct access to the data stored on each disk to remove many of the bottlenecks present in traditional file systems\. Amazon FSx for Lustre builds on Lustre's scalable architecture to support high levels of performance across large numbers of clients\.

**Topics**
+ [How FSx for Lustre file systems work](#how-lustre-fs-work)
+ [Aggregate file system performance](#fsx-aggregate-perf)
+ [File system storage layout](#storage-layout)
+ [Striping data in your file system](#striping-data)
+ [Monitoring performance and usage](#performance-monitoring)
+ [Performance tips](#performance-tips)

## How FSx for Lustre file systems work<a name="how-lustre-fs-work"></a>

Each FSx for Lustre file system consists of the file servers that the clients communicate with, and a set of disks attached to each file server that store your data\. Each file server employs a fast, in\-memory cache to enhance performance for the most frequently accessed data\. HDD\-based file systems can also be provisioned with an SSD\-based read cache to further enhance performance for the most frequently accessed data\. When a client accesses data that's stored in the in\-memory or SSD cache, the file server doesn't need to read it from disk, which reduces latency and increases the total amount of throughput you can drive\. The following diagram illustrates the paths of a write operation, a read operation served from disk, and a read operation served from in\-memory or SSD cache\.

![\[FSx for Lustre performance architecture.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/LustrePerfDiagram.png)

 When you read data that is stored on the file server's in\-memory or SSD cache, file system performance is determined by the network throughput\. When you write data to your file system, or when you read data that isn't stored on the in\-memory cache, file system performance is determined by the lower of the network throughput and disk throughput\. 

When you provision an HDD Lustre file system with an SSD cache, Amazon FSx creates an SSD cache that is automatically sized to 20 percent of the file system's HDD storage capacity\. Doing this provides sub\-millisecond latencies and higher IOPS for frequently accessed files\.

## Aggregate file system performance<a name="fsx-aggregate-perf"></a>

The throughput that an FSx for Lustre file system supports is proportional to its storage capacity\. Amazon FSx for Lustre file systems scale to hundreds of GBps of throughput and millions of IOPS\. Amazon FSx for Lustre also supports concurrent access to the same file or directory from thousands of compute instances\. This access enables rapid data checkpointing from application memory to storage, which is a common technique in high performance computing \(HPC\)\. You can increase the amount of storage and throughput capacity as needed at any time after you create the file system\. For more information, see [Managing storage and throughput capacity](managing-storage-capacity.md)\.

FSx for Lustre file systems provide burst read throughput using a network I/O credit mechanism to allocate network bandwidth based on average bandwidth utilization\. The file systems accrue credits when their network bandwidth usage is below their baseline limits, and can use these credits when they perform network data transfers\.

The following tables show performance that the FSx for Lustre deployment options are designed for\.


**File system performance for SSD storage options**  

| Deployment Type | **Network throughput \(MB/s/TiB of storage provisioned\)** |  **Network IOPS \(IOPS/TiB of storage provisioned\)** |  **Cache storage \(GiB of RAM/TiB of storage provisioned\)** |  **Disk latencies per file operation \(milliseconds, P50\)** | **Disk throughput \(MBps/TiB of storage or SSD cache provisioned\)** | 
| --- |--- |--- |--- |--- |--- |
| **** | **Baseline** | **Burst** | **** | **** | **** | **Baseline** | **Burst** | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| PERSISTENT\-125 | 320 | 1300 | Tens of thousands baselineHundreds of thousands burst | 3\.4 |  Metadata: sub\-ms Data: sub\-ms  |  125  | 500 | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| PERSISTENT\-250 | 640 | 1300 | 6\.8 |  250  | 500 | 
| --- |--- |--- |--- |--- |--- |
| PERSISTENT\-500 | 1300 | ‐ | 13\.7 | 500 | ‐ | 
| --- |--- |--- |--- |--- |--- |
| PERSISTENT\-1000 | 2600 | ‐ | 27\.3 | 1000 | ‐ | 
| --- |--- |--- |--- |--- |--- |


**File system performance for HDD storage options**  

| Deployment Type | **Network throughput \(MB/s/TiB of storage or SSD cache provisioned\)** |  **Network IOPS \(IOPS/TiB of storage provisioned\)** | **Cache storage \(GiB of RAM/TiB of storage provisioned\)** | **Disk latencies per file operation \(milliseconds, P50\) ** | **Disk throughput \(MBps/TiB of storage or SSD cache provisioned\)** | 
| --- |--- |--- |--- |--- |--- |
| **** | **Baseline** | **Burst** |  | **Baseline** | **Burst** | 
| --- |--- |--- |--- |--- |--- |
| PERSISTENT\-12 | 
| --- |
| HDD storage | 40 | 375\*  |  Tens of thousands baseline Hundreds of thousands burst  | 0\.4 memory |  Metadata: sub\-ms Data: single\-digit ms  | 12 |  80 \(read\) 50 \(write\)  | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| SSD read cache |  200  | 1,900 |  200 SSD cache  |  Data: sub\-ms  | 200 |  \-  | 
| --- |--- |--- |--- |--- |--- |--- |
|  PERSISTENT\-40 | 
| --- |
| HDD storage | 150 | 1,300\*  |  Tens of thousands baseline Hundreds of thousands burst  | 1\.5 |  Metadata: sub\-ms Data: single\-digit ms  | 40 |  250 \(read\) 150 \(write\)  | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| SSD read cache |  750  |  6500  | 200 SSD cache |  Data: sub\-ms  | 200 |  \-  | 
| --- |--- |--- |--- |--- |--- |--- |


**File system performance for previous generation SSD storage options**  

| Deployment Type | **Network throughput \(MB/s per TiB of storage provisioned\)** |  **Network IOPS \(IOPS per TiB of storage provisioned\)** |  **Cache storage \(GiB per TiB of storage provisioned\)** |  **Disk latencies per file operation \(milliseconds, P50\)** | **Disk throughput \(MB/s per TiB of storage or SSD cache provisioned\)** | 
| --- |--- |--- |--- |--- |--- |
| **** | **Baseline** | **Burst** | **** | **** | **** | **Baseline** | **Burst** | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| Scratch\_2 | 200 | 1300 | Tens of thousands baselineHundreds of thousands burst | 6\.7 RAM |  Metadata: sub\-ms Data: sub\-ms  |  200 \(read\) 100 \(write\)  | – | 
| --- |--- |--- |--- |--- |--- |--- |--- |
| PERSISTENT\-50 | 250 | 1,300\* | 2\.2 RAM | 50 | 240 | 
| --- |--- |--- |--- |--- |--- |
| PERSISTENT\-100 | 500 | 1,300\* | 4\.4 RAM | 100 | 240 | 
| --- |--- |--- |--- |--- |--- |
| PERSISTENT\-200 | 750 | 1,300\* | 8\.8 RAM | 200 | 240 | 
| --- |--- |--- |--- |--- |--- |

**Note**  
\*Persistent file systems in the following AWS Regions provide network burst up to 530 MB/s per TiB of storage: Africa \(Cape Town\), Asia Pacific \(Hong Kong\), Asia Pacific \(Osaka\), Asia Pacific \(Singapore\), Canada \(Central\), Europe \(Frankfurt\), Europe \(London\), Europe \(Milan\), Europe \(Stockholm\), Middle East \(Bahrain\), South America \(São Paulo\), China, and US West \(Los Angeles\)\.

**Note**  
The FSx for Lustre SCRATCH\_1 deployment option was designed to support 200 MB/s/TiB\.

### Example: Aggregate baseline and burst throughput<a name="example-persistant-throughput"></a>

The following example illustrates how storage capacity and disk throughput impact file system performance\.

A persistent file system with a storage capacity of 4\.8 TiB and 50 MB/s per TiB of throughput per unit of storage provides an aggregate baseline disk throughput of 240 MB/s and a burst disk throughput of 1\.152 GB/s\.

Regardless of file system size, Amazon FSx for Lustre provides consistent, sub\-millisecond latencies for file operations\.

## File system storage layout<a name="storage-layout"></a>

All file data in Lustre is stored on storage volumes called *object storage targets* \(OSTs\)\. All file metadata \(including file names, timestamps, permissions, and more\) is stored on storage volumes called *metadata targets* \(MDTs\)\. Amazon FSx for Lustre file systems are composed of a single MDT and multiple OSTs\. Each OST is approximately 1 to 2 TiB in size, depending on the file system's deployment type\. Amazon FSx for Lustre spreads your file data across the OSTs that make up your file system to balance storage capacity with throughput and IOPS load\.

To view the storage usage of the MDT and OSTs that make up your file system, run the following command from a client that has the file system mounted\.

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

## Striping data in your file system<a name="striping-data"></a>

You can optimize your file system's throughput performance with file striping\. Amazon FSx for Lustre automatically spreads out files across OSTs in order to ensure that data is served from all storage servers\. You can apply the same concept at the file level by configuring how files are striped across multiple OSTs\.

Striping means that files can be divided into multiple chunks that are then stored across different OSTs\. When a file is striped across multiple OSTs, read or write requests to the file are spread across those OSTs, increasing the aggregate throughput or IOPS your applications can drive through it\.



Following are the default layouts for Amazon FSx for Lustre file systems\.
+ For Amazon FSx for Lustre file systems created before December 18, 2020, the default layout specifies a stripe count of one\. This means that unless a different layout is specified, each file created in Amazon FSx for Lustre using standard Linux tools is stored on a single disk\.
+ For Amazon FSx for Lustre file systems created after December 18, 2020, the default layout is a progressive file layout in which files under 1GiB in size are stored in one stripe, and larger files are assigned a stripe count of five\.
+ For all Amazon FSx for Lustre file systems regardless of their creation date, files imported from Amazon S3 don't use the default layout, but instead use the layout in the file system's `ImportedFileChunkSize` parameter\. S3\-imported files larger than the `ImportedFileChunkSize` will be stored on multiple OSTs with a stripe count of `(FileSize / ImportedFileChunksize) + 1`\. The default value of `ImportedFileChunkSize` is 1GiB\.

You can view the layout configuration of a file or directory using the `lfs getstripe` command\.

```
lfs getstripe path/to/filename
```

This command reports a file's stripe count, stripe size, and stripe offset\. The *stripe count * is how many OSTs the file is striped across\. The *stripe size *is how much continuous data is stored on an OST\. The *stripe offset *is the index of the first OST that the file is striped across\.

### Modifying your striping configuration<a name="striping-modify"></a>

A file's layout parameters are set when the file is first created\. Use the `lfs setstripe` command to create a new, empty file with a specified layout\.

```
lfs setstripe filename --stripe-count number_of_OSTs
```

The `lfs setstripe` command affects only the layout of a new file\. Use it to specify the layout of a file before you create it\. You can also define a layout for a directory\. Once set on a directory, that layout is applied to every new file added to that directory, but not to existing files\. Any new subdirectory you create also inherits the new layout, which is then applied to any new file or directory you create within that subdirectory\.

To modify the layout of an existing file, use the `lfs migrate` command\. This command copies the file as needed to distribute its content according to the layout you specify in the command\. For example, files that are appended to or are increased in size don't change the stripe count, so you have to migrate them to change the file layout\. Alternatively, you can create a new file using the `lfs setstripe` command to specify its layout, copy the original content to the new file, and then rename the new file to replace the original file\.

There may be cases where the default layout configuration is not optimal for your workload\. For example, a file system with tens of OSTs and a large number of multi\-gigabyte files may see higher performance by striping the files across more than the default stripe count value of five OSTs\. Creating large files with low stripe counts can cause I/O performance bottlenecks and can also cause OSTs to fill up\. In this case, you can create a directory with a larger stripe count for these files\.

Setting up a striped layout for large files \(especially files larger than a gigabyte in size\) is important for the following reasons:
+ Improves throughput by allowing multiple OSTs and their associated servers to contribute IOPS, network bandwidth, and CPU resources when reading and writing large files\.
+ Reduces the likelihood that a small subset of OSTs become hot spots that limit overall workload performance\.
+ Prevents a single large file from filling an OST, possibly causing disk full errors\.

There is no single optimal layout configuration for all use cases\. For detailed guidance on file layouts, see [Managing File Layout \(Striping\) and Free Space](https://doc.lustre.org/lustre_manual.xhtml#managingstripingfreespace) in the Lustre\.org documentation\. The following are general guidelines:
+ Striped layout matters most for large files, especially for use cases where files are routinely hundreds of megabytes or more in size\. For this reason, the default layout for a new file system assigns a striped count of five for files over 1GiB in size\.
+ Stripe count is the layout parameter that you should adjust for systems supporting large files\. The stripe count specifies the number of OST volumes that will hold chunks of a striped file\. For example, with a stripe count of 2 and a stripe size of 1MiB, Lustre writes alternate 1MiB chunks of a file to each of two OSTs\.
+ The effective stripe count is the lesser of the actual number of OST volumes and the stripe count value you specify\. You can use the special stripe count value of `-1` to indicate that stripes should be placed on all OST volumes\.
+ Setting a large stripe count for small files is sub\-optimal because for certain operations Lustre requires a network round trip to every OST in the layout, even if the file is too small to consume space on all the OST volumes\.
+ You can set up a progressive file layout \(PFL\) that allows the layout of a file to change with size\. A PFL configuration can simplify managing a file system that has a combination of large and small files without you having to explicitly set a configuration for each file\. For more information, see [Progressive file layouts](#striping-pfl)\.
+ Stripe size by default is 1MiB\. Setting a stripe offset may be userful in special circumstances, but in general it is best to leave it unspecified and use the default\.

### Progressive file layouts<a name="striping-pfl"></a>

You can specify a progressive file layout \(PFL\) configuration for a directory to specify different stripe configurations for small and large files before populating it\. For example, you can set a PFL on the top\-level directory before any data is written to a new file system\.

To specify a PFL configuration, use the `lfs setstripe` command with `-E` options to specify layout components for different sized files, such as the following command:

```
lfs setstripe -E 100M -c 1 -E 10G -c 8 -E -1 -c -1 /mountname/directory
```

This command sets three layout components:
+ The first component \(`-E 100M -c 1`\) indicates a stripe count value of 1 for files up to 100MiB in size\.
+ The second component \(`-E 10G -c 8`\) indicates a stripe count of 8 for files up to 10GiB in size\.
+ The third component \(`-E -1 -c -1`\) indicates that files larger than 10GiB will be striped across all OSTs\.

**Important**  
Appending data to a file created with a PFL layout will populate all of its layout components\. For example, with the 3\-component command shown above, if you create a 1MiB file and then add data to the end of it, the layout of the file will expand to have a stripe count of \-1, meaning all the OSTs in the system\. This does not mean data will be written to every OST, but an operation such as reading the file length will send a request in parallel to every OST, adding significant network load to the file system\.  
Therefore, be careful to limit the stripe count for any small or medium length file that can subsequently have data appended to it\. Because log files usually grow by having new records appended, Amazon FSx for Lustre assigns a default stripe count of 1 to any file created in append mode, regardless of the default stripe configuration specified by its parent directory\.

The default PFL configuration on Amazon FSx for Lustre file systems created after December 18, 2020 is set with this command:

```
lfs setstripe -E 1G -c 1 -E -1 -c 5 /mountname
```

Customers with workloads that have highly concurrent access on medium and large files are likely to benefit from a layout with more stripes at smaller sizes and striping across all OSTs for the largest files, as shown in the three\-component example layout shown previously\.

## Monitoring performance and usage<a name="performance-monitoring"></a>

Every minute, Amazon FSx for Lustre emits usage metrics for each disk \(MDT and OST\) to Amazon CloudWatch\.

To view aggregate file system usage details, you can look at the Sum statistic of each metric\. For example, the Sum of the `DataReadBytes` statistic reports the total read throughput seen by all the OSTs in a file system\. Similarly, the Sum of the `FreeDataStorageCapacity` statistic reports the total available storage capacity for file data in the file system\.

For more information on monitoring your file system’s performance, see [Monitoring Amazon FSx for Lustre](monitoring_overview.md)\.

## Performance tips<a name="performance-tips"></a>

When using Amazon FSx for Lustre, keep the following performance tips in mind\. For service limits, see [Quotas](limits.md)\.
+ **Average I/O size** – Because Amazon FSx for Lustre is a network file system, each file operation goes through a round trip between the client and Amazon FSx for Lustre, incurring a small latency overhead\. Due to this per\-operation latency, overall throughput generally increases as the average I/O size increases, because the overhead is amortized over a larger amount of data\.
+ **Request model** – By enabling asynchronous writes to your file system, pending write operations are buffered on the Amazon EC2 instance before they are written to Amazon FSx for Lustre asynchronously\. Asynchronous writes typically have lower latencies\. When performing asynchronous writes, the kernel uses additional memory for caching\. A file system that has enabled synchronous writes issues synchronous requests to Amazon FSx for Lustre\. Every operation goes through a round trip between the client and Amazon FSx for Lustre\.
**Note**  
Your chosen request model has tradeoffs in consistency \(if you're using multiple Amazon EC2 instances\) and speed\.
+ **Amazon EC2 instances** – Applications that perform a large number of read and write operations likely need more memory or computing capacity than applications that don't\. When launching your Amazon EC2 instances for your compute\-intensive workload, choose instance types that have the amount of these resources that your application needs\. The performance characteristics of Amazon FSx for Lustre file systems don't depend on the use of Amazon EBS–optimized instances\.
+ **Recommended tuning for large client instance types**

  1. To tune large client instances for optimal performance: 

     1. For client instance types with memory of more than 64 GiB, we recommend applying the following tuning:

        ```
        lctl set_param ldlm.namespaces.*.lru_max_age=600000
        ```

     1. For client instance types with more than 64 CPU cores, we recommend applying the following tuning:

        ```
        echo "options ptlrpc ptlrpcd_per_cpt_max=32" >> /etc/modprobe.d/modprobe.conf
        echo "options ksocklnd credits=2560" >> /etc/modprobe.d/modprobe.conf
                    
        # reload all kernel modules to apply the above two settings
        sudo reboot
        ```

  1. After the client is mounted, the following tuning needs to be applied:

     ```
     sudo lctl set_param osc.*OST*.max_rpcs_in_flight=32
     sudo lctl set_param mdc.*.max_rpcs_in_flight=64
     sudo lctl set_param mdc.*.max_mod_rpcs_in_flight=50
     ```

  Note that `lctl set_param` is known to not persist over reboot\. Since these parameters cannot be set permanently from the client side, it is recommended to implement a boot cron job to set the configuration with the recommended tunings\.
+ **Workload balance across OSTs** – In some cases, your workload isn’t driving the aggregate throughput that your file system can provide \(200 MB/s per TiB of storage\)\. If so, you can use CloudWatch metrics to troubleshoot if performance is affected by an imbalance in your workload’s I/O patterns\. To identify if this is the cause, look at the Maximum CloudWatch metric for Amazon FSx for Lustre\.

  In some cases, this statistic shows a load at or above 240 MBps of throughput \(the throughput capacity of a single 1\.2\-TiB Amazon FSx for Lustre disk\)\. In such cases, your workload is not evenly spread out across your disks\. If this is the case, you can use the `lfs setstripe` command to modify the striping of files your workload is most frequently accessing\. For optimal performance, stripe files with high throughput requirements across all the OSTs comprising your file system\.

  If your files are imported from a data repository, you can take another approach to stripe your high\-throughput files evenly across your OSTs\. To do this, you can modify the `ImportedFileChunkSize` parameter when creating your next Amazon FSx for Lustre file system\.

  For example, suppose that your workload uses a 7\.0\-TiB file system \(which is made up of 6x 1\.17\-TiB OSTs\) and needs to drive high throughput across 2\.4\-GiB files\. In this case, you can set the `ImportedFileChunkSize` value to `(2.4 GiB / 6 OSTs) = 400 MiB` so that your files are spread evenly across your file system’s OSTs\.