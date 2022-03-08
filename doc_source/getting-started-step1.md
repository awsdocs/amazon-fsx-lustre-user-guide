# Step 1: Create your Amazon FSx for Lustre file system<a name="getting-started-step1"></a>

Next, you create your file system in the console\.

**To create your file system**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **Create file system** to start the file system creation wizard\.

1. Choose **FSx for Lustre** and then choose **Next** to display the **Create File System** page\.

1. Provide the information in the **File system details** section:
   + For **File system name\-optional**, provide a name for your file system\. You can use up to 256 Unicode letters, white space, and numbers plus the special characters **\+ \- = \. \_ : /**\.
   + For **Deployment and storage type**, choose one of the options:

     **SSD** storage provides low\-latency, IOPS\-intensive workloads that typically have small, random file operations\. **HDD** storage provides throughput\-intensive workloads that typically have large, sequential file operations\.

     For more information about storage types, see [Multiple storage options](what-is.md#storage-options)\.

     For more information about deployment types, see [Deployment options for FSx for Lustre file systems](using-fsx-lustre.md)\.

     For more information about the AWS Regions where encrypting data in transit is available, see [Encrypting data in transit](encryption-in-transit-fsxl.md)\.
     + Choose the **Persistent, SSD** deployment type for longer\-term storage and for latency\-sensitive workloads requiring the highest levels of IOPS/throughput\. The file servers are highly available, data is automatically replicated within the file system's Availability Zone, and this type supports encrypting data in transit\. *Persistent, SSD* uses Persistent 2, the latest\-generation of persistent file systems\.
     + Choose the **Persistent, HDD** deployment type for longer\-term storage and for throughput\-focused workloads that aren't latency\-sensitive\. The file servers are highly available, data is automatically replicated within the file system's Availability Zone, and this type supports encrypting data in transit\. *Persistent, HDD* uses the Persistent 1 deployment type\.

       Choose **with SSD cache** to create an SSD cache that is sized to 20 percent of your HDD storage capacity to provide sub\-millisecond latencies and higher IOPS for frequently accessed files\.
     + Choose the **Scratch, SSD** deployment type for temporary storage and shorter\-term processing of data\. *Scratch, SSD* uses Scratch 2 file systems, and offers in\-transit encryption of data\.
   + Choose the amount of **Throughput per unit of storage** that you want for your file system\. This option is only valid for **Persistent** deployment types\.

     *Throughput per unit of storage* is the amount of read and write throughput for each 1 tebibyte \(TiB\) of storage provisioned, in MB/s/TiB\. You pay for the amount of throughput that you provision: 
     + For Persistent SSD storage, choose a value of either 125, 250, 500, or 1,000 MB/s/TiB\.
     + For Persistent HDD storage, choose a value of 12 or 40 MB/s/TiB\.

     For more information about throughput per unit storage and file system performance, see [Aggregate file system performance](performance.md#fsx-aggregate-perf)\.
   + For **Storage capacity**, set the amount of storage capacity for your file system, in TiB:
     + For a *Persistent, SSD* deployment type, set this to a value of 1\.2 TiB, 2\.4 TiB, or increments of 2\.4 TiB\.
     + For a *Persistent, HDD* deployment type, this value can be increments of 6\.0 TiB for 12 MB/s/TiB file systems and increments of 1\.8 TiB for 40 MB/s/TiB file systems\.

     You can increase the amount of storage capacity as needed after you create the file system\. For more information, see [Managing storage and throughput capacity](managing-storage-capacity.md)\.
   + For **Data compression type**, choose **NONE** to turn off data compression or choose **LZ4** to turn on data compression with the LZ4 algorithm\. For more information, see [Lustre data compression](data-compression.md)\.

   All FSx for Lustre file systems are built on Lustre version 2\.12 when created using the Amazon FSx console\.  
![\[File system details section of the FSx for Lustre Create sile system console page.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/FSxLScreenShot.png)

1. In the **Network & security** section, provide the following networking and security group information:
   + Choose the VPC that you want to associate with your file system\. For this getting started exercise, choose the same VPC that you chose for your Amazon EC2 instance\.
   + For **VPC security groups**, the ID for the default security group for your VPC should be already added\. If you're not using the default security group, make sure that the following inbound rule is added to the security group you're using for this getting started exercise\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/getting-started-step1.html)

     The following screen capture shows an example of editing inbound rules\.  
![\[Inbound traffic rule on the default security group assigned to the FSx for Lustre file system.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/Inbound-Traffic-Lustrec-rule.PNG)
   + For **Subnet**, choose any value from the list of available subnets\.

1. For the **Encryption** section, the options available vary depending upon which file system type you're creating: 
   + For a persistent file system, you can choose an AWS Key Management Service \(AWS KMS\) encryption key to encrypt the data on your file system at rest\. 
   + For a scratch file system, data at rest is encrypted using the default Amazon FSx–managed key for your account\.
   + For scratch 2 and persistent file systems, data in transit is encrypted automatically when the file system is accessed from a supported Amazon EC2 instance type\. For more information, see [Encrypting data in transit](encryption-in-transit-fsxl.md)\.

1. For the **Data Repository Import/Export \- optional** section, linking your file system to Amazon S3 data repositories is disabled by default\. For information about enabling this option and creating a data repository association to an existing S3 bucket, see [To link an S3 bucket while creating a file system \(console\)](create-dra-linked-data-repo.md#export-path-lustre-console-dra-new)\.
**Important**  
Selecting this option also disables backups and you won't be able to enable backups while creating the file system\.
If you link one or more Amazon FSx for Lustre file systems to an Amazon S3 bucket, don't delete the Amazon S3 bucket until all linked file systems have been deleted\.

1. For **Logging \- optional**, logging is enabled by default\. When enabled, failures and warnings for data repository activity on your file system are logged to Amazon CloudWatch Logs\. For information about configuring logging, see [Managing logging](cw-event-logging.md#manage-logging)\.

1. In **Backup and maintenance \- *optional***, you can do the following\.

   For daily automatic backups:
   + Disable the **Daily automatic backup**\. This option is enabled by default, unless you enabled **Data Repository Import/Export**,\.
   + Set the start time for **Daily automatic backup window**\.
   + Set the **Automatic backup retention period**, from 1 \- 35 days\.

   For more information, see [Working with backups](using-backups-fsx.md)\.

1. Set the **Weekly maintenance window** start time, or keep it set to the default **No preference**\.

1. Create any tags that you want to apply to your file system\.

1. Choose **Next** to display the **Create file system summary** page\.

1. Review the settings for your Amazon FSx for Lustre file system, and choose **Create file system**\.

Now that you've created your file system, note its fully qualified domain name and mount name for a later step\. You can find the fully qualified domain name and mount name for a file system by choosing the name of the file system in the **File Systems** dashboard, and then choosing **Attach**\.