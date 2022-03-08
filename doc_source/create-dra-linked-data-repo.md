# Linking your file system to an S3 bucket<a name="create-dra-linked-data-repo"></a>

You can link your Amazon FSx for Lustre file system to data repositories in Amazon S3\. You can create the link when creating the file system or at any time after the file system has been created\.

A link between a directory on the file system and an S3 bucket or prefix is called a *data repository association \(DRA\)*\. You can configure a maximum of 8 data repository associations on an Amazon FSx file system\. A maximum of 8 DRA requests can be queued, but only one request can be worked on at a time for the file system\. Each DRA must have a unique Amazon FSx file system directory and a unique S3 bucket or prefix associated with it\.

**Note**  
Data repository associations, automatic export, and multiple data repositories are only supported on `Persistent 2` file systems\. If you're using a file system with an older FSx for Lustre deployment type, see [Working with older deployment types](older-deployment-types.md)\.

In order to access objects on the S3 data repository as files and directories on the file system, file and directory metadata must be loaded into the file system\. You can load metadata from a linked data repository when you create the DRA or load metadata for batches of files and directories that you want to access using the FSx for Lustre file system at a later time using an import data repository task\.

You can configure a DRA for automatic import only, for automatic export only, or for both\. A data repository association configured with both automatic import and automatic export propagates data in both directions between the file system and the linked S3 bucket\. As you make changes to data in your S3 bucket, Amazon FSx detects the changes and then automatically imports the changes to your file system\. As you create, modify, or delete files, Amazon FSx automatically exports the changes to Amazon S3 asynchronously once your application finishes modifying the file\.

**Note**  
You should not modify the same file on both the S3 bucket and the Lustre file system at the same time, otherwise the behavior is undefined\.

When you create a data repository association, you can configure the following properties:
+ **File system path** – Enter a local path on the file system that points to a directory \(such as `/ns1/`\) or subdirectory \(such as `/ns1/subdir/`\) that will be mapped one\-to\-one with the specified data repository path below\. The leading forward slash in the name is required\. Two data repository associations cannot have overlapping file system paths\. For example, if a data repository is associated with file system path `/ns1`, then you cannot link another data repository with file system path `/ns1/ns2`\.
**Note**  
If you specify only a forward slash \(`/`\) as the file system path, you can link only one data repository to the file system\. You can only specify "/" as the file system path for the first data repository associated with a file system\.
+ **Data repository path** – Enter a path in the S3 data repository\. The path can be an S3 bucket or prefix in the format `s3://myBucket/myPrefix/`\. This property specifies where in the S3 data repository files will be imported from or exported to\. FSx for Lustre will append a trailing "/" to your data repository path if you don't provide one\. For example, if you provide a data repository path of `s3://myBucket/myPrefix`, FSx for Lustre will interpret it as `s3://myBucket/myPrefix/`\.

  Two data repository associations cannot have overlapping data repository paths\. For example, if a data repository with path `s3://myBucket/myPrefix/` is linked to the file system, then you cannot create another data repository association with data repository path `s3://myBucket/myPrefix/mySubPrefix`\.
+ **Import metadata from repository** – You can select this option to import metadata from the entire data repository immediately after creating the data repository association\. Alternatively, you can run an import data repository task to load all or a subset of the metadata from the linked data repository into the file system at any time after the data repository association is created\.
+ **Import settings** – Choose an import policy that specifies the type of updated objects \(any combination of new, changed, and deleted\) that will be automatically imported from the linked S3 bucket to your file system\. Automatic import \(new, changed, deleted\) is turned on by default when you add a data repository from the console, but is disabled by default when using the AWS CLI or Amazon FSx API\.
+ **Export settings** – Choose an export policy that specifies the type of updated objects \(any combination of new, changed, and deleted\) that will be automatically exported to the S3 bucket\. Automatic export \(new, changed, deleted\) is turned on by default when you add a data repository from the console, but is disabled by default when using the AWS CLI or Amazon FSx API\.

The **File system path** and **Data repository path** settings provide a 1:1 mapping where Amazon FSx exports data from your FSx for Lustre file system back to the corresponding prefixes on the S3 bucket that it was imported from\.

## Region and account support for linked S3 buckets<a name="dra-regional-support"></a>

When you create links to S3 buckets, keep in mind the following Region and account support limitations:
+ Automatic export supports cross\-Region configurations\. The Amazon FSx file system and the linked S3 bucket can be located in the same AWS Region or in different AWS Regions\.
+ Automatic import does not support cross\-Region configurations\. Both the Amazon FSx file system and the linked S3 bucket must be located in the same AWS Region\.
+ Both automatic export and automatic import support cross\-Account configurations\. The Amazon FSx file system and the linked S3 bucket can belong to the same AWS account or to different AWS accounts\.

## Creating a link to an S3 bucket<a name="create-linked-dra"></a>

The following procedures walk you through the process of creating a data repository association for a `Persistent 2` file system to an existing S3 bucket, using the AWS Management Console and AWS Command Line Interface \(AWS CLI\)\.

**Note**  
Data repositories cannot be linked to file systems that have file system backups enabled\. Disable backups before linking to a data repository\.

### To link an S3 bucket while creating a file system \(console\)<a name="export-path-lustre-console-dra-new"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Follow the procedure for creating a new file system described in [Step 1: Create your Amazon FSx for Lustre file system](getting-started-step1.md) in the Getting Started section\. 

1. Open the **Data Repository Import/Export \- *optional*** section\. The feature is disabled by default\.

1. Choose **Import data from and export data to S3**\.

1. In the **Data repository association information** dialog, provide information for the following fields\.
   + **File system path**: Enter the name of a high\-level directory \(such as `/ns1`\) or subdirectory \(such as `/ns1/subdir`\) within the Amazon FSx file system that will be associated with the S3 data repository\. The leading forward slash in the path is required\. Two data repository associations cannot have overlapping file system paths\. For example, if a data repository is associated with file system path `/ns1`, then you cannot link another data repository with file system path `/ns1/ns2`\. The **File system path** setting must be unique across all the data repository associations for the file system\.
   + **Data repository path**: Enter the path of an existing S3 bucket or prefix to associate with your file system \(for example, `s3://my-bucket/my-prefix/`\)\. Two data repository associations cannot have overlapping data repository paths\. For example, if a data repository with the path `s3://myBucket/myPrefix/` is linked to the file system, then you cannot create another data repository association with the data repository path `s3://myBucket/myPrefix/mySubPrefix`\. The **Data repository path** setting must be unique across all the data repository associations for the file system\.
   + **Import metadata from repository**: Select this property to optionally run an import data repository task to import metadata immediately after the link is created\.  
![\[The Data Repository Associations configuration dialog, which is one of the dialogs to configure export and import links for an S3 data repository.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/create-fs-dra-info.png)

1. For **Import settings \- optional**, set an **Import Policy** that determines how your file and directory listings are kept up to date as you add, change, or delete objects in your S3 bucket\. For example, choose **New** to import metadata to your file system for new objects created in the S3 bucket\. For more information on import policies, see [Automatically import updates from your S3 bucket](autoimport-data-repo-dra.md)\.  
![\[The Import Settings configuration dialog, which you use to set the import policy for automatic import.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/create-fs-dra-import.png)

1. For **Export policy**, set an export policy that determines how your files are exported to your linked S3 bucket as you add, change, or delete objects in your file system\. For example, choose **Changed** to export objects whose content or metadata has been changed on your file system\. For more information about export policies, see [Automatically export updates to your S3 bucket](autoexport-data-repo-dra.md)\.  
![\[The Export Settings configuration dialog, which you use to set the export policy for automatic export.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/create-fs-dra-export.png)

1. Continue with the next section of the file system creation wizard\.

### To link an S3 bucket to an existing file system \(console\)<a name="export-path-lustre-console-dra"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **File systems** and then select the file system that you want to create a data repository association for\. 

1. Choose the **Data repository** tab\.

1. In the **Data repository associations** pane, choose **Create data repository association**\.

1. In the **Data repository association information** dialog, provide information for the following fields\.
   + **File system path**: Enter the name of a high\-level directory \(such as `/ns1`\) or subdirectory \(such as `/ns1/subdir`\) within the Amazon FSx file system that will be associated with the S3 data repository\. The leading forward slash in the path is required\. Two data repository associations cannot have overlapping file system paths\. For example, if a data repository is associated with file system path `/ns1`, then you cannot link another data repository with file system path `/ns1/ns2`\. The **File system path** setting must be unique across all the data repository associations for the file system\.
   + **Data repository path**: Enter the path of an existing S3 bucket or prefix to associate with your file system \(for example, `s3://my-bucket/my-prefix/`\)\. Two data repository associations cannot have overlapping data repository paths\. For example, if a data repository with path `s3://myBucket/myPrefix/` is linked to the file system, then you cannot create another data repository association with data repository path `s3://myBucket/myPrefix/mySubPrefix`\. The **Data repository path** setting must be unique across all the data repository associations for the file system\.
   + **Import metadata from repository**: Select this property to optionally run an import data repository task to import metadata immediately after the link is created\.  
![\[The Data Repository Associations configuration dialog, which is one of the dialogs to configure export and import links for an S3 data repository.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/dra-create1-information.png)

1. For **Import settings \- optional**, set an **Import Policy** that determines how your file and directory listings are kept up to date as you add, change, or delete objects in your S3 bucket\. For example, choose **New** to import metadata to your file system for new objects created in the S3 bucket\. For more information about import policies, see [Automatically import updates from your S3 bucket](autoimport-data-repo-dra.md)\.  
![\[The Import Settings configuration dialog, which you use to set the import policy for automatic import.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/dra-create2-settings.png)

1. For **Export policy**, set an export policy that determines how your files are exported to your linked S3 bucket as you add, change, or delete objects in your file system\. For example, choose **Changed** to export objects whose content or metadata has been changed on your file system\. For more information about export policies, see [Automatically export updates to your S3 bucket](autoexport-data-repo-dra.md)\.  
![\[The Export Settings configuration dialog, which you use to set the export policy for automatic export.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/dra-create3-policies.png)

1. Choose **Create**\.

### To link a file system to an S3 bucket \(AWS CLI\)<a name="export-path-lustre-cli-dra"></a>

The following example creates a data repository association that links an Amazon FSx file system to an S3 bucket, with an import policy that imports any new or changed files to the file system and an export policy that exports new, changed, or deleted files to the linked S3 bucket\.
+ To create a data repository association, use the Amazon FSx CLI command `create-data-repository-association`, as shown following\.

  ```
  $ aws fsx create-data-repository-association \
        --file-system-id fs-0123456789abcdef0 \
        --file-system-path /ns1/path1/ \
        --data-repository-path s3://mybucket/myprefix/ \
        --s3 "AutoImportPolicy={Events=[NEW,CHANGED,DELETED]},AutoExportPolicy={Events=[NEW,CHANGED,DELETED]}"
  ```

Amazon FSx returns the JSON description of the DRA immediately\. The DRA is created asynchronously\.

You can use this command to create a data repository association even before the file system has finished creating\. The request will be queued and the data repository association will be created after the file system is available\.

### Updating data repository association settings<a name="update-dra-settings"></a>

You can update an existing data repository association's settings using the AWS Management Console, the AWS CLI, and the Amazon FSx API, as shown in the following procedures\.

**Note**  
You cannot update the `File system path` or `Data repository path` of a DRA after it's created\. If you want to change the `File system path` or `Data repository path`, you must delete the DRA and create it again\.

#### To update settings for an existing data repository association \(console\)<a name="update-dra-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **File systems**, and then select the file system that you want to manage\.

1. Choose the **Data repository** tab\.

1. In the **Data repository associations** pane, choose the data repository association you want to change\.

1. Choose **Update**\. An edit dialog displays for the data repository association\.

1. For **Import settings \- optional**, you can update your **Import Policy**\. For more information on import policies, see [Automatically import updates from your S3 bucket](autoimport-data-repo-dra.md)\.

1. For **Export settings \- optional**, you can update our export policy\. For more information on export policies, see [Automatically export updates to your S3 bucket](autoexport-data-repo-dra.md)\.

1. Choose **Update**\.

#### To update settings for an existing data repository association \(CLI\)<a name="update-dra--cli"></a>
+ To update a data repository association, use the Amazon FSx CLI command `update-data-repository-association`, as shown following\.

  ```
  $ aws fsx update-data-repository-association \
        --association-id 'dra-872abab4b4503bfc2' \
        --s3 "AutoImportPolicy={Events=[NEW,CHANGED,DELETED]},AutoExportPolicy={Events=[NEW,CHANGED,DELETED]}"
  ```

After successfully updating the data repository association's export policy, Amazon FSx returns the description of the updated data repository association as JSON\.

### Deleting an association to an S3 bucket<a name="delete-linked-dra"></a>

The following procedures walk you through the process of deleting a data repository association from an existing Amazon FSx file system to an existing S3 bucket, using the AWS Management Console and AWS Command Line Interface \(AWS CLI\)\. Deleting the data repository association unlinks the file system from the S3 bucket\.

#### To delete a link from a file system to an S3 bucket \(console\)<a name="delete-dra-console-dra"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **File systems** and then select the file system that you want to delete a data repository association from\. 

1. Choose the **Data repository** tab\.

1. In the **Data repository associations** pane, choose the data repository association that you want to delete\.

1. For **Actions**, choose **Delete association**\.

1. \(Optional\) In the **Delete** dialog, you can choose **Delete data in file system** to physically delete the data in the file system that corresponds to the data repository association\.

1. Choose **Delete** to remove the data repository association from the file system\.

#### To delete a link from a file system to an S3 bucket \(AWS CLI\)<a name="delete-dra-cli-dra"></a>

The following example deletes a data repository association that links an Amazon FSx file system to an S3 bucket\. The `--association-id` parameter specifies the ID of the data repository association to be deleted\.
+ To delete a data repository association, use the Amazon FSx CLI command `delete-data-repository-association`, as shown following\.

  ```
  $ aws fsx delete-data-repository-association \
        --association-id dra-872abab4b4503bfc \
        --delete-data-in-file-system false
  ```

After successfully deleting the data repository association, Amazon FSx returns its description as JSON\.

### Viewing data repository association details<a name="view-dra-details"></a>

You can view the details of a data repository association using the FSx for Lustre console, the AWS CLI, and the API\. The details include the DRA's association ID, file system path, data repository path, import settings, export settings, status, and the ID of its associated file system\.

#### To view DRA details \(console\)<a name="view-dra-details-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **File systems** and then select the file system that you want to view a data repository association's details for\.

1. Choose the **Data repository** tab\.

1. In the **Data repository associations** pane, choose the data repository association that you want to view\. The **Summary** page appears, showing the DRA details\.  
![\[Amazon FSx Details page of a data repository association.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/dra-describe.png)

#### To view DRA details \(CLI\)<a name="view-dra-details-cli"></a>
+ To view the details of a specific data repository association, use the Amazon FSx CLI command `describe-data-repository-associations`, as shown following\.

  ```
  $ aws fsx describe-data-repository-associations \
        --association-ids dra-872abab4b4503bfc2
  ```

  Amazon FSx returns the description of the data repository association as JSON\.

### Data repository association lifecycle state<a name="dra-lifecycles"></a>

The data repository association lifecycle state provides status information about a specific DRA\. A data repository association can have the following **Lifecycle states**:
+ **Creating** – Amazon FSx is creating the data repository association between the file system and the linked data repository\. The data repository is unavailable\.
+ **Available** – The data repository association is available for use\.
+ **Updating** – The data repository association is undergoing a customer initiated update that might affect its availability\.
+ **Deleting** – The data repository association is undergoing a customer initiated deletion\.
+ **Misconfigured** – Amazon FSx cannot automatically import updates from the S3 bucket or automatically export updates to the S3 bucket until the data repository association configuration is corrected\.
+ **Failed** – The data repository association is in a terminal state that cannot be recovered \(for example, because its file system path is deleted or the S3 bucket is deleted\)\.

 You can view a data repository associations's lifecycle state using the Amazon FSx console, the AWS Command Line Interface, and the Amazon FSx API\. For more information, see [Viewing data repository association details](#view-dra-details)\.

## Working with server\-side encrypted Amazon S3 buckets<a name="s3-server-side-encryption-support"></a>

 FSx for Lustre supports Amazon S3 buckets that use server\-side encryption with S3\-managed keys \(SSE\-S3\), and with AWS KMS keys stored in AWS Key Management Service \(SSE\-KMS\)\. 

If you want Amazon FSx to encrypt data when writing to your S3 bucket, you need to set the default encryption on your S3 bucket to either SSE\-S3 or SSE\-KMS\. For more information, see [Enabling Amazon S3 default bucket encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-bucket-encryption.html) in the *Amazon S3 User Guide*\. When writing files to your S3 bucket, Amazon FSx follows the default encryption policy of your S3 bucket\.

By default, Amazon FSx supports S3 buckets encrypted using SSE\-S3\. If you want to link your Amazon FSx file system to an S3 bucket encrypted using SSE\-KMS encryption, you need to add a statement to your customer managed key policy that allows Amazon FSx to encrypt and decrypt objects in your S3 bucket using your KMS key\.

**Note**  
 If you're using a KMS with a CMK to encrypt your S3 bucket with S3 bucket keys enabled, set the encryption context to the bucket ARN, not the object ARN\.

The following statement allows a specific Amazon FSx file system to encrypt and decrypt objects for a specific S3 bucket, *bucket\_name*\.

```
{
    "Sid": "Allow access through S3 for the FSx SLR to use the KMS key on the objects in the given S3 bucket",
    "Effect": "Allow",
    "Principal": {
        "AWS": "arn:aws:iam::aws_account_id:role/aws-service-role/s3.data-source.lustre.fsx.amazonaws.com/AWSServiceRoleForFSxS3Access_fsx_file_system_id"
    },
    "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
    ],
    "Resource": "*",
    "Condition": {
        "StringEquals": {
            "kms:CallerAccount": "aws_account_id",
            "kms:ViaService": "s3.bucket-region.amazonaws.com"
        },
        "StringLike": {
            "kms:EncryptionContext:aws:s3:arn": "arn:aws:s3:::bucket_name/*"
        }
    }
}
```

The following policy statement allows all Amazon FSx file systems in your account to link to a specific S3 bucket\.

```
{
    "Sid": "Allow access through S3 for the FSx SLR to use the KMS key on the objects in the given S3 bucket",
    "Effect": "Allow",
    "Principal": {
        "AWS": "*"
    },
    "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
    ],
    "Resource": "*",
    "Condition": {
        "StringEquals": {
            "kms:CallerAccount": "aws_account_id",
            "kms:ViaService": "s3.bucket-region.amazonaws.com"
        },
        "StringLike": {
            "aws:userid": "*:FSx",
            "kms:EncryptionContext:aws:s3:arn": "arn:aws:s3:::bucket_name/*"
        }
    }
}
```

### Accessing server\-side encrypted Amazon S3 buckets in a different AWS account<a name="s3-server-side-cross-account-support"></a>

After you create an FSx for Lustre file system linked to an encrypted Amazon S3 bucket, you must then grant the `AWSServiceRoleForFSxS3Access_fs-01234567890` service\-linked role \(SLR\) access to the KMS key used to encrypt the S3 bucket before reading or writing data from the linked S3 bucket\. You can use an IAM role which already has permissions to the KMS key\.

**Note**  
This IAM role must be in the account that the FSx for Lustre file system was created in \(which is the same account as the S3 SLR\), not the account that the KMS key/S3 bucket belong to\.

You use the IAM role to call the following AWS KMS API to create a grant for the S3 SLR so that the SLR gains permission to the S3 objects\. In order to find the ARN associated with your SLR, search your IAM roles using your file system ID as the search string\.

```
$ aws kms create-grant --region fs_account_region \
      --key-id arn:aws:kms:s3_bucket_account_region:s3_bucket_account:key/key_id \
      --grantee-principal arn:aws:iam::fs_account_id:role/aws-service-role/s3.data-source.lustre.fsx.amazonaws.com/AWSServiceRoleForFSxS3Access_file-system-id \
      --operations "Decrypt" "Encrypt" "GenerateDataKey" "GenerateDataKeyWithoutPlaintext" "CreateGrant" "DescribeKey" "ReEncryptFrom" "ReEncryptTo"
```

For more information about service\-linked roles, see [Using service\-linked roles for Amazon FSx for Lustre](using-service-linked-roles.md)\.