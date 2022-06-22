# Lustre data compression<a name="data-compression"></a>

You can use the Lustre data compression feature to achieve cost savings on your high\-performance Amazon FSx for Lustre file systems and backup storage\. When data compression is enabled, Amazon FSx for Lustre automatically compresses newly written files before they are written to disk and automatically uncompresses them when they are read\.

Data compression uses the LZ4 algorithm, which is optimized to deliver high levels of compression without adversely impacting file system performance\. LZ4 is a Lustre community\-trusted and performance\-oriented algorithm that provides a balance between compression speed and compressed file size\.

Data compression reduces the amount of data that is transferred between Amazon FSx for Lustre file servers and storage\. If you are not already using compressed file formats, you will see an increase in overall file system throughput capacity when using data compression\. Increases in throughput capacity that are related to data compression will be capped after you have saturated your front\-end network interface cards\.

For example, if your file system is a PERSISTENT\-50 SSD deployment type, your network throughput has a baseline of 250 MB/s per TiB of storage\. Your disk throughput has a baseline of 50 MB/s per TiB\. With data compression, your disk throughput could increase from 50 MB/s per TiB to a maximum of 250 MB/s per TiB, which is the baseline network throughput limit\. For more information about network and disk throughput limits, see the file system performance tables in [Aggregate file system performance](performance.md#fsx-aggregate-perf)\.

**Topics**
+ [Managing data compression](#manage-compression)
+ [Compressing previously written files](#migrate-compression)
+ [Viewing file sizes](#view-compression)
+ [Using CloudWatch metrics](#compression-metrics)

## Managing data compression<a name="manage-compression"></a>

You can turn data compression on or off when creating a new Amazon FSx for Lustre file system\. Data compression is turned off by default when you create an Amazon FSx for Lustre file system from the console, AWS CLI, or API\.

### To turn on data compression when creating a file system \(console\)<a name="create-compression-fs-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Follow the procedure for creating a new file system described in [Step 1: Create your Amazon FSx for Lustre file system](getting-started-step1.md) in the *Getting started* section\. 

1. In the **File system details** section, for **Data compression type**, choose **LZ4**\.

1. Complete the wizard as you do when you create a new file system\.

1. Choose **Review and create**\.

1. Review the settings you chose for your Amazon FSx for Lustre file system, and then choose **Create file system**\.

When the file system is **Available**, data compression is turned on\.

### To turn on data compression when creating a file system \(CLI\)<a name="create-compression-fs-cli"></a>
+ To create an FSx for Lustre file system with data compression turned on, use the Amazon FSx CLI command [https://docs.aws.amazon.com/cli/latest/reference/fsx/create-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/create-file-system.html) with the `DataCompressionType` parameter, as shown following\. The corresponding API operation is [CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html)\.

  ```
  $ aws fsx create-file-system \
        --client-request-token CRT1234 \
        --file-system-type LUSTRE \
        --file-system-type-version 2.12 \
        --lustre-configuration DeploymentType=PERSISTENT_1,PerUnitStorageThroughput=50,DataCompressionType=LZ4 \
        --storage-capacity 3600 \
        --subnet-ids subnet-123456 \
        --tags Key=Name,Value=Lustre-TEST-1 \
        --region us-east-2
  ```

After successfully creating the file system, Amazon FSx returns the file system description as JSON, as shown in the following example\.

```
{

    "FileSystems": [
        {
            "OwnerId": "111122223333",
            "CreationTime": 1549310341.483,
            "FileSystemId": "fs-0123456789abcdef0",
            "FileSystemType": "LUSTRE",
            "FileSystemTypeVersion": "2.12",
            "Lifecycle": "CREATING",
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
                "DeploymentType": "PERSISTENT_1",
                "DataCompressionType": "LZ4",
                "PerUnitStorageThroughput": 50
            }
        }
    ]
}
```

You can also change the data compression configuration of your existing file systems\. When you turn data compression on for an existing file system, only newly written files are compressed, and existing files are not compressed\. For more information, see [Compressing previously written files](#migrate-compression)\.

### To update data compression on an existing file system \(console\)<a name="manage-compression-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems**, and choose the Lustre file system that you want to manage data compression for\.

1. For **Actions**, choose **Update data compression type**\.

1. On the **Update data compression type** dialog box, choose **LZ4** to turn on data compression, or choose **NONE** to turn it off\.

1. Choose **Update**\.

1. You can monitor the update progress on the file systems detail page in the **Updates** tab\.

### To update data compression on an existing file system \(CLI\)<a name="manage-compression-cli"></a>

To update the data compression configuration for an existing FSx for Lustre file system, use the AWS CLI command [update\-file\-system](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html)\. Set the following parameters:
+ Set `--file-system-id` to the ID of the file system that you are updating\.
+ Set `--lustre-configuration DataCompressionType` to `NONE` to turn off data compression or `LZ4` to turn on data compression with the LZ4 algorithm\.

This command specifies that data compression is turned on with the LZ4 algorithm\.

```
$ aws fsx update-file-system \
    --file-system-id fs-0123456789abcdef0 \
    --lustre-configuration DataCompressionType=LZ4
```

### Data compression configuration when creating a file system from backup<a name="migrate-compression-backup"></a>

You can use an available backup to create a new Amazon FSx for Lustre file system\. When you create a new file system from backup, there is no need to specify the `DataCompressionType`; the setting will be applied using the backup's `DataCompressionType` setting\. If you choose to specify the `DataCompressionType` when creating from backup, the value must match the backup's `DataCompressionType` setting\. 

To view the settings on a backup, choose it from the **Backups** tab of the Amazon FSx console\. Details of the backup will be listed on the **Summary** page for the backup\. You can also run the [https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-backups.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-backups.html) AWS CLI command \(the equivalent API action is [https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeBackups.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeBackups.html)\)\.

## Compressing previously written files<a name="migrate-compression"></a>

Files are uncompressed if they were created when data compression was turned off on the Amazon FSx for Lustre file system\. Turning on data compression will not automatically compress your existing uncompressed data\.

You can use the `lfs_migrate` command that is installed as a part of the Lustre client installation to compress existing files\. For an example, see [FSxL\-Compression](https://github.com/aws-samples/fsx-solutions/blob/master/FSxL-Compression) which is available on GitHub\.

## Viewing file sizes<a name="view-compression"></a>

You can use the following commands to view the uncompressed and compressed sizes of your files and directories\.
+ `du` displays compressed sizes\.
+ `du --apparent-size` displays uncompressed sizes\.
+ `ls -l` displays uncompressed sizes\.

The following examples show the output of each command with the same file\.

```
$ du -sh samplefile
272M	samplefile
$ du -sh --apparent-size samplefile
1.0G	samplefile
$ ls -lh samplefile
-rw-r--r-- 1 root root 1.0G May 10 21:16 samplefile
```

The `-h` option is useful for these commands because it prints sizes in a human\-readable format\.

## Using CloudWatch metrics<a name="compression-metrics"></a>

You can use Amazon CloudWatch Logs metrics to view your file system usage\. The `LogicalDiskUsage` metric shows the total logical disk usage \(without compression\), and the `PhysicalDiskUsage` metric shows the total physical disk usage \(with compression\)\. These two metrics are available only if your file system has data compression enabled or previously had it enabled\.

You can determine your file system's compression ratio by dividing the `Sum` of the `LogicalDiskUsage` statistic by the `Sum` of the `PhysicalDiskUsage` statistic\. For information about using metric math to calculate this ratio, see [Metric math: Data compression ratio](how_to_use_metrics.md#metric-math-compression-ratio)\. 

For more information about monitoring your file system’s performance, see [Monitoring Amazon FSx for Lustre](monitoring_overview.md)\.