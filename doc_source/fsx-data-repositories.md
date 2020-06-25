# Using Data Repositories with Amazon FSx for Lustre<a name="fsx-data-repositories"></a>

Amazon FSx for Lustre provides high\-performance file systems optimized for fast workload processing\. It can support workloads such as machine learning, high performance computing \(HPC\), video processing, financial modeling, and electronic design automation \(EDA\)\. These workloads commonly require data to be presented using a scalable, high\-speed file system interface for data access\. They typically have datasets stored on long\-term durable data stores like Amazon S3, or on\-premises storage\.

**Topics**
+ [Working with Server\-Side Encrypted Amazon S3 Buckets](s3-server-side-encryption-support.md)
+ [POSIX Metadata Support for Data Repositories](posix-metadata-support.md)
+ [Transferring Data and Metadata with Data Repository Tasks](data-repository-tasks.md)
+ [Exporting Data to Your Amazon S3 Bucket](export-data-repository.md)
+ [Using HSM Commands with Data Repositories](using-hsm-commands-fsxl.md)
+ [Using Amazon FSx with Your On\-Premises Data Repository](fsx-on-premises.md)

When you use Amazon FSx with a durable storage repository, you can ingest and process large volumes of file data in a high\-performance file system\. At the same time, you can periodically write intermediate results to your data repository\. By using this approach, you can restart your workload at any time using the latest data stored in your data repository\. When your workload is done, you can write final results from your file system to your data repository and delete your file system\.

You can link your Amazon FSx file system to an Amazon S3 durable data repository when you create the file system\. For more information, see [Step 1: Create Your Amazon FSx for Lustre File System](getting-started-step1.md)\.

**Note**  
Data from an Amazon S3 linked repository is imported into your Amazon FSx for Lustre file system only once, at creation\. Any data added to your S3 repository after this initial import can't be imported into your Lustre file system\. You will have to create a new Amazon FSx for Lustre file system and link it to the S3 repository to download the newly added data\.   
If your primary data set is stored on S3, file system backups are not supported\.  
Creating a file system from a backup is only permitted by the account that created the original backup record\.

Amazon FSx is deeply integrated with Amazon S3\. This integration means that you can seamlessly access the objects stored in your Amazon S3 buckets from applications that mount your Amazon FSx file system\. You can also run your compute\-intensive workloads on Amazon EC2 instances in the AWS Cloud and export the results to your data repository after your workload is complete\.

Amazon FSx also supports cloud bursting workloads with on\-premises file systems by enabling you to copy data from on\-premises clients using AWS Direct Connect or VPN\.

**Important**  
If you have linked one or more Amazon FSx file systems to a durable data repository on Amazon S3, don't delete the Amazon S3 bucket until you have deleted all linked file systems\.  
Backups are not supported on file systems that are linked to an Amazon S3 bucket\.