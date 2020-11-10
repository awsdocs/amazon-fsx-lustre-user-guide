# Automatically import updates from your S3 bucket<a name="autoimport-data-repo"></a>

By default, when you create a new file system, Amazon FSx automatically imports the file metadata \(the name, ownership, timestamp, and permissions\) of objects in the linked S3 bucket at file system creation\. You can configure your Amazon FSx for Lustre file system to automatically import metadata of objects that are added to or changed in your S3 bucket after file system creation\. Amazon FSx for Lustre updates the file and directory listing of a changed object after creation in the same manner as it imports file metadata at file system creation\. When Amazon FSx updates the file and directory listing of a changed object, if the changed object in the S3 bucket no longer contains its metadata, Amazon FSx maintains the current metadata values of the file, rather than using default permissions\. 

**Note**  
Import settings are only available on Amazon FSx for Lustre file systems created after 3:00 pm EDT, July 23, 2020\. 

 You can set import preferences when you create a new file system, and you can update the preferences setting on existing file systems using the FSx management console, the CLI, and the AWS API\. When you create your file system, your existing S3 objects will appear as file and directory listings\. After you create your file system, how do you want to update it as the contents of your S3 bucket are updated? A file system can have one of the following Import preferences:
+ **Update my file and directory listing as objects are added to my S3 bucket** \- \(Default\) Amazon FSx automatically updates file and directory listings of any new objects added to the linked S3 bucket that do not currently exist in the FSx file system\. Amazon FSx does not import update listings for objects that have changed in the S3 bucket\. Amazon FSx does not delete listings of objects that are deleted in the S3 bucket\.
+ **Update my file and directory listing as objects are added to or changed in my S3 bucket** \- Amazon FSx automatically updates file and directory listings of any new objects added to the S3 bucket and any existing objects that are changed in the S3 bucket after you choose this option\. Amazon FSx does not delete listings of objects that are deleted in the S3 bucket\. 
+ **Do not update my file and directly listing when objects are added to or changed in my S3 bucket** \- Amazon FSx only updates file and directory listing from the linked S3 bucket when the file system is created\. FSx does not update file and directory listings for any new or changed objects after choosing this option\.

**Note**  
The Amazon FSx for Lustre file system and its linked S3 bucket must be located in the same AWS Region to automatically import updates\.

When you set the import preferences to update your file system file and directory listings based on changes in the linked S3 bucket, Amazon FSx creates an event notification configuration on the linked S3 bucket named `FSx`\. Do not modify or delete the `FSx` event notification configuration on the S3 bucket – doing so will prevent the automatic import of new or changed file and directory listings to your file system\.

 When Amazon FSx updates a file listing that has changed on the linked S3 bucket, it overwrites the local file with the updated version, even if the file is write\-locked\.

Amazon FSx makes a best effort to update your file system\. Amazon FSx cannot update the file system with changes in the following situations:
+ If Amazon FSx does not have permission to open the changed or new S3 object\.
+ If the `FSx` event notification configuration on the linked S3 bucket is deleted or changed\.

Either of these conditions to will cause the data repository lifecycle state to become **Misconfigured**\. For more information, see [Data repository lifecycle state](overview-data-repo.md#data-repository-lifecycles)\.

It is a best practice to periodically sweep your linked S3 bucket and compare changes to the list of files on your file system, especially if your application requires a guarantee around importing changes\.

## Prerequisites<a name="auto-import-prereqs"></a>

The following conditions are required for Amazon FSx to automatically import new or changed files from the linked S3 bucket:
+ The file system and its linked S3 bucket are located in the same AWS Region\.
+ The S3 bucket does not have a misconfigured **Lifecycle state**\. For more information, see [Data repository lifecycle state](overview-data-repo.md#data-repository-lifecycles)\.
+ Your account has the permissions required to configure and receive event notifications on the linked S3 bucket\.

## Types of file changes supported<a name="file-change-support"></a>

Amazon FSx supports importing the following changes to files and folders that occur in the linked S3 bucket:
+ Changes to file contents
+ Changes to file or folder metadata
+ Changes to symlink target or metadata

## Updating import preferences<a name="manage-autoimport"></a>

You can set a file system's import preferences when you create a new file system\. For more information, see [Linking your file system to an S3 bucket](create-fs-linked-data-repo.md)\. 

You can also update a file system's import preferences after it is created using the AWS Management Console, the AWS CLI, and the Amazon FSx API, as shown in the following procedures\. 

### To update import preferences on an existing file system \(console\)<a name="manage-autoimport-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **File systems**\.

1. Select the file system that you want to manage to display the file system details\.

1. Choose **Data repository** to view the data repository settings\. You can modify the import preferences if the lifecycle state is **AVAILABLE** or **MISCONFIGURED**\. For more information, see [Data repository lifecycle state](overview-data-repo.md#data-repository-lifecycles)\.

1. Choose **Actions**, and then choose **Update import preferences** to display the **Update import preferences** dialog box\.

1. Choose the new setting, and then choose **Update** to make the change\.

### To update import preferences on an existing file system \(CLI\)<a name="manage-autoimport-cli"></a>
+ To update import preferences, use the [https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html) CLI command\. The corresponding API operation is [ `UpdateFileSystem`](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html)\.

  ```
  $ aws fsx update-file-system \
      --file-system-id fs-0123456789abcdef0 \
      --lustre-configuration AutoImportPolicy=NEW_CHANGED
  ```

After successfully updating the file system's `AutoImportPolicy`, Amazon FSx returns the description of the updated file system as JSON, as shown in the following example\.

```
{

    "FileSystems": [
        {
            "OwnerId": "111122223333",
            "CreationTime": 1549310341.483,
            "FileSystemId": "fs-0123456789abcdef0",
            "FileSystemType": "LUSTRE",
            "Lifecycle": "UPDATING",
            "StorageCapacity": 3600,
            "VpcId": "vpc-123456",
            "SubnetIds": [
                "subnet-123456"
            ],
            "NetworkInterfaceIds": [
                "eni-039fcf55123456789"
            ],
            "DNSName": "fs-0123456789abcdef0.fsx.us-east-2.amazonaws.com",
            "ResourceARN": "arn:aws:fsx:us-east-2:123456:file-system/fs-0123456789abcdef0",
            "Tags": [
                {
                    "Key": "Name",
                    "Value": "Lustre-TEST-1"
                }
            ],
            "LustreConfiguration": {
                "WeeklyMaintenanceStartTime": "2:04:30",
                "DeploymentType": "PERSISTENT_1",
                "DataRepositoryConfiguration": {
                    "AutoImportPolicy": "NEW_CHANGED",
                    "Lifecycle": "UPDATING",
                    "ImportPath": "s3://lustre-export-test-bucket/",
                    "ExportPath": "s3://lustre-export-test-bucket/export",
                    "ImportedFileChunkSize": 1024
                }
            }
        }
    ]
}
```