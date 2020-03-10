# POSIX Metadata Support for Data Repositories<a name="posix-metadata-support"></a>

Amazon FSx for Lustre automatically transfers Portable Operating System Interface \(POSIX\) metadata for files, directories, and symbolic links \(symlinks\) when importing and exporting data to and from a linked durable data repository on Amazon S3\. Amazon FSx also exports POSIX metadata changes, along with data changes, to the linked data repository on S3\. Because of this metadata export, you can implement and maintain access controls between your Amazon FSx for Lustre file system and its data repository on S3\.

**Note**  
 Amazon FSx imports only S3 objects that have POSIX\-compliant object keys, such as the following\.  

```
test/mydir/ 
test/
```
Amazon FSx doesn't import S3 object keys that are not POSIX\-compliant, such as the following\.  

```
.
.. 
test/. 
test/..
```

Amazon FSx stores directories and symlinks as separate objects in the linked data repository on S3\. For directories, Amazon FSx creates an S3 object with a key name that ends with a slash \("/"\), as follows:
+ S3 object key = `test/mydir/` maps to the Amazon FSx directory `test/mydir`\.
+ S3 object key = `test/` maps to the Amazon FSx directory `test`\.

For symlinks, Amazon FSx for Lustre uses the same Amazon S3 schema as AWS DataSync, shown following:
+ S3 object key – The path to the link, relative to the Amazon FSx mount directory
+ S3 object data – The target path of this symlink
+ S3 object metadata – The metadata for the symlink

Amazon FSx stores POSIX metadata, including ownership, permissions, and timestamps for Amazon FSx files, directories and symbolic links, in S3 objects as follows:
+ `Content-Type` – HTTP entity header used to indicate the media type of resource for web browsers\.
+ `x-amz-meta-file-permissions` – File type and permissions in format `<octal file type><octal permission mask>`, consistent with `st_mode` in Linux stat\(2\)\.
**Note**  
Amazon FSx for Lustre does not import or retain `setuid` and `setgid` information\.
+ `x-amz-meta-file-owner` – The owner UID expressed as an integer\.
+ `x-amz-meta-file-group` – The group UID expressed as an integer\.
+ `x-amz-meta-file-atime` – The last accessed time in nanoseconds\. Terminate the time value with "ns"; otherwise Amazon FSx interprets the value as milliseconds\.
+ `x-amz-meta-file-mtime` – The last modified time in nanoseconds\. Terminate the time value with "ns"; otherwise, Amazon FSx interprets the value as milliseconds\.
+ `x-amz-meta-user-agent` – The user agent, ignored during Amazon FSx import\. During export, Amazon FSx sets this value to `aws-fsx-lustre`\.

**Note**  
Amazon FSx doesn't retain any user\-defined custom metadata on S3 objects\.