# Overview of data repositories<a name="overview-dra-data-repo"></a>

When you use Amazon FSx with multiple durable storage repositories, you can ingest and process large volumes of file data in a high\-performance file system by using automatic import and import data repository tasks\. At the same time, you can write results to your data repositories by using automatic export or export data repository tasks\. With these features, you can restart your workload at any time using the latest data stored in your data repository\.

**Note**  
Automatic export and multiple data repositories are supported only on `Persistent 2` file systems\. If you're using a file system with an older FSx for Lustre deployment type, see [Working with older deployment types](older-deployment-types.md)\.

Amazon FSx is deeply integrated with Amazon S3\. This integration means that you can seamlessly access the objects stored in your Amazon S3 buckets from applications that mount your Amazon FSx file system\. You can also run your compute\-intensive workloads on Amazon EC2 instances in the AWS Cloud and export the results to your data repository after your workload is complete\.

In Amazon FSx for Lustre, you can import file and directory listings from your linked data repositories to the file system using automatic import or using an import data repository task\. When you turn on automatic import on a data repository association, your file system imports file metadata as files are created, modified, and/or deleted in the S3 data repository\. Alternatively, you can import metadata for new or changed files and directories using an import data repository task\. Both automatic import and import data repository tasks include POSIX metadata\.

**Note**  
Automatic import and import data repository tasks can be used simultaneously on a file system\.

In order to access objects on the Amazon S3 data repository as files and directories on the file system, file and directory metadata must be loaded into the file system\. You can load metadata from a linked data repository when you create a data repository association or load metadata for batches of files and directories that you want to access using the FSx for Lustre file system at a later time using an import data repository task\.

You can also export files and their associated metadata in your file system to your durable data repository using automatic export or using an export data repository task\. When you turn on automatic export on a data repository association, your file system exports file data and metadata as they are created, modified, or deleted\. Alternatively, you can export a file or directory using an export data repository task\. When you use an export data repository task, file data and metadata that were created or modified since the last such task are exported\. Both automatic export and export data repository tasks include POSIX metadata\.

**Note**  
Automatic export and export data repository tasks can't be used simultaneously on a file system\.

Amazon FSx also supports cloud bursting workloads with on\-premises file systems by enabling you to copy data from on\-premises clients using AWS Direct Connect or VPN\.

**Important**  
If you have linked one or more Amazon FSx file systems to a durable data repository on Amazon S3, don't delete the Amazon S3 bucket until you have deleted all linked file systems\.

## POSIX metadata support for data repositories<a name="posix-metadata-support"></a>

Amazon FSx for Lustre automatically transfers Portable Operating System Interface \(POSIX\) metadata for files, directories, and symbolic links \(symlinks\) when importing and exporting data to and from a linked durable data repository on Amazon S3\. When you export changes in your file system to its linked data repository, Amazon FSx also exports POSIX metadata changes along with data changes\. Because of this metadata export, you can implement and maintain access controls between your FSx for Lustre file system and its data repository on S3\.

 Amazon FSx imports only S3 objects that have POSIX\-compliant object keys, such as the following\.

```
test/mydir/ 
test/
```

Amazon FSx stores directories and symlinks as separate objects in the linked data repository on S3\. For directories, Amazon FSx creates an S3 object with a key name that ends with a slash \("/"\), as follows:
+ The S3 object key `test/mydir/` maps to the Amazon FSx directory `test/mydir`\.
+ The S3 object key `test/` maps to the Amazon FSx directory `test`\.

For symlinks, FSx for Lustre uses the following Amazon S3 schema for symlinks:
+ **S3 object key** – The path to the link, relative to the Amazon FSx mount directory
+ **S3 object data** – The target path of this symlink
+ **S3 object metadata** – The metadata for the symlink

Amazon FSx stores POSIX metadata, including ownership, permissions, and timestamps for Amazon FSx files, directories, and symbolic links, in S3 objects as follows:
+ `Content-Type` – The HTTP entity header used to indicate the media type of the resource for web browsers\.
+ `x-amz-meta-file-permissions` – The file type and permissions in the format `<octal file type><octal permission mask>`, consistent with `st_mode` in Linux stat\(2\)\.
**Note**  
FSx for Lustre does not import or retain `setuid` information\.
+ `x-amz-meta-file-owner` – The owner user ID \(UID\) expressed as an integer\.
+ `x-amz-meta-file-group` – The group ID \(GID\) expressed as an integer\. 
+ `x-amz-meta-file-atime` – The last\-accessed time in nanoseconds\. Terminate the time value with `ns`; otherwise Amazon FSx interprets the value as milliseconds\.
+ `x-amz-meta-file-mtime` – The last\-modified time in nanoseconds\. Terminate the time value with `ns`; otherwise, Amazon FSx interprets the value as milliseconds\.
+ `x-amz-meta-user-agent` – The user agent, ignored during Amazon FSx import\. During export, Amazon FSx sets this value to `aws-fsx-lustre`\.

The default POSIX permission that FSx for Lustre assigns to a file is 755\. This permission allows read and execute access for all users and write access for the owner of the file\.

**Note**  
Amazon FSx doesn't retain any user\-defined custom metadata on S3 objects\.