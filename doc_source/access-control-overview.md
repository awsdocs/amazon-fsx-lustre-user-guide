# Resource administration access control with IAM for Amazon FSx for Lustre<a name="access-control-overview"></a>

Every AWS resource is owned by an AWS account, and permissions to create or access a resource are governed by permissions policies\. An account administrator can attach permissions policies to AWS Identity and Access Management \(IAM\) identities \(that is, users, groups, and roles\)\. Some services \(such as AWS Lambda\) also support attaching permissions policies to resources\. 

**Note**  
An *account administrator* \(or administrator user\) is a user with administrator privileges\. For more information, see [IAM best practices](https://docs.aws.amazon.com/https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

When granting permissions, you decide who is getting the permissions, the resources they get permissions for, and the specific actions that you want to allow on those resources\.

**Topics**
+ [Amazon FSx for Lustre resources and operations](#access-control-resources)
+ [Understanding resource ownership](#access-control-owner)
+ [Grant permission to tag resources during creation](supported-iam-actions-tagging.md)
+ [Managing access to Amazon FSx resources](access-control-manage-access-intro.md)
+ [Using service\-linked roles for Amazon FSx for Lustre](using-service-linked-roles.md)

## Amazon FSx for Lustre resources and operations<a name="access-control-resources"></a>

In Amazon FSx for Lustre, the primary resource is a *file system*\. FSx for Lustre also supports the backup and data repository task subresources\. You can create data repository tasks only in the context of an existing file system\. You can create backups only in the context of an existing file system, or by copying an existing backup\. These resources and subresources have unique Amazon Resource Names \(ARNs\) associated with them as shown in the following table\.


****  

| Resource Type | ARN Format | 
| --- | --- | 
| File system | arn:aws:fsx:region:account\-id:file\-system/filesystem\-id | 
| Data repository task | arn:aws:fsx:region:account\-id:task/task\-id | 
| Backup | arn:aws:fsx:region:account\-id:backup/backup\-id | 

Amazon FSx for Lustre provides a set of operations to work with Amazon FSx for Lustre resources\. For a list of available operations, see the [Amazon FSx for Lustre API Reference](https://docs.aws.amazon.com/fsx/latest/APIReference/Welcome.html)\.

## Understanding resource ownership<a name="access-control-owner"></a>

The AWS account owns the resources that are created in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the [principal entity](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html) \(that is, the root account, an IAM user, or an IAM role\) that authenticates the resource creation request\. The following examples illustrate how this works:
+ If you use the root account credentials of your AWS account to create a file system, your AWS account is the owner of the resource\. In Amazon FSx for Lustre, the resource is the file system\.
+ If you create an IAM user in your AWS account and grant permissions to create a file system to that user, the user can create a file system\. However, your AWS account, to which the user belongs, owns the file system resource\.
+ If you create an IAM role in your AWS account with permissions to create a file system, anyone who can assume the role can create a file system\. Your AWS account, to which the role belongs, owns the file system resource\.