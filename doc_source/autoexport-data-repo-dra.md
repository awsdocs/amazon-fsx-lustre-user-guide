# Automatically export updates to your S3 bucket<a name="autoexport-data-repo-dra"></a>

You can configure your FSx for Lustre file system to automatically update the contents of a linked S3 bucket as files are added, changed, or deleted on the file system\. Changes such as file creations, deletes, renames, and attribute changes can be exported\. A linked S3 repository can be an entire S3 bucket or an S3 prefix\.

A link between a file system and an S3 data repository is called a *data repository association \(DRA\)*\. You can link or unlink an S3 data repository at any time\. You configure the automatic export feature on the data repository association\. For information on creating a data repository association, see [Linking your file system to an S3 bucket](create-dra-linked-data-repo.md)\. 

**Note**  
Support for data repository associations, automatic export, and multiple repositories is only available on `Persistent 2` file systems\.

You can export to a data repository that is in the same AWS Region as the file system or in a different AWS Region\.

You can configure the automatic export settings when you create the data repository association for the S3 repository or at any time after the data repository association is created\. You can update the automatic export settings at any time using the FSx management console, the AWS CLI, and the AWS API\.

**Note**  
You can configure both automatic export and automatic import on the same data repository association\. This topic describes only the automatic export feature\.

The export settings specify how you want Amazon FSx to update your linked S3 bucket as the contents change in file system directories linked to your data repositories\. A data repository association can have one of the following automatic export policies:
+ **New** – Update the S3 data repository only when a new file, directory, or symlink is created on the file system\.
+ **Changed** – Update the S3 data repository only when an existing object in the file system is changed\. For file content changes, the file must be closed before it's propagated to the S3 repository\. Metadata changes \(rename, ownership, permissions, and timestamps\) are propagated when the operation is done\. For renaming changes, the existing \(pre\-renamed\) S3 object is deleted and a S3 new object is created with the new name\.
+ **Deleted** – Update the S3 data repository only when a file, directory, or symlink is deleted in the file system path\. The corresponding object in the S3 bucket is deleted after you configure this export policy\.
+ **Any combination of new, changed, and deleted** – Update the S3 data repository when any of these configured policies are met\. For example, you can specify that the S3 repository is updated when an object is added to \(**New**\) or removed from \(**Deleted**\) the file system, but not when it is changed\.
+ **No policy is configured** – Amazon FSx doesn't update the S3 repository for any new, changed, or deleted objects on the file system\. If you don't configure an export policy, automatic export is disabled\. You can still manually export changes by using an export data repository task, as described in [Using data repository tasks to export data and metadata changes](export-data-repo-task-dra.md)\.

We recommend that you configure an export policy of **New**, **Changed**, and **Deleted**\. This policy ensures that all updates made on your file system are automatically exported to your linked S3 bucket\.

You can [turn on logging](cw-event-logging.md#manage-logging) to CloudWatch Logs to log information about any files or directories that could not be exported automatically\. The log entries contain information about the failure reason\. For more information, see [Data repository event logs](data-repo-event-logs.md)\.

## Updating export settings<a name="manage-autoexport-dra"></a>

You can set a file system's export settings to a linked S3 bucket when you create the data repository association\. For more information, see [Creating a link to an S3 bucket](create-dra-linked-data-repo.md#create-linked-dra)\.

You can also update the export settings at any time, including the export policy\. For more information, see [Updating data repository association settings](create-dra-linked-data-repo.md#update-dra-settings)\.