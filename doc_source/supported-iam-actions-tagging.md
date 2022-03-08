# Grant permission to tag resources during creation<a name="supported-iam-actions-tagging"></a>

Some resource\-creating Amazon FSx for Lustre API actions enable you to specify tags when you create the resource\. You can use resource tags to implement attribute\-based access control \(ABAC\)\. For more information,  [ What is ABAC for AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_attribute-based-access-control.html) in the *IAM User Guide*\.

To enable users to tag resources on creation, they must have permissions to use the action that creates the resource, such as `fsx:CreateFileSystem` or `fsx:CreateBackup`\. If tags are specified in the resource\-creating action, Amazon performs additional authorization on the `fsx:TagResource` action to verify if users have permissions to create tags\. Therefore, users must also have explicit permissions to use the `fsx:TagResource` action\.

The following example demonstrates a policy that allows users to create file systems and apply any tags to file systems during creation in a specific AWS account\.

```
{
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
         "fsx:CreateFileSystem",
         "fsx:TagResource"         
      ],
      "Resource": "arn:aws:fsx:region:account-id:file-system/*"
    }
  ]
}
```

Similarly, the following policy allows users to create backups for a specific file system resource and apply any tags to the backup during backup creation\.

```
{
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
         "fsx:CreateBackup"
      ],
      "Resource": "arn:aws:fsx:region:account-id:file-system/file-system-id"
    },
    {
      "Effect": "Allow",
      "Action": [
         "fsx:TagResource"
      ],
      "Resource": "arn:aws:fsx:region:account-id:backup/*"
    }
  ]
}
```

The `fsx:TagResource` action is only evaluated if tags are applied during the resource\-creating action\. Therefore, a user that has permissions to create a resource \(assuming there are no tagging conditions\) does not require permissions to use the `fsx:TagResource` action if no tags are specified in the request\. However, if the user attempts to create a resource with tags, the request fails if the user does not have permissions to use the `fsx:TagResource` action\.

Users require the `fsx:CreateBackup` permission when deleting an FSx for Lustre file system using the `DeleteFileSystem` API action or CLI command, and `SkipFinalBackup` is set to false\. Otherwise, the action will fail because the user was not allowed to create a final backup before the file system is deleted\.

For more information about tagging Amazon FSx resources, see [Tag your Amazon FSx resources](tag-resources.md)\. For more information about using tags to control access to FSx resources, see [Using tags to control access to your Amazon FSx resources](restrict-fsx-access-tags.md)\.