# Using Data Repositories<a name="fsx-data-repositories"></a>

Amazon FSx for Lustre is designed for short\-term, compute\-intensive workloads where your long\-term data is stored in a durable data repository such as Amazon S3 or an on\-premises data store, and your file system is brought up for the duration of your compute job \(typically several hours or days\)\. Amazon FSx for Lustre is cost\-optimized for these workloads by providing high\-performance, nonreplicated file storage that’s designed to be spun up and down as needed, and isn’t meant to store durable, long\-term data\.

When you use Amazon FSx for Lustre with a durable storage repository, you can ingest and process large volumes of file data in a high\-performance file system\. At the same time, you can periodically write intermediate results to your data repository\. By taking this approach, you can restart your workload at any time from the latest data you've stored in your data repository\. When your workload is done, you can write final results from your file system to your data repository, and delete your file system\.

Amazon FSx for Lustre is deeply integrated with Amazon S3\. This integration means that you can seamlessly access the objects stored in your Amazon S3 buckets from applications mounting your Amazon FSx for Lustre file system\. Amazon FSx for Lustre also supports cloud bursting workloads with on\-premises data repositories by enabling you to copy data from on\-premises clients using AWS Direct Connect or VPN\. When you use Amazon FSx for Lustre with either type of data repository, you can have your data copied into your Amazon FSx for Lustre file system as needed\. You can also run your compute\-intensive workloads on Amazon EC2 instances in the AWS Cloud and copy your results to your data repository when your workload is done\.

**Important**  
If you link one or more Amazon FSx for Lustre file systems to an Amazon S3 bucket, don't delete the Amazon S3 bucket until all linked file systems have been deleted\.

## Importing Data from Your Amazon S3 Bucket<a name="import-data-repository"></a>

During file system creation, you have the option of Amazon FSx for Lustre automatically pre\-loading a listing of file metadata \(file name, size, and modification time\) into your file system, allowing clients to view the listing of files in your data repository as soon as your file system is available\.

**Note**  
Importing data from your Amazon S3 data repository happens during file system creation\. If you have a large number of files to import, this affects how long it takes for your file system to be created\.

Amazon FSx for Lustre automatically copies file data for a given time the first time you open that file from the Amazon S3 data repository into your file system\. This data movement is managed by Amazon FSx for Lustre and occurs transparently to your applications\. Subsequent reads of these files are served directly out of the file system with consistent submillisecond latencies\.

Because Amazon FSx for Lustre copies data from your Amazon S3 data repository when a file is first accessed, the initial read or write to a file incurs a small latency penalty\. If your application is sensitive to this latency and you know which file your application needs to access, you can optionally preload contents of an individual file\. You do so using the `hsm_restore` command, as follows\.

The `hsm_action` command can be used to verify that the file's contents have finished loading into the file system \(a return value of `NOOP` indicates that the file has successfully been loaded\)\. Run these commands from a compute instance with the file system mounted\.

```
sudo lfs hsm_restore path/to/file
sudo lfs hsm_action path/to/file
```

You can preload the entirety of your file system \(or the entirety of a directory within your file system\) using the following commands\. If you request the preloading of multiple files simultaneously, Amazon FSx for Lustre loads your files from your Amazon S3 data repository in parallel\.

```
find local/directory -type f -print0 | xargs -0 -n 50 -P 8 sudo lfs hsm_restore
```

## Exporting Data to Your Amazon S3 Bucket<a name="export-data-repository"></a>

Amazon FSx for Lustre enables you to export files you've written or modified in your file system to your Amazon S3 data repository at any time\. When you request an export of a particular file or directory, your file system will only export files that have been created or modified since the last export \(or file system creation\)\.

Any files or directories exported from your Amazon FSx for Lustre file system will be stored in the following export path inside your Amazon S3 bucket: `s3://import-bucket/FSxLustrefile-system-creation-timestamp`\. You can determine the export path for your file system by calling `aws fsx describe-file-systems`\. You can export an individual file to your data repository and verify that the file has successfully been exported to your data repository by running the commands following\. A return value of `NOOP` indicates that the file has successfully been exported\.

```
sudo lfs hsm_archive path/to/export/file
sudo lfs hsm_action path/to/export/file
```

You can export the entirety of your file system \(or the entirety of a directory within your file system\) using the following commands\. If you request an export of multiple files simultaneously, Amazon FSx for Lustre exports your files to your Amazon S3 data repository in parallel\.

```
find local/directory -type f -print0 | xargs -0 -n 50 -P 8 sudo lfs hsm_archive
```

To determine whether the export has completed, you can run the following command:

```
find path/to/export/file -type f -print0 | xargs -0 -n 50 -P 8 sudo lfs hsm_action | grep "ARCHIVE" | wc -l
```

If the command returns with zero files remaining, then the export has completed\.

## Using Amazon FSx for Lustre With Your On\-Premises Data Repository<a name="fsx-on-premises"></a>

You can use Amazon FSx for Lustre to process data stored in your on\-premises data repository with in\-cloud compute instances\. Amazon FSx for Lustre supports access over AWS Direct Connect and VPN, enabling you to mount your file systems from on\-premises clients\.

**To use Amazon FSx for Lustre with your on\-premises data:**

1. Create a file system\. For more information, see [Step 1: Create Your Amazon FSx for Lustre File System](getting-started.md#getting-started-step1) in the getting started exercise\.

1. Mount the file system from on\-premises clients\. For more information, see [Mounting from On\-Premises or a Peered Amazon VPC](mounting-on-premises.md)\.

1. Copy the data you’d like to process into your Amazon FSx for Lustre file system\.

1. Run your compute\-intensive workload on in\-cloud Amazon EC2 instances mounting your file system\. As desired, you can periodically copy intermediate results to your data repository\.

1. When you're finished, you can copy the final results from your file system back to your on\-premises data repository, and delete your Amazon FSx for Lustre file system\.