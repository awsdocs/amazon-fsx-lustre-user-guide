# Step 1: Create Your Amazon FSx for Lustre File System<a name="getting-started-step1"></a>

Next, you create your file system in the console\.

**To create your file system**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **Create file system** to start the file system creation wizard\.

1. Choose **FSx for Lustre** and then choose **Next** to display the **Create File System** page\.

1. Provide the information in the **File system details** section:
   + For **File System name\-optional**, provide a name for your file system\. You can use up to 256 Unicode letters, white space, and numbers plus the special characters **\+ \- = \. \_ : /**\.
   + For **Deployment and storage type**, choose one of the options\.

     **SSD** storage provides low\-latency, IOPS\-intensive workloads that typically have small, random file operations\. **HDD** storage provides throughput\-intensive workloads that typically have large, sequential file operations\.
     + Choose the **Persistent, SSD** deployment type for longer\-term storage and workloads\. The file servers are highly available, data is automatically replicated within the file system's Availability Zone, and this type supports encrypting data in transit\. To learn in which AWS Regions encrypting data in transit is available, see [Encrypting Data in Transit](encryption-in-transit-fsxl.md)\. 
     + Choose the **Persistent, HDD** deployment type for longer\-term storage and workloads\. The file servers are highly available, data is automatically replicated within the file system's Availability Zone, and this type supports encrypting data in transit\. To learn in which AWS Regions encrypting data in transit is available, see [Encrypting Data in Transit](encryption-in-transit-fsxl.md)\. 

       If you choose **with SSD cache**, this cache is automatically sized to 20 percent of your HDD storage capacity to provide sub\-millisecond latencies and higher IOPS for frequently accessed files\.
     + Choose the **Scratch** deployment type for temporary storage and shorter\-term processing of data\. **Scratch 2** is the latest generation of scratch file systems, and offers higher burst throughput over baseline throughput and also in\-transit encryption of data\. 
   + Choose the **Throughput per unit of storage** that you want for your file system\. This option is only valid for **Persistent** deployment types\.

     For SSD storage, set a value for **Throughput per unit of storage** to either 50, 100, or 200 MB/s per tebibyte \(TiB\)\. For HDD, set a value for **Throughput per unit of storage ** to 12 or 40 MB/s per tebibyte \(TiB\)\.* Throughput per unit of storage* is the amount of read and write throughput for each 1 tebibyte \(TiB\) of storage provisioned, in MB/s/TiB\. For a 2\.4 TiB ﬁle system, provisioning 50 MB/s/TiB of per unit storage throughput yields 120 MB/s of ﬁle system throughput\. You pay for the amount of throughput that you provision\.
   + For **Storage capacity**, provide a storage capacity for your file system, in TiB:
     +  For a persistent SSD or scratch 2 file system, this value can be 1\.2 TiB or increments of 2\.4 TiB\. For a persistent HDD file system, this value can be increments of 6\.0 TiB for 12 MB/s/TiB file systems and increments of 1\.8 TiB for 40 MB/s/TiB file systems\.
     +  For a scratch 1 file system, this value can be 1\.2, 2\.4, or increments of 3\.6 TiB\.

     You can increase the amount of storage capacity as needed after you create the file system\. For more information, see [Managing storage and throughput capacity](managing-storage-capacity.md)\.  
![\[File system details section of the Amazon FSx for Lustre Create File System console page.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/FSxLScreenShot-2.png)

1. In the **Network & security** section, provide networking and security group information:
   + Choose the VPC that you want to associate with your file system\. For this getting started exercise, choose the same VPC that you chose for your Amazon EC2 instance\.
   + For **VPC security groups**, the ID for the default security group for your VPC should be already added\. If you're not using the default security group, make sure that the following inbound rule is added to the security group you're using for this getting started exercise\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/getting-started-step1.html)

     The following screen capture shows an example of editing inbound rules\.  
![\[Inbound traffic rule on the default security group assigned to the Amazon FSx for Lustre file system.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/Inbound-Traffic-Lustrec-rule.PNG)
   + For **Subnet**, choose any value from the list of available subnets\.

1. For the **Encryption** section, the options available vary depending upon which file system type you're creating: 
   + For a persistent file system, you can choose an AWS Key Management Service \(AWS KMS\) encryption key to encrypt the data on your file system at rest\. 
   + For a scratch file system, data at rest is encrypted using the default Amazon FSx–managed key\.
   + For scratch 2 and persistent file systems, data in transit is encrypted automatically when the file system is accessed from a supported Amazon EC2 instance type\. For more information, see [Encrypting Data in Transit](encryption-in-transit-fsxl.md)\.

1. \(Optional\) Use the **Data repository import/export** panel to configure a data repository linked to an Amazon S3 bucket\.

    Select **Import data from and export data to S3** to expand the panel and configure the data repository settings\.  
![\[The Data repository import and export configuration panel in the Create Lustre file system page.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/Data-repository-int.png)

1. \(Optional\) Choose how Amazon FSx keeps your file and directory listings up to date automatically as you add or modify objects in your S3 bucket\. By default, Amazon FSx imports file and directory listings at file system creation, and then whenever new files are added after this initial import\. For more information, see [Automatically import updates from your S3 bucket](autoimport-data-repo.md)\. 

1. Enter an optional **Import prefix** if you want to import only some of the file and directory listings in your S3 bucket into your file system\. The import prefix defines where in your S3 bucket to import from\. For more information, see [Linking your file system to an S3 bucket](create-fs-linked-data-repo.md)\.

1. Keep **Export prefix** at the default setting\. For more information about the data repository integration, see [Linking your file system to an S3 bucket](create-fs-linked-data-repo.md)\.
**Important**  
If you link one or more Amazon FSx for Lustre file systems to an Amazon S3 bucket, don't delete the Amazon S3 bucket until all linked file systems have been deleted\.

1. In **Backup and maintenance \- *optional*** you can do the following\.

   For daily automatic backups:
   +  Disable the **Daily automatic backup**, which is enabled by default\. 
   + Set the start time for **Daily automatic backup window**\.
   + Set the **Automatic backup retention period**, from 1 \- 35 days\.

   For more information, see [Working with backups](using-backups-fsx.md)\.

1. Set the **Weekly maintenance window** start time, or leave it set to the default **No preference**\.

1. Create any tags that you want to apply to your file system\.

1. Choose **Next** to display the **Create file system summary** page\.

1. Review the settings for your Amazon FSx for Lustre file system, and choose **Create file system**\.

Now that you've created your file system, note its fully qualified domain name and mount name for a later step\. You can find the fully qualified domain name and mount name for a file system by choosing the name of the file system in the **File Systems** dashboard, and then choosing **Attach**\.