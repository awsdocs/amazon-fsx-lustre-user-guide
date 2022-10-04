# Troubleshooting a misconfigured linked S3 bucket<a name="troubleshooting-misconfigured-data-repository"></a>

In some cases, an FSx for Lustre file system's linked S3 bucket might have a misconfigured data repository lifecycle state\. 

**Possible cause**

This error can occur if Amazon FSx does not have the necessary AWS Identity and Access Management \(IAM\) permissions that are required to access the linked data repository\. The required IAM permissions support the Amazon FSx for Lustre service\-linked role that is used to access the specified Amazon S3 bucket on your behalf\.

**Action to take**

1. Ensure that your IAM entity \(user, group, or role\) has the appropriate permissions to create file systems\. Doing this includes adding the permissions policy that supports the Amazon FSx for Lustre service\-linked role\. For more information, see [Adding permissions to use data repositories in Amazon S3](setting-up.md#fsx-adding-permissions-s3)\.

1. Using the Amazon FSx CLI or API, refresh the file system's `AutoImportPolicy` with the `update-file-system` CLI command \([UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) is the equivalent API action\), as follows\. 

   ```
   aws fsx update-file-system \
   --file-system-id fs-0123456789abcdef0 \
   --lustre-configuration AutoImportPolicy=the_existing_AutoImportPolicy
   ```

For more information about service\-linked roles, see [Using service\-linked roles for Amazon FSx for Lustre](using-service-linked-roles.md)\.

**Possible Cause**

This error can occur if the linked Amazon S3 data repository has an existing event notification configuration with event types that overlap with the Amazon FSx event notification configuration \(`s3:ObjectCreated:*`, `s3:ObjectRemoved:*`\)\.

This can also occur if the Amazon FSx event notification configuration on the linked S3 bucket was deleted or modified\.

**Action to take**

1. Remove any existing event notification on the linked S3 bucket that uses either or both of the event types that the FSx event configuration uses, `s3:ObjectCreated:*` and `s3:ObjectRemoved:*`\.

1. Please ensure that there is an S3 Event Notification Configuration in you linked S3 bucket with the name `FSx`, event types `s3:ObjectCreated:*` and `s3:ObjectRemoved:*`, and send to the SNS topic with `ARN:topic_arn_returned_in_API_response`\.

1. Reapply the FSx event notification configuration on the S3 bucket by using the Amazon FSx CLI or API, to refresh the file system's `AutoImportPolicy`\. Do so with the `update-file-system` CLI command \([UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) is the equivalent API action\), as follows\. 

   ```
   aws fsx update-file-system \
   --file-system-id fs-0123456789abcdef0 \
   --lustre-configuration AutoImportPolicy=the_existing_AutoImportPolicy
   ```