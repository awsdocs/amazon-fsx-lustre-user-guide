# Managing access to Amazon FSx resources<a name="access-control-manage-access-intro"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of Amazon FSx for Lustre\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What Is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as *identity\-based* policies \(IAM policies\) and policies attached to a resource are referred to as *resource\-based* policies\. Amazon FSx for Lustre supports only identity\-based policies \(IAM policies\)\. 

## Amazon FSx for Lustre API permissions: actions, resources, and conditions reference<a name="fsx-api-permissions-ref"></a>

When you are setting up access control and writing a permissions policy that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The table includes each Amazon FSx for Lustre API operation, the corresponding actions for which you can grant permissions to perform the action, and the AWS resource for which you can grant the permissions\. You specify the actions in the policy's `Action` field, and you specify the resource value in the policy's `Resource` field\. 

You can use AWS\-wide condition keys in your Amazon FSx for Lustre policies to express conditions\. For a complete list of AWS\-wide keys, see [Available Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. 

To specify an action, use the `fsx:` prefix followed by the API operation name \(for example, `fsx:CreateFileSystem`\)\. Each action applies to either a single Amazon FSx for Lustre file system, to all Amazon FSx for Lustre file systems owned by an AWS account, to a single backup, or to all backups owned by an AWS account\.

This section only includes the Amazon FSx permissions required for these actions\. Additional permissions from othe AWS services are required for some of these actions\.


**Amazon FSx for Lustre API and required permissions for actions**  

| Amazon FSx for Lustre API operation | Required permissions \(API actions\) | Resource | 
| --- | --- | --- | 
|  [CancelDataRepositoryTask](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CancelDataRepositoryTask.html)  |  `fsx:CancelDataRepositoryTask`  |  `arn:aws:fsx:region:account-id:file-system/file-system-id`  | 
| [CopyBackup](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CopyBackup.html) |  `fsx:CopyBackup` `fsx:CopyBackup` `fsx:TagResource`  |  `arn:aws:fsx:region:account-id:backup/source-backup-id` – the source backup `arn:aws:fsx:region:account-id:backup/*` – the destination region `arn:aws:fsx:region:account-id:backup/*` – required to copy or create tags on the backup copy  | 
| [CreateBackup](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateBackup.html) | fsx:CreateBackup fsx:CreateBackup `fsx:TagResource`  |  `arn:aws:fsx:region:account-id:backup/*` `arn:aws:fsx:region:account-id:file-system/file-system-id` `arn:aws:fsx:region:account-id:backup/*` – required to create tags on the new backup  | 
| [CreateDataRepositoryTask](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateDataRepositoryTask) |  `fsx:CreateDataRepositoryTask` `fsx:CreateDataRepositoryTask` `fsx:TagResource`  |  `arn:aws:fsx:region:account-id:file-system/file-system-id` `arn:aws:fsx:region:account-id:task/*` `arn:aws:fsx:region:account-id:task/*` – required to create tags on the task  | 
|   [CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html)   |  `fsx:CreateFileSystem` `fsx:TagResource`  |  `arn:aws:fsx:region:account-id:file-system/*` `arn:aws:fsx:region:account-id:file-system/*` – to create tags on the file system  | 
|  [CreateFileSystemFromBackup](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystemFromBackup.html)  |  `fsx:CreateFileSystemFromBackup` `fsx:CreateFileSystemFromBackup` `fsx:TagResource`  |  `arn:aws:fsx:region:account-id:file-system/*` `arn:aws:fsx:region:account-id:backup/*` `arn:aws:fsx:region:account-id:file-system/*` – to create tags on the file system  | 
| [DeleteBackup](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DeleteBackup.html) | `fsx:DeleteBackup ` | `arn:aws:fsx:region:account-id:backup/backup-id`  | 
| [DeleteFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DeleteFileSystem.html) |  `fsx:DeleteFileSystem` `fsx:TagResource` `fsx:CreateBackup`  |  `arn:aws:fsx:region:account-id:file-system/filesystem-id` `arn:aws:fsx:region:account-id:backup/*` – required to create tags on a final backup if created `arn:aws:fsx:region:account-id:backup/*` – For Lustre file systems, required to create a final backup\.  | 
| [DescribeBackups](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeBackups.html) | fsx:DescribeBackups | `arn:aws:fsx:region:account-id:backup/*` | 
| [DescribeDataRepositoryTasks](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeDataRepositoryTasks.html) | fsx:DescribeDataRepositoryTasks | `arn:aws:fsx:region:account-id:task/*` | 
| [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) | fsx:DescribeFileSystems | `arn:aws:fsx:region:account-id:file-system/*` | 
| [ListTagsForResource](https://docs.aws.amazon.com/fsx/latest/APIReference/API_ListTagsForResource.html) | fsx:ListTagsForResource |  `arn:aws:fsx:region:account-id:backup/backup-id` `arn:aws:fsx:region:account-id:file-system/filesystem-id` `arn:aws:fsx:region:account-id:task/task-id`  | 
| [TagResource](https://docs.aws.amazon.com/fsx/latest/APIReference/API_TagResource.html) | fsx:TagResource |  `arn:aws:fsx:region:account-id:backup/backup-id` `arn:aws:fsx:region:account-id:file-system/filesystem-id` `arn:aws:fsx:region:account-id:task/task-id`  | 
| [UntagResource](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UntagResource.html) | `fsx:UntagResource` |  `arn:aws:fsx:region:account-id:backup/backup-id` `arn:aws:fsx:region:account-id:file-system/filesystem-id` `arn:aws:fsx:region:account-id:task/task-id`  | 
| [UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) | `fsx:UpdateFileSystem` |  `arn:aws:fsx:region:account-id:file-system/filesystem-id`  | 