# Monitoring Amazon FSx for Lustre<a name="monitoring_overview"></a>

You can use the following automated monitoring tools to watch Amazon FSx for Lustre and report when something is wrong:
+ **Monitoring using Amazon CloudWatch** – CloudWatch collects and processes raw data from Amazon FSx for Lustre into readable, near real\-time metrics\. You can create a CloudWatch alarm that sends an Amazon SNS message when the alarm changes state\.
+ **Monitoring using Lustre logging** – You can monitor the enabled logging events for your file system\. Lustre logging writes these events to Amazon CloudWatch Logs\.
+ **AWS CloudTrail log monitoring** – Share log files between accounts, monitor CloudTrail log files in real time by sending them to CloudWatch Logs, write log processing applications in Java, and validate that your log files have not changed after delivery by CloudTrail\.

**Topics**
+ [Monitoring with Amazon CloudWatch](monitoring-cloudwatch.md)
+ [Logging with Amazon CloudWatch Logs](cw-event-logging.md)
+ [Logging FSx for Lustre API calls with AWS CloudTrail](logging-using-cloudtrail.md)