# Automatically import updates from your S3 bucket<a name="autoimport-data-repo-dra"></a>

When creating a data repository association, you can configure Amazon FSx to automatically import the file metadata \(the name, ownership, timestamp, and permissions\) of objects in a linked S3 bucket to the file system\. You can configure your FSx for Lustre file system to automatically import metadata of objects that are added to, changed in, or deleted from your S3 bucket\. FSx for Lustre updates the file and directory listing of a changed object, if the changed object in the S3 bucket no longer contains its metadata, Amazon FSx maintains the current metadata values of the file, rather than using default permissions\. 

**Note**  
The FSx for Lustre file system and the linked S3 bucket must be located in the same AWS Region to automatically import updates\.

You can set import settings when you create the data repository association, and you can update those import settings at any time using the FSx management console, the AWS CLI, and the AWS API\. When you link your file system to an S3 data repository, your existing S3 objects will appear as file and directory listings\. For information on creating a data repository association, see [Linking your file system to an S3 bucket](create-dra-linked-data-repo.md)\.

**Note**  
In case of file conflicts between the S3 data repository and your file system during an import, S3 always prevails\. S3 can thus modify a file in the file system directory if a file with the same name but different content is present in the S3 directory\.

A data repository association can have one of the following Import policy settings:
+ **New** – Amazon FSx automatically updates file and directory listings of new objects added to the linked S3 bucket that do not currently exist in the FSx file system\.
+ **Changed** – Amazon FSx automatically updates file and directory listings of any existing objects that are changed in the S3 bucket\. Changes include content and metadata changes\.
+ **Deleted** – Amazon FSx automatically deletes listings of objects on the file system that are deleted in the S3 bucket\.
+ **Any combination of New, Changed, and Deleted** – Amazon FSx automatically updates listings of objects on the file system when any of these configured policies occur in the S3 data repository\. For example, you can specify that the file system is updated when an object is added to \(**New**\) or removed from \(**Deleted**\) the S3 repository, but not when an object is changed\.
+ **No policy is configured** – Amazon FSx doesn't update listings of objects on the file system as objects are added, changed, or deleted in the S3 bucket\. If you don't configure an import policy, automatic import is disabled for the data repository association\. You can still manually import metadata changes by using an import data repository task, as described in [Using data repository tasks to import metadata changes](import-data-repo-task-dra.md)\.

We recommend that you configure an import policy of **New**, **Changed**, and **Deleted**\. This policy ensures that all updates made in your linked S3 bucket are automatically imported to your file system\.

When you set the import settings to update your file system file and directory listings based on changes in the linked S3 bucket, Amazon FSx creates an event notification configuration on the linked S3 bucket named `FSx`\. Do not modify or delete the `FSx` event notification configuration on the S3 bucket – doing so will prevent the automatic import of updated file and directory listings to your file system\.

 When Amazon FSx updates a file listing that has changed on the linked S3 bucket, it overwrites the local file with the updated version, even if the file is write\-locked\.

Amazon FSx makes a best effort to update your file system\. Amazon FSx cannot update the file system with updates in the following situations:
+ If Amazon FSx does not have permission to open the changed or new S3 object\. In this case, Amazon FSx skips the object and continues\. The DRA lifecycle state is not affected\.
+ If Amazon FSx does not have bucket\-level permissions, such as for `getBucketAcl`\. This will cause the data repository lifecycle state to become **Misconfigured**\. For more information, see [Data repository association lifecycle state](create-dra-linked-data-repo.md#dra-lifecycles)\.
+ If the `FSx` event notification configuration on the linked S3 bucket is deleted or changed\. This will cause the data repository lifecycle state to become **Misconfigured**\. For more information, see [Data repository association lifecycle state](create-dra-linked-data-repo.md#dra-lifecycles)\.

It is a best practice to periodically sweep your linked S3 bucket and compare changes to the list of files on your file system, especially if your application requires a guarantee around importing changes\.

## Prerequisites<a name="auto-import-prereqs-dra"></a>

The following conditions are required for Amazon FSx to automatically import new, changed, or deleted files from the linked S3 bucket:
+ The file system and its linked S3 bucket are located in the same AWS Region\.
+ The S3 bucket does not have a misconfigured **Lifecycle state**\. For more information, see [Data repository association lifecycle state](create-dra-linked-data-repo.md#dra-lifecycles)\.
+ Your account has the permissions required to configure and receive event notifications on the linked S3 bucket\.

## Types of file changes supported<a name="file-change-support-dra"></a>

Amazon FSx supports importing the following changes to files and folders that occur in the linked S3 bucket:
+ Changes to file contents,
+ Changes to file or folder metadata,
+ Changes to symlink target or metadata\.
+ Deletions of files and folders\. If you delete a folder in the linked S3 bucket, Amazon FSx deletes the corresponding folder on the file system only if it is empty\.

## Updating import settings<a name="manage-autoimport-dra"></a>

You can set a file system's import settings to a linked S3 bucket when you create the data repository association\. For more information, see [Creating a link to an S3 bucket](create-dra-linked-data-repo.md#create-linked-dra)\.

You can also update the import settings at any time, including the import policy\. For more information, see [Updating data repository association settings](create-dra-linked-data-repo.md#update-dra-settings)\.