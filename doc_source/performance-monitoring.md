# Monitoring performance and usage<a name="performance-monitoring"></a>

Every minute, Amazon FSx for Lustre emits usage metrics for each disk \(MDT and OST\) to Amazon CloudWatch\.

To view aggregate file system usage details, you can look at the Sum statistic of each metric\. For example, the Sum of the `DataReadBytes` statistic reports the total read throughput seen by all the OSTs in a file system\. Similarly, the Sum of the `FreeDataStorageCapacity` statistic reports the total available storage capacity for file data in the file system\.

For more information on monitoring your file system’s performance, see [Monitoring Amazon FSx for Lustre](monitoring_overview.md)\.