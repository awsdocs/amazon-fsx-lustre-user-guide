# Linking your file system to an S3 bucket<a name="create-fs-linked-data-repo"></a>

 When you create an Amazon FSx for Lustre file system, you can link it to a durable data repository in Amazon S3\. Before you create your file system, make sure that you have already created the S3 bucket that you will link to\. In the **Create file system** wizard, you set the following data repository configuration properties in the optional **Data repository integration** pane\. 
+ Choose how Amazon FSx keeps your file and directory listing up to date as you add or modify objects in your S3 bucket after the file system is created\. For more information, see [Automatically import updates from your S3 bucket](autoimport-data-repo.md)\. 
+ **Import bucket** – Enter the name of the S3 bucket that you are using for the linked repository\.
+ **Import prefix** – Enter an optional import prefix if you want to import only some of the file and directory listings of data in your S3 bucket into your file system\. The import prefix defines where in your S3 bucket to import data from\.
+ **Export prefix** – Defines where Amazon FSx will export the contents of your file system to your linked S3 bucket\.

You can have a 1:1 mapping where Amazon FSx exports data from your Amazon FSx for Lustre file system back to the same directories on the S3 bucket that it was imported from\. To have a 1:1 mapping, you will need to specify an export path to the S3 bucket without any prefixes when creating your file system\.
+ When creating a file system using the console, choose the **Export prefix** > **A prefix you specify** option and leave the prefix field blank\. 
+ When creating a file system using the AWS CLI or API, specify the export path as the name of the S3 bucket without any additional prefixes, for example,`ExportPath=s3://lustre-export-test-bucket/`\.

Using this method, you can include an Import prefix when specifying the Import path, and it will not impact a 1:1 mapping for exports\.

## Creating file systems linked to an S3 bucket<a name="create-linked-fs"></a>

The following procedures walk you through the process of creating an Amazon FSx file system linked to an S3 bucket using the AWS Management Console and AWS Command Line Interface \(AWS CLI\)\.

### To create a file system linked to an S3 bucket \(console\)<a name="export-path-lustre-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. From the dashboard, choose **Create file system**\.

1. For the file system type, choose **Amazon FSx for Lustre**, and then choose **Next**\.

1. Provide the information required for the **File system details** and **Network and security** sections\. For more information, see [Step 1: Create Your Amazon FSx for Lustre File System](getting-started-step1.md)\.

1. You use the **Data repository import/export** panel to configure a linked data repository in Amazon S3\.

    Select **Import data from and export data to S3** to expand the **Data repository integration** section and configure the data repository settings\.  
![\[The Data repository import and export configuration panel in the Create Lustre file system page.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/Data-repository-int.png)

1. Choose how Amazon FSx keeps your file and directory listing up to date as you add or modify objects in your S3 bucket\. When you create your file system, your existing S3 objects appear as file and directory listings\. After you create your file system, how do you want to update it as the contents of your S3 bucket are updated?
   + **Update my file and directory listing as objects are added to my S3 bucket** \- \(Default\) Amazon FSx automatically updates file and directory listings of any new objects added to the linked S3 bucket that do not currently exist in the FSx file system\. Amazon FSx does not import update listings for objects that have changed in the S3 bucket\. Amazon FSx does not delete listings of objects that are deleted in the S3 bucket\.
**Note**  
The default import preferences setting for importing data from a linked S3 bucket using the CLI and API is `NONE`, which is different from the default behavior when using the console\.
   + **Update my file and directory listing as objects are added to or changed in my S3 bucket** \- Amazon FSx automatically updates file and directory listings of any new objects added to the S3 bucket and any existing objects that are changed in the S3 bucket after you choose this option\. Amazon FSx does not delete listings of objects that are deleted in the S3 bucket\. 
   + **Do not update my file and directly listing when objects are added to or changed in my S3 bucket** \- Amazon FSx only updates file and directory listing from the linked S3 bucket when the file system is created\. FSx does not update file and directory listings for any new or changed objects after choosing this option\.

1. Enter an optional **Import prefix** if you want to import only some of the file and directory listings of data in your S3 bucket into your file system\. The import prefix defines where in your S3 bucket to import data from\. For more information, see [Automatically import updates from your S3 bucket](autoimport-data-repo.md)\.

1. Choose one of the three **Export prefix** options:
   + **A unique prefix that Amazon FSx creates in your bucket** – Choose this option to export new and changed objects using a prefix generated by Amazon FSx for Lustre\. The prefix looks like the following: `/FSxLustrefile-system-creation-timestamp`\. The timestamp is in UTC format, for example `FSxLustre20181105T222312Z`\. 
   + **The same prefix that you imported from \(replace existing objects with updated ones\)** – Choose this option to replace existing objects with updated ones\.
   + **A prefix you specify** – Choose this option to preserve your imported data and to export new and changed objects using a prefix that you specify\. To achieve a 1:1 mapping when exporting data to your S3 bucket, choose this option and leave the prefix field blank\. FSx will export data to same directories it was imported from\.

1. \(Optional\) Set **Maintenance preferences**, or use the system defaults\.

1. Choose **Next**, and review the file system settings\. Make any changes if needed\.

1. Choose **Create file system**\.

### To create a file system linked to an S3 bucket \(AWS CLI\)<a name="export-path-lustre-cli"></a>

The following example creates an Amazon FSx file system linked to the `lustre-export-test-bucket`, with an import preference that imports any new or changed files in the linked data repository after the file system is created\.

**Note**  
The default import preferences setting for importing data from a linked S3 bucket using the CLI and API is `NONE`, which is different from the default behavior when using the console\.
+ To create an Amazon FSx for Lustre file system, use the Amazon FSx CLI command [https://docs.aws.amazon.com/cli/latest/reference/fsx/create-file-system.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/create-file-system.html), as shown following\. The corresponding API operation is [CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html)\.

  ```
  $ aws fsx create-file-system \
        --client-request-token CRT1234 \
        --file-system-type LUSTRE \
        --lustre-configuration AutoImportPolicy=NEW_CHANGED,DeploymentType=PERSISTENT_1,ImportPath=s3://lustre-export-test-bucket/,ExportPath=s3://lustre-export-test-bucket/export \
        --storage-capacity 3600 \
        --subnet-ids subnet-123456 \
        --tags Key=Name,Value=Lustre-TEST-1 \
        --region us-east-2
  ```

After successfully creating the file system, Amazon FSx returns the file system description as JSON, as shown in the following example\.

```
{

    "FileSystems": [
        {
            "OwnerId": "owner-id-string",
            "CreationTime": 1549310341.483,
            "FileSystemId": "fs-0123456789abcdef0",
            "FileSystemType": "LUSTRE",
            "Lifecycle": "CREATING",
            "StorageCapacity": 3600,
            "VpcId": "vpc-123456",
            "SubnetIds": [
                "subnet-123456"
            ],
            "NetworkInterfaceIds": [
                "eni-039fcf55123456789"
            ],
            "DNSName": "fs-0123456789abcdef0.fsx.us-east-2.amazonaws.com",
            "ResourceARN": "arn:aws:fsx:us-east-2:123456:file-system/fs-0123456789abcdef0",
            "Tags": [
                {
                    "Key": "Name",
                    "Value": "Lustre-TEST-1"
                }
            ],
            "LustreConfiguration": {
                "DeploymentType": "PERSISTENT_1",
                "DataRepositoryConfiguration": {
                    "AutoImportPolicy": "NEW_CHANGED",
                    "Lifecycle": "UPDATING",
                    "ImportPath": "s3://lustre-export-test-bucket/",
                    "ExportPath": "s3://lustre-export-test-bucket/export",
                    "ImportedFileChunkSize": 1024
                }
            }
        }
    ]
}
```

## Working with server\-side encrypted Amazon S3 buckets<a name="s3-server-side-encryption-support"></a>

 Amazon FSx for Lustre supports Amazon S3 buckets that use server\-side encryption with S3\-managed keys \(SSE\-S3\), and with customer master keys \(CMKs\) stored in AWS Key Management Service \(SSE\-KMS\)\. 

If you want Amazon FSx to encrypt data when writing to your S3 bucket, you need to set the default encryption on your S3 bucket to either SSE\-S3 or SSE\-KMS\. For more information, see [ How Do I Enable Default Encryption for an Amazon S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/default-bucket-encryption.html) in the *Amazon Simple Storage Service Console User Guide*\. When writing files to your S3 bucket, Amazon FSx follows the default encryption policy of your S3 bucket\.

By default, Amazon FSx supports S3 buckets encrypted using SSE\-S3\. If you want to link your Amazon FSx file system to an S3 bucket encrypted using SSE\-KMS encryption, you need to add a statement to your customer managed KMS key policy that allows Amazon FSx to encrypt and decrypt objects in your S3 bucket using your CMK\.

The following statement allows a specific Amazon FSx file system to encrypt and decrypt objects for a specific S3 bucket, *bucket\_name*\.

```
{
    "Sid": "Allow access through S3 for the FSx SLR to use the KMS CMK on the objects in the given S3 bucket",
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
    "Sid": "Allow access through S3 for the FSx SLR to use the KMS CMK on the objects in the given S3 bucket",
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

## Viewing a file system's export path<a name="view-export-path"></a>

You can view a file system's export path using the Amazon FSx for Lustre console, the AWS CLI, and the API\.

### To view a file system's export path \(console\)<a name="view-export-path-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Choose **File system name** or **File system ID** for the Amazon FSx for Lustre file system that you want to view the export path for\. The file system details page appears for that file system\. 

1. Choose the **Data repository** tab\. The **Data repository integration** panel appears, showing the import and export paths\.  
![\[Amazon FSx Lustre file system data repository integration panel.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/show_exprt_path.png)

### To view a file system's export path \(CLI\)<a name="view-export-path-cli"></a>
+ To determine the export path for your file system, use the [https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) AWS CLI command\.

  ```
  aws fsx describe-file-systems
  ```

  Look for the `ExportPath` property under `LustreConfiguration` in the response\.

  ```
  {
      "OwnerId": "111122223333",
      "CreationTime": 1563382847.014,
      "FileSystemId": "",
      "FileSystemType": "LUSTRE",
      "Lifecycle": "AVAILABLE",
      "StorageCapacity": 3600,
      "VpcId": "vpc-6296a00a",
      "SubnetIds": [
          "subnet-1111111"
      ],
      "NetworkInterfaceIds": [
          "eni-0c288d5b8cc06c82d",
          "eni-0f38b702442c6918c"
      ],
      "DNSName": "fs-0123456789abcdef0.fsx.us-east-2.amazonaws.com",
      "ResourceARN": "arn:aws:fsx:us-east-2:267731178466:file-system/fs-0123456789abcdef0",
      "Tags": [
          {
              "Key": "Name",
              "Value": "Lustre System"
          }
      ],
      "LustreConfiguration": {
          "WeeklyMaintenanceStartTime": "6:09:30",
          "DataRepositoryConfiguration": {
              "AutoImportPolicy": "NEW_CHANGED",
              "Lifecycle": "AVAILABLE",
              "ImportPath": "s3://lustre-export-test-bucket/",
      =======>"ExportPath": "s3://lustre-export-test-bucket/FSxLustre20190717T164753Z",
              "ImportedFileChunkSize": 1024
          }
      }
  },
  ```