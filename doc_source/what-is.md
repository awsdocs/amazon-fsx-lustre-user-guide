# What Is Amazon FSx for Lustre?<a name="what-is"></a>

Amazon FSx for Lustre provides fully managed file systems that are optimized for compute\-intensive workloads, such as high\-performance computing, machine learning, and media processing workflows\. Many of these applications require the high performance and low latencies of scale\-out, parallel file systems\. Operating these file systems typically requires specialized expertise and administrative overhead, requiring you to provision storage servers and tune complex performance parameters\. With Amazon FSx for Lustre, in minutes you can launch and run a [Lustre](http://lustre.org/) file system that can process massive datasets at up to hundreds of gigabytes per second of throughput, millions of IOPS, and submillisecond latencies\.

Amazon FSx for Lustre provides high\-performance storage at low cost, because it is nonreplicated, on\-demand storage for short\-term, compute\-intensive processing of datasets\. The service is seamlessly integrated with Amazon Simple Storage Service \(Amazon S3\)\. Thus, you can automatically copy Amazon S3 data into your file system to run analyses for hours to days, write results back to S3, and then delete your file system\. Amazon FSx for Lustre also enables you to burst your compute\-intensive workloads from on\-premises to the AWS Cloud by importing data over AWS Direct Connect or VPN\.

As a fully managed service, Amazon FSx for Lustre eliminates the administrative overhead of setting up and provisioning file servers and storage volumes\. Additionally, Amazon FSx for Lustre keeps the underlying software powering your file systems up\-to\-date and provides rich integration with other AWS services like Amazon S3, Amazon CloudWatch, and AWS CloudTrail\.

## Assumptions<a name="assumptions"></a>

In this guide, we make the following assumptions:
+ If you use Amazon Elastic Compute Cloud \(Amazon EC2\), we assume that you're familiar with that service\. For more information on how to use Amazon EC2, see the [Amazon EC2 documentation](https://docs.aws.amazon.com/ec2)\.
+ We assume that you are familiar with using Amazon Virtual Private Cloud \(Amazon VPC\)\. For more information on how to use Amazon VPC, see the [Amazon VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)\.
+ We assume that you haven't changed the rules on the default security group for your VPC based on the Amazon VPC service\. If you have, make sure that you add the necessary rules to allow network traffic from your Amazon EC2 instance to your Amazon FSx for Lustre file system\. For more details, see [Security](security.md)\.

## Pricing for Amazon FSx for Lustre<a name="pricing"></a>

With Amazon FSx for Lustre, there are no upfront hardware or software costs\. You pay for only the resources used, with no minimum commitments, setup costs, or additional fees\. For information about the pricing and fees associated with the service, see [Amazon FSx for Lustre Pricing](https://aws.amazon.com//fsx/lustre/pricing)\.

## Amazon FSx for Lustre Forums<a name="fsx-forums"></a>

If you encounter issues while using Amazon FSx for Lustre use the [forums](https://forums.aws.amazon.com/forum.jspa?forumID=311)\.

## Are You a First\-Time User of Amazon FSx for Lustre?<a name="first-time-user"></a>

If you are a first\-time user of Amazon FSx for Lustre, we recommend that you read the following sections in order:

1. If you're ready to create your first Amazon FSx for Lustre file system, try the [Getting Started with Amazon FSx for Lustre](getting-started.md)\.

1. For information on performance, see [Amazon FSx for Lustre Performance](performance.md)\.

1. For information on linking your file system to an Amazon S3 bucket data repository, see [Using Data Repositories](fsx-data-repositories.md)\.

1. For Amazon FSx for Lustre security details, see [Security](security.md)\.

1. For information on the scalability limits of Amazon FSx for Lustre, including throughput and file system size, see [Limits](limits.md)\.

1. For information on the Amazon FSx for Lustre API, see the [Amazon FSx for Lustre API Reference](https://docs.aws.amazon.com/fsx/latest/APIReference/Welcome.html)\.