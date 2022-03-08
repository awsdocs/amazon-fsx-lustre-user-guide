# Logging with Amazon CloudWatch Logs<a name="cw-event-logging"></a>

FSx for Lustre supports logging of error and warning events for data repositories associated with your file system to Amazon CloudWatch Logs\.

**Note**  
Logging with Amazon CloudWatch Logs is only available on Amazon FSx for Lustre file systems created after 3pm PST on November 30, 2021\.

**Topics**
+ [Logging overview](#event-log-overview)
+ [Log destinations](#event-log-destinations)
+ [Managing logging](#manage-logging)
+ [Viewing logs](#view-logs)

## Logging overview<a name="event-log-overview"></a>

If you have data repositories linked to your FSx for Lustre file system, you can enable logging of data repository events to Amazon CloudWatch Logs\. Error and warning events can be logged from the following data repository operations:
+ Automatic export
+ Data repository tasks

For more information on these operations and on linking to data repositories, see [Using data repositories with Amazon FSx for Lustre](fsx-data-repositories.md)\.

You can configure the log levels that Amazon FSx logs; that is, whether Amazon FSx will log only error events, only warning events, or both error and warning events\. You can also turn event logging off at any time\.

## Log destinations<a name="event-log-destinations"></a>

When logging is enabled, FSx for Lustre must be configured with an Amazon CloudWatch Logs destination\. The event log destination is an Amazon CloudWatch Logs log group, and Amazon FSx creates a log stream for your file system within this log group\. CloudWatch Logs allows you to store, view, and search audit event logs in the Amazon CloudWatch console, run queries on the logs using CloudWatch Logs Insights, and trigger CloudWatch alarms or Lambda functions\.

You choose the log destination when you create your FSx for Lustre file system or afterwards by updating it\. For more information, see [Managing logging](#manage-logging)\.

By default, Amazon FSx will create and use a default CloudWatch Logs log group in your account as the event log destination\. If you want to use a custom CloudWatch Logs log group as the event log destination, here are the requirements for the name and location of the event log destination:
+ The name of the CloudWatch Logs log group must begin with the `/aws/fsx/` prefix\.
+ If you don't have an existing CloudWatch Logs log group when you create or update a file system on the console, Amazon FSx for Lustre can create and use a default log stream in the CloudWatch Logs `/aws/fsx/lustre` log group\. The log stream will be created with the format `datarepo_file_system_id` \(for example, `datarepo_fs-0123456789abcdef0`\)\.
+ If you don't want to use the default log group, the configuration UI lets you create a CloudWatch Logs log group when you create or update your file system on the console\.
+ The destination CloudWatch Logs log group must be in the same AWS partition, AWS Region, and AWS account as your Amazon FSx for Lustre file system\.

You can change the event log destination at any time\. When you do so, new event logs are sent only to the new destination\.

## Managing logging<a name="manage-logging"></a>

You can enable logging when you create a new FSx for Lustre file system or afterwards by updating it\. Logging is turned on by default when you create a ﬁle system from the Amazon FSx console\. However, logging is turned off by default when you create a ﬁle system with the AWS CLI or Amazon FSx API\.

On existing file systems that have logging enabled, you can change the event logging settings, including which log level to log events for, and the log destination\. You can perform these tasks using the Amazon FSx console, AWS CLI, or Amazon FSx API\.

### To enable logging when creating a file system \(console\)<a name="create-enable-event-logs"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Follow the procedure for creating a new file system described in [Step 1: Create your Amazon FSx for Lustre file system](getting-started-step1.md) in the Getting Started section\. 

1. Open the **Logging \- optional** section\. Logging is enabled by default\.  
![\[\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/create-fs-logging.png)

1. Continue with the next section of the file system creation wizard\.

When the file system becomes **Available**, logging will be enabled\.

### To enable logging when creating a file system \(CLI\)<a name="w166aac21c11c13b7b3"></a>

1. When creating a new file system, use the `LogConfiguration` property with the [CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html) operation to enable logging for the new file system\.

   ```
   create-file-system --file-system-type LUSTRE \
       --storage-capacity 1200 --subnet-id subnet-08b31917a72b548a9  \ 
       --lustre-configuration "LogConfiguration={Level=DISABLED, \
           Destination="arn:aws:logs:us-east-1:234567890123:log-group:/aws/fsx/testEventLogging"}"
   ```

1. When the file system becomes **Available**, logging feature will be enabled\.

### To change the logging configuration \(console\)<a name="w166aac21c11c13b7b5"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Navigate to **File systems**, and choose the Lustre file system that you want to manage logging for\.

1. Choose the **Monitoring** tab\.

1. On the **Logging** panel, choose **Update**\.

1. On the **Update logging configuration** dialog, change the desired settings\.

   1. Choose **Log errors** to log only error events, or **Log warnings** to log only warning events, or both\. Logging is disabled if you don't make a selection\.

   1. Choose an existing CloudWatch Logs log destination or create a new one\.

1. Choose **Save**\.

### To change the logging configuration \(CLI\)<a name="w166aac21c11c13b7b7"></a>
+ Use the [https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/update-file-system.html) CLI command or the equivalent [https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) API operation\.

  ```
  update-file-system --file-system-id fs-0123456789abcdef0 \
      --lustre-configuration "LogConfiguration={Level=WARN_ERROR, \
          Destination="arn:aws:logs:us-east-1:234567890123:log-group:/aws/fsx/testEventLogging"}"
  ```

## Viewing logs<a name="view-logs"></a>

You can view the logs after Amazon FSx has started emitting them\. You can view the logs as follows: 
+ You can view logs by going to the Amazon CloudWatch console and choosing the log group and log stream to which your event logs are sent\. For more information, see [ View log data sent to CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Working-with-log-groups-and-streams.html) in the *Amazon CloudWatch Logs User Guide*\. 
+ You can use CloudWatch Logs Insights to interactively search and analyze your log data\. For more information, see [ Analyzing log data with CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html), in the *Amazon CloudWatch Logs User Guide*\.
+ You can also export logs to Amazon S3\. For more information, see [ Exporting log data to Amazon S3](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/S3Export.html), in the *Amazon CloudWatch Logs User Guide*\.

To learn about failure reasons, see [Troubleshooting import and export failures](failed-tasks.md)\.