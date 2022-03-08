# How to use Amazon FSx for Lustre metrics<a name="how_to_use_metrics"></a>

The metrics reported by Amazon FSx for Lustre provide information that you can analyze in different ways\. The list following shows some common uses for the metrics\. These are suggestions to get you started, not a comprehensive list\.


| How Do I Determine\.\.\. | Relevant Metrics | 
| --- | --- | 
| My file system's throughput? | SUM\(DataReadBytes \+ DataWriteBytes\)/Period \(in seconds\)  | 
| My file system's IOPS? | Total IOPS = SUM\(DataReadOperations \+ DataWriteOperations \+ MetadataOperations\)/Period \(in seconds\) | 
| My file system's data compression ratio? | SUM\(LogicalDiskUsage\) / SUM\(PhysicalDiskUsage\)  | 

## Metric math: Data compression ratio<a name="metric-math-compression-ratio"></a>

Using metric math, you can query multiple CloudWatch metrics and use math expressions to create new time series based on these metrics\. You can visualize the resulting time series in the CloudWatch console and add them to dashboards\. For more information on metric math, see [Use Metric Math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html) in the * Amazon CloudWatch User Guide\.*

This metric math expression calculates the data compression ratio of your Amazon FSx for Lustre file system\. To calculate this ratio, first get the sum statistic of the total logical disk usage \(without compression\), which is provided by the `LogicalDiskUsage` metric\. Then divide that by the sum statistic of the total physical disk usage \(with compression\), provided by the `PhysicalDiskUsage` metric\.

So if your logic is this: sum of `LogicalDiskUsage` ÷ sum of `PhysicalDiskUsage`

Then your CloudWatch metric information is the following\.


| ID | Usable metric | Statistic | Period | 
| --- | --- | --- | --- | 
| m1 | LogicalDiskUsage  | Sum | 1 minute | 
| m2 | PhysicalDiskUsage | Sum | 1 minute | 

Your metric math ID and expression are the following\.


| ID | Expression | 
| --- | --- | 
| e1 | m1/m2 | 

`e1` is the data compression ratio\.