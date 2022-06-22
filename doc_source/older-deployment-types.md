# Working with older deployment types<a name="older-deployment-types"></a>

This section applies to file systems created with Scratch 1, Scratch 2, and Persistent 1 deployment types\.

**Topics**
+ [Link your file system to an Amazon S3 bucket](#legacy-link-to-S3)
+ [Automatically import updates from your S3 bucket](#legacy-auto-import-from-s3)

## Link your file system to an Amazon S3 bucket<a name="legacy-link-to-S3"></a>

When you create an Amazon FSx for Lustre file system, you can link it to a durable data repository in Amazon S3\. Before you create your file system, make sure that you have already created the Amazon S3 bucket to which you are linking\. In the **Create file system** wizard, you set the following data repository configuration properties in the optional **Data Repository Import/Export** pane\.
+ Choose how Amazon FSx keeps your file and directory listing up to date as you add or modify objects in your S3 bucket after the file system is created\. For more information, see [Automatically import updates from your S3 bucket](#legacy-auto-import-from-s3)\.
+ **Import bucket**: Enter the name of the S3 bucket that you are using for the linked repository\.
+ **Import prefix**: Enter an optional import prefix if you want to import only some file and directory listings of data in your S3 bucket into your file system\. The import prefix defines where in your S3 bucket to import data from\.
+ **Export prefix**: Defines where Amazon FSx exports the contents of your file system to your linked S3 bucket\.

You can have a 1:1 mapping where Amazon FSx exports data from your FSx for Lustre file system back to the same directories on the S3 bucket that it was imported from\. To have a 1:1 mapping, specify an export path to the S3 bucket without any prefixes when you create your file system\.
+ When you create file system using the console, choose **Export prefix > A prefix you specify** option, and keep the prefix field blank\.
+ When you create a file system using the AWS CLI or API, specify the export path as the name of the S3 bucket without any additional prefixes, for example, `ExportPath=s3://lustre-export-test-bucket/`\.

Using this method, you can include an import prefix when you specify the import path, and it doesn't impact a 1:1 mapping for exports\.

### Creating file systems linked to an S3 bucket<a name="legacy-create-linked-S3"></a>

The following procedures walk you through the process of creating an Amazon FSx file system linked to an S3 bucket using the AWS Management Console and AWS Command Line Interface \(AWS CLI\)\.

------
#### [ Console ]

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **Create file system**\.

1. For the file system type, choose **FSx for Lustre**, and then choose **Next**\.

1. Provide the information required for the **File system details** and **Network and security** sections\. For more information, see [Step 1: Create your Amazon FSx for Lustre file system](getting-started-step1.md)\.

1. You use the **Data repository import/export** panel to configure a linked data repository in Amazon S3\. Select **Import data from and export data to S3** to expand the **Data Repository Import/Export** section and configure the data repository settings\.  
![\[The Data repository import and export panel for configuring a linked data repository in Amazon S3.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/legacy-data-repository-import-export.png)

1. Choose how Amazon FSx keeps your file and directory listing up to date as you add or modify objects in your S3 bucket\. When you create your file system, your existing S3 objects appear as file and directory listings\.
   + **Update my file and directory listing as objects are added to my S3 bucket**: \(Default\) Amazon FSx automatically updates file and directory listings of any new objects added to the linked S3 bucket that do not currently exist in the FSx file system\. Amazon FSx does not update listings for objects that have changed in the S3 bucket\. Amazon FSx does not delete listings of objects that are deleted in the S3 bucket\.
**Note**  
The default import preferences setting for importing data from a linked S3 bucket using the CLI and API is `NONE`\. The default import preferences setting when using the console is to update Lustre as new objects are added to the S3 bucket\.
   + **Update my file and directory listing as objects are added to or changed in my S3 bucket**: Amazon FSx automatically updates file and directory listings of any new objects added to the S3 bucket and any existing objects that are changed in the S3 bucket after you choose this option\. Amazon FSx does not delete listings of objects that are deleted in the S3 bucket\.
   + **Update my file and directory listing as objects are added to, changed in, or deleted from my S3 bucket**: Amazon FSx automatically updates file and directory listings of any new objects added to the S3 bucket, any existing objects that are changed in the S3 bucket, and any existing objects that are deleted in the S3 bucket after you choose this option\.
   + **Do not update my file and directly listing when objects are added to, changed in, or deleted from my S3 bucket** \- Amazon FSx only updates file and directory listings from the linked S3 bucket when the file system is created\. FSx does not update file and directory listings for any new, changed, or deleted objects after choosing this option\.

1. Enter an optional **Import prefix** if you want to import only some of the file and directory listings of data in your S3 bucket into your file system\. The import prefix defines where in your S3 bucket to import data from\. For more information, see [Automatically import updates from your S3 bucket](autoimport-data-repo-dra.md)\.

1. Choose one of the available **Export prefix** options:
   + **A unique prefix that Amazon FSx creates in your bucket**: Choose this option to export new and changed objects using a prefix generated by FSx for Lustre\. The prefix looks like the following: `/FSxLustrefile-system-creation- timestamp`\. The timestamp is in UTC format, for example `FSxLustre20181105T222312Z`\.
   + **The same prefix that you imported from \(replace existing objects with updated ones\)**: Choose this option to replace existing objects with updated ones\.
   + **A prefix you specify**: Choose this option to preserve your imported data and to export new and changed objects using a prefix that you specify\. To achieve a 1:1 mapping when exporting data to your S3 bucket, choose this option and leave the prefix field blank\. FSx will export data to same directories it was imported from\.

1. \(Optional\) Set **Maintenance preferences**, or use the system defaults\.

1. Choose **Next**, and review the file system settings\. Make any changes if needed\.

1. Choose **Create file system**\.

------
#### [ AWS CLI ]

The following example creates an Amazon FSx file system linked to the `lustre-export-test-bucket`, with an import preference that imports any new, changed, and deleted files in the linked data repository after the file system is created\.

**Note**  
The default import preferences setting for importing data from a linked S3 bucket using the CLI and API is `NONE`, which is different from the default behavior when using the console\.

To create an FSx for Lustre file system, use the Amazon FSx CLI command [https://docs.aws.amazon.com/cli/latest/reference/fsx/create-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/create-file-system.html), as shown below\. The corresponding API operation is [https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html)\.

```
$ aws fsx create-file-system \
--client-request-token CRT1234 \
--file-system-type LUSTRE \
--file-system-type-version 2.12 \
--lustre-configuration
AutoImportPolicy=NEW_CHANGED_DELETED,DeploymentType=PERSISTENT_1,ImportPath=s
3://lustre-export-test-bucket/,ExportPath=s3://lustre-export-test-bucket/export,
PerUnitStorageThroughput=50 \
--storage-capacity 2400 \
--subnet-ids subnet-123456 \
--tags Key=Name,Value=Lustre-TEST-1 \
--region us-east-2
```

After you successfully create the file system, Amazon FSx returns the file system description as JSON, as shown in the following example\.

```
{
    "FileSystems": [
        {
            "OwnerId": "owner-id-string",
            "CreationTime": 1549310341.483,
            "FileSystemId": "fs-0123456789abcdef0",
            "FileSystemType": "LUSTRE",
            "FileSystemTypeVersion": "2.12",
            "Lifecycle": "CREATING",
            "StorageCapacity": 2400,
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
                "DeploymentType": "PERSISTENT_1",
                "DataRepositoryConfiguration": {
                    "AutoImportPolicy": "NEW_CHANGED_DELETED",
                    "Lifecycle": "UPDATING",
                    "ImportPath": "s3://lustre-export-test-bucket/",
                    "ExportPath": "s3://lustre-export-test-bucket/export",
                    "ImportedFileChunkSize": 1024
                },
                "PerUnitStorageThroughput": 50
            }
        }
    ]
}
```

------

### Viewing a file system's export path<a name="legacy-view-export"></a>

You can view a file system's export path using the FSx for Lustre console, the AWS CLI, and the API\.

------
#### [ Console ]

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)

1. Choose **File system name** or **File system ID** for the FSx for Lustre file system for which you want to view the export path\.

    The file system details page appears for that file system\.

1. Choose the **Data repository** tab\.

   The **Data repository integration** panel appears, showing the import and export paths\.

![\[The Data repository import and export paths in the Data repository integration panel.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/legacy-view-export-path.png)

------
#### [ CLI ]

To determine the export path for your file system, use the [https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) AWS CLI command\.

```
aws fsx describe-file-systems
```

Look for the `ExportPath` property under `LustreConfiguration` in the response\.

```
{
    "OwnerId": "111122223333",
    "CreationTime": 1563382847.014,
    "FileSystemId": "",
    "FileSystemType": "LUSTRE",
    "Lifecycle": "AVAILABLE",
    "StorageCapacity": 2400,
    "VpcId": "vpc-6296a00a",
    "SubnetIds": [
        "subnet-1111111"
    ],
    "NetworkInterfaceIds": [
        "eni-0c288d5b8cc06c82d",
        "eni-0f38b702442c6918c"
    ],
    "DNSName": "fs-0123456789abcdef0.fsx.us-east-2.amazonaws.com",
    "ResourceARN": "arn:aws:fsx:us-east-2:267731178466:file-system/fs-0123456789abcdef0",
    "Tags": [
        {
          "Key": "Name",
          "Value": "Lustre System"
        }
    ],
	"LustreConfiguration": {
    "DeploymentType": "PERSISTENT_1",
    "DataRepositoryConfiguration": {
    "AutoImportPolicy": " NEW_CHANGED_DELETED",
    "Lifecycle": "AVAILABLE",
    "ImportPath": "s3://lustre-export-test-bucket/",
    "ExportPath": "s3://lustre-export-test-bucket/FSxLustre20190717T164753Z",
    "ImportedFileChunkSize": 1024
    }
  },
  "PerUnitStorageThroughput": 50,
  "WeeklyMaintenanceStartTime": "6:09:30"
}
```

------

### Data repository lifecycle state<a name="legacy-data-lifecycle"></a>

The data repository lifecycle state provides status information about the file system's linked data repository\. A data repository can have the following Lifecycle states\.
+ **Creating**: Amazon FSx is creating the data repository configuration between the file system and the linked data repository\. The data repository is unavailable\.
+ **Available**: The data repository is available for use\.
+ **Updating**: The data repository configuration is undergoing a customer\-initiated update that might affect its availability\.
+ **Misconfigured**: Amazon FSx cannot automatically import updates from the S3 bucket until the data repository configuration is corrected\. For more information, see [Troubleshooting a misconfigured linked S3 bucket](troubleshooting.md#troubleshooting-misconfigured-data-repository)\.

You can view a file system's linked data repository lifecycle state using the Amazon FSx console, the AWS Command Line Interface, and the Amazon FSx API\. In the Amazon FSx console, you can access the data repository **Lifecycle state** in the **Data Repository Integration** pane of the **Data Repository** tab for the file system\. The `Lifecycle` property is located in the `DataRepositoryConfiguration` object in the response of a [https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) CLI command \(the equivalent API action is [https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html)\)\.

## Automatically import updates from your S3 bucket<a name="legacy-auto-import-from-s3"></a>

By default, when you create a new file system, Amazon FSx imports the file metadata \(the name, ownership, timestamp, and permissions\) of objects in the linked S3 bucket at file system creation\. You can configure your FSx for Lustre file system to automatically import metadata of objects that are added to, changed in, or deleted from your S3 bucket after file system creation\. FSx for Lustre updates the file and directory listing of a changed object after creation in the same manner as it imports file metadata at file system creation\. When Amazon FSx updates the file and directory listing of a changed object, if the changed object in the S3 bucket no longer contains its metadata, Amazon FSx maintains the current metadata values of the file, rather than using default permissions\.

**Note**  
Import settings are available on FSx for Lustre file systems created after 3:00 pm EDT, July 23, 2020\.

You can set import preferences when you create a new file system, and you can update the setting on existing file systems using the FSx management console, the AWS CLI, and the AWS API\. When you create your file system, your existing S3 objects appear as file and directory listings\. After you create your file system, how do you want to update it as the contents of your S3 bucket are updated? A file system can have one of the following Import preferences:

**Note**  
The FSx for Lustre file system and its linked S3 bucket must be located in the same AWS Region to automatically import updates\.
+ **Update my file and directory listing as objects are added to my S3 bucket**: \(Default\) Amazon FSx automatically updates file and directory listings of any new objects added to the linked S3 bucket that do not currently exist in the FSx file system\. Amazon FSx does not update listings for objects that have changed in the S3 bucket\. Amazon FSx does not delete listings of objects that are deleted in the S3 bucket\.
**Note**  
The default import preferences setting for importing data from a linked S3 bucket using the CLI and API is `NONE`\. The default import preferences setting when using the console is to update Lustre as new objects are added to the S3 bucket\.
+ **Update my file and directory listing as objects are added to or changed in my S3 bucket**: Amazon FSx automatically updates file and directory listings of any new objects added to the S3 bucket and any existing objects that are changed in the S3 bucket after you choose this option\. Amazon FSx does not delete listings of objects that are deleted in the S3 bucket\.
+ **Update my file and directory listing as objects are added to, changed in, or deleted from my S3 bucket**: Amazon FSx automatically updates file and directory listings of any new objects added to the S3 bucket, any existing objects that are changed in the S3 bucket, and any existing objects that are deleted in the S3 bucket after you choose this option\.
+ **Do not update my file and directly listing when objects are added to, changed in, or deleted from my S3 bucket** \- Amazon FSx only updates file and directory listings from the linked S3 bucket when the file system is created\. FSx does not update file and directory listings for any new, changed, or deleted objects after choosing this option\.

When you set the import preferences to update your file system file and directory listings based on changes in the linked S3 bucket, Amazon FSx creates an event notification configuration on the linked S3 bucket named `FSx`\. Do not modify or delete the `FSx` event notification configuration on the S3 bucket—doing so prevents the automatic import of new or changed file and directory listings to your file system\. 

When Amazon FSx updates a file listing that has changed on the linked S3 bucket, it overwrites the local file with the updated version, even if the file is write\-locked\. Similarly, when Amazon FSx updates a file listing when the corresponding object has been deleted on the linked S3 bucket, it deletes the local file, even if the file is write\- locked\.

Amazon FSx makes a best effort to update your file system\. Amazon FSx cannot update the file system with changes in the following situations:
+ When Amazon FSx does not have permission to open the changed or new S3 object\.
+ When the `FSx` event notification configuration on the linked S3 bucket is deleted or changed\.

Either of these conditions cause the data repository lifecycle state to become **Misconfigured**\. For more information, see [Data repository lifecycle state](#legacy-data-lifecycle)\.

### Prerequisites<a name="legacy-auto-import-prereq"></a>

The following conditions are required for Amazon FSx to automatically import new, changed, or deleted files from the linked S3 bucket:
+ The file system and its linked S3 bucket must be located in the same AWS Region\.
+ The S3 bucket does not have a misconfigured Lifecycle state\. For more information, see see [Data repository lifecycle state](#legacy-data-lifecycle)\.
+ Your account must have the permissions required to configure and receive event notifications on the linked S3 bucket\.

### Types of file changes supported<a name="legacy-file-types-supported"></a>

Amazon FSx supports importing the following changes to files and folders that occur in the linked S3 bucket:
+ Changes to file contents
+ Changes to file or folder metadata
+ Changes to symlink target or metadata

  

### Updating import preferences<a name="legacy-update-import-pref"></a>

You can set a file system's import preferences when you create a new file system\. For more information, see [Linking your file system to an S3 bucket](create-dra-linked-data-repo.md)\.

You can also update a file system's import preferences after it is created using the AWS Management Console, the AWS CLI, and the Amazon FSx API, as shown in the following procedure\.

------
#### [ Console ]

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **File systems**\.

1. Select the file system that you want to manage to display the file system details\.

1. Choose **Data repository** to view the data repository settings\. You can modify the import preferences if the lifecycle state is **AVAILABLE** or **MISCONFIGURED**\. For more information, see [Data repository lifecycle state](#legacy-data-lifecycle)\.

1. Choose **Actions**, and then choose **Update import preferences** to display the **Update import preferences** dialog box\.

1. Select the new setting, and then choose **Update** to make the change\.

------
#### [ CLI ]

To update import preferences, use the [https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html) CLI command\. The corresponding API operation is [https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html)\. 

After you successfully update the file system's `AutoImportPolicy`, Amazon FSx returns the description of the updated file system as JSON, as shown here:

```
{
    "FileSystems": [
        {
            "OwnerId": "111122223333",
            "CreationTime": 1549310341.483,
            "FileSystemId": "fs-0123456789abcdef0",
            "FileSystemType": "LUSTRE",
            "Lifecycle": "UPDATING",
            "StorageCapacity": 2400,
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
                "DeploymentType": "PERSISTENT_1",
                "DataRepositoryConfiguration": {
                    "AutoImportPolicy": "NEW_CHANGED_DELETED",
                    "Lifecycle": "UPDATING",
                    "ImportPath": "s3://lustre-export-test-bucket/",
                    "ExportPath": "s3://lustre-export-test-bucket/export",
                    "ImportedFileChunkSize": 1024
                }
                "PerUnitStorageThroughput": 50,
                "WeeklyMaintenanceStartTime": "2:04:30"
            }
        }
    ]
}
```

------