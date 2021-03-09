# Managing storage and throughput capacity<a name="managing-storage-capacity"></a>

You can increase the storage capacity that is configured on your Amazon FSx for Lustre file system as you need additional storage and throughput\. Because the throughput of an Amazon FSx for Lustre file system scales linearly with storage capacity, you also get a comparable increase in throughput capacity\. To increase the storage capacity, you can use the Amazon FSx console, the AWS Command Line Interface \(AWS CLI\), or the Amazon FSx API\.

When you request an update to your file system's storage capacity, Amazon FSx automatically adds new network file servers and scales your metadata server\. While scaling storage capacity, the file system may be unavailable for a few minutes\. File operations issued by clients while the file system is unavailable will transparently retry and eventually succeed after storage scaling is complete\. During the time that the file system is unavailable, the file system status is set to `UPDATING`\. Once storage scaling is complete, the file system status is set to `AVAILABLE`\.

Amazon FSx then runs a storage optimization process that transparently rebalances data across the existing and newly added file servers\. Rebalancing is performed in the background with no impact to file system availability\. During rebalancing, you might see decreased file system performance as resources are consumed for data movement\. For most file systems, storage optimization takes a few hours up to a few days\. You can access and use your file system during the optimization phase\.

You can track the storage optimization progress at any time using the Amazon FSx console, CLI, and API\. For more information, see [Monitoring storage capacity increases](#monitoring-storage-capacity-increase)\.

**Topics**
+ [Important points to know when increasing storage capacity](#storage-capacity-important-to-know)
+ [When to increase storage and throughput capacity](#when-to-modify-storage-capacity)
+ [How concurrent storage scaling and backup requests are handled](#storage-capacity-changes-and-backups)
+ [How to increase storage capacity](#increase-storage-capacity)
+ [Monitoring storage capacity increases](#monitoring-storage-capacity-increase)

## Important points to know when increasing storage capacity<a name="storage-capacity-important-to-know"></a>

Here are a few important items to consider when increasing storage capacity:
+ **Increase only** – You can only *increase* the amount of storage capacity for a file system; you cannot decrease storage capacity\.
+ **Increase increments** – When you increase storage capacity, use the increments listed in the **Increase storage capacity** dialog box\.
+ **Time between increases** – You can't make further storage capacity increases on a file system until 6 hours after the last increase was requested, or until the storage optimization process has completed, whichever time is longer\.
+ **Throughput capacity** – The Amazon FSx console, the AWS CLI, and the Amazon FSx API don't allow you to specify a desired throughput level\. However, you automatically increase throughput capacity when you increase the storage capacity\. For persistent HDD file systems with SSD cache, the read cache storage capacity is also similarly increased to maintain an SSD cache that is sized to 20 percent of the HDD storage capacity\. Amazon FSx calculates the new values for the storage and throughput capacity units and lists them in the **Increase storage capacity** dialog box\.
+ **Deployment type** – You can increase the storage capacity of all deployment types except for scratch 1 file systems\.

## When to increase storage and throughput capacity<a name="when-to-modify-storage-capacity"></a>

Increase your file system's storage capacity when it's running low on free storage capacity\. Use the `FreeStorageCapacity` CloudWatch metric to monitor the amount of free storage that is available on the file system\. You can create an Amazon CloudWatch alarm on this metric and get notified when it drops below a specific threshold\. For more information, see [Monitoring with Amazon CloudWatch](monitoring_overview.md#monitoring-cloudwatch)\.

You can use CloudWatch metrics to monitor your file system's ongoing throughput usage levels\. If you determine that your file system needs a higher throughput capacity, you can use the metric information to help you decide how much to increase the storage capacity\. For information about how to determine your file system's current throughput, see [How to Use Amazon FSx for Lustre Metrics](how_to_use_metrics.md)\. For information about how storage capacity affects throughput capacity, see [Amazon FSx for Lustre Performance](performance.md)\.

You can also view your file system's storage capacity and total throughput on the **Summary** panel of the file system details page\.

## How concurrent storage scaling and backup requests are handled<a name="storage-capacity-changes-and-backups"></a>

You can request a backup just before a storage scaling workflow begins or while it is in progress\. The sequence of how Amazon FSx handles the two requests is as follows:
+ If a storage scaling workflow is in progress \(storage scaling status is `IN_PROGRESS` and file system status is `UPDATING`\) and you request a backup, the backup request is queued\. The backup task is started when storage scaling is in the storage optimization phase \(storage scaling status is `UPDATED_OPTIMIZING` and file system status is `AVAILABLE`\)\.
+ If the backup is in progress \(backup status is `CREATING`\) and you request storage scaling, the storage scaling request is queued\. The storage scaling workflow is started when Amazon FSx is transferring the backup to Amazon S3 \(backup status is `TRANSFERRING`\)\.

If a storage scaling request is pending and a file system backup request is also pending, the backup task has higher precedence\. The storage scaling task does not start until the backup task is finished\.

## How to increase storage capacity<a name="increase-storage-capacity"></a>

You can increase a file system's storage capacity using the Amazon FSx console, the AWS CLI, or the Amazon FSx API\.

### To increase storage capacity for a file system \(console\)<a name="increase-storage-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems**, and choose the Lustre file system that you want to increase storage capacity for\.

1. For **Actions**, choose **Update storage capacity**\. Or, in the **Summary** panel, choose **Update** next to the file system's **Storage capacity** to display the **Increase storage capacity** dialog box\.  
![\[Console screenshot showing the Update storage capacity pane.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/update-storage-capacity-modal.png)

1. For **Desired storage capacity**, provide a new storage capacity in GiB that is greater than the current storage capacity of the file system:
   + For a persistent SSD or scratch 2 file system, this value must be in multiples of 2400 GiB\.
   + For a persistent HDD file system, this value must be in multiples of 6000 GiB for 12 MB/s/TiB file systems and multiples of 1800 GiB for 40 MB/s/TiB file systems\.
**Note**  
You cannot increase the storage capacity of scratch 1 file systems\.

1. Choose **Update** to initiate the storage capacity update\.

1. You can monitor the update progress on the file systems detail page in the **Updates** tab\.

### To increase storage capacity for a file system \(CLI\)<a name="increase-storage-cli"></a>

To increase the storage capacity for an Amazon FSx for Lustre file system, use the AWS CLI command [update\-file\-system](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html)\. Set the following parameters:
+ Set `--file-system-id` to the ID of the file system you are updating\.
+ Set `--storage-capacity` to an integer value that is the amount, in GiB, of the storage capacity increase\. For a persistent SSD or scratch 2 file system, this value must be in multiples of 2400\. For a persistent HDD file system, this value must be in multiples of 6000 for 12 MB/s/TiB file systems and multiples of 1800 for 40 MB/s/TiB file systems\. The new target value must be greater than the current storage capacity of the file system\.

This command specifies a storage capacity target value of 9600 GiB for a persistent SSD or scratch 2 file system\.

```
$ aws fsx update-file-system \
    --file-system-id fs-0123456789abcdef0 \
    --storage-capacity 9600
```

You can monitor the progress of the update by using the AWS CLI command [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html)\. Look for the `administrative-actions` in the output\.

For more information, see [AdministrativeAction](https://docs.aws.amazon.com/fsx/latest/APIReference/API_AdministrativeAction.html)\.

## Monitoring storage capacity increases<a name="monitoring-storage-capacity-increase"></a>

You can monitor the progress of a storage capacity increase using the Amazon FSx console, the API, or the AWS CLI\.

### Monitoring increases in the console<a name="monitor-storage-action-console"></a>

In the **Updates** tab in the file system details page, you can view the 10 most recent updates for each update type\.

![\[Console screenshot showing recent updates list.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/fs-updates-panel.png)

You can view the following information:

****Update type****  
Supported types are **Storage capacity** and **Storage optimization**\.

****Target value****  
The desired value to update the file system's storage capacity to\.

****Status****  
The current status of the storage capacity updates\. The possible values are as follows:  
+ **Pending** – Amazon FSx has received the update request, but has not started processing it\.
+ **In progress** – Amazon FSx is processing the update request\.
+ **Updated; Optimizing** – Amazon FSx has increased the file system's storage capacity\. The storage optimization process is now rebalancing data across the file servers\.
+ **Completed** – The storage capacity increase completed successfully\.
+ **Failed** – The storage capacity increase failed\. Choose the question mark \(**?**\) to see details on why the storage update failed\.

****Progress %****  
Displays the progress of the storage optimization process as percent complete\.

****Request time****  
The time that Amazon FSx received the update action request\.

### Monitoring increases with the AWS CLI and API<a name="monitor-storage-action-cli-api"></a>

You can view and monitor file system storage capacity increase requests using the [describe\-file\-systems](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) AWS CLI command and the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API action\. The `AdministrativeActions` array lists the 10 most recent update actions for each administrative action type\. When you increase a file system's storage capacity, two `AdministrativeActions` are generated: a `FILE_SYSTEM_UPDATE` and a `STORAGE_OPTIMIZATION` action\. 

The following example shows an excerpt of the response of a describe\-file\-systems CLI command\. The file system has a storage capacity of 4800 GB, and there is a pending administrative action to increase the storage capacity to 9600 GB\.

```
{
    "FileSystems": [
        {
            "OwnerId": "111122223333",
            .
            .
            .
            "StorageCapacity": 4800,
            "AdministrativeActions": [
                {
                     "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
                     "RequestTime": 1581694764.757,
                     "Status": "PENDING",
                     "TargetFileSystemValues": {
                         "StorageCapacity": 9600
                     }
                },
                {
                    "AdministrativeActionType": "STORAGE_OPTIMIZATION",
                    "RequestTime": 1581694764.757,
                    "Status": "PENDING",
                }
            ]
```

Amazon FSx processes the `FILE_SYSTEM_UPDATE` action first, adding new file servers to the file system\. When the new storage is available to the file system, the `FILE_SYSTEM_UPDATE` status changes to `UPDATED_OPTIMIZING`\. The storage capacity shows the new larger value, and Amazon FSx begins processing the `STORAGE_OPTIMIZATION` administrative action\. This is shown in the following excerpt of the response of a describe\-file\-systems CLI command\. 

The `ProgressPercent` property displays the progress of the storage optimization process\. After the storage optimization process completes successfully, the status of the `FILE_SYSTEM_UPDATE` action changes to `COMPLETED`, and the `STORAGE_OPTIMIZATION` action no longer appears\.

```
{
    "FileSystems": [
        {
            "OwnerId": "111122223333",
            .
            .
            .
            "StorageCapacity": 9600,
            "AdministrativeActions": [
                {
                    "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
                    "RequestTime": 1581694764.757,
                    "Status": "UPDATED_OPTIMIZING",
                    "TargetFileSystemValues": {
                        "StorageCapacity": 9600
                }
                },
                {
                    "AdministrativeActionType": "STORAGE_OPTIMIZATION",
                    "RequestTime": 1581694764.757,
                    "Status": "IN_PROGRESS",
                    "ProgressPercent": 50,
                }
            ]
```



If the storage capacity increase fails, the status of the `FILE_SYSTEM_UPDATE` action changes to `FAILED`\. The `FailureDetails` property provides information about the failure, shown in the following example\.

```
{
    "FileSystems": [ 
        { 
            "OwnerId": "111122223333",
            .
            .
            .
            "StorageCapacity": 4800,
            "AdministrativeActions": [ 
                { 
                    "AdministrativeActionType": "FILE_SYSTEM_UPDATE",
                    "FailureDetails": { 
                        "Message": "string"
                    },
                    "RequestTime": 1581694764.757,
                    "Status": "FAILED",
                    "TargetFileSystemValues": 
                        "StorageCapacity": 9600
                }
            ]
```