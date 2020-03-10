# Working with Server\-Side Encrypted Amazon S3 Buckets<a name="s3-server-side-encryption-support"></a>

 Amazon FSx for Lustre supports Amazon S3 buckets that use server\-side encryption with S3\-managed keys \(SSE\-S3\), and with Customer Master Keys \(CMKs\) Stored in AWS Key Management Service \(SSE\-KMS\)\. 

If you want Amazon FSx to encrypt data when writing to your S3 bucket, you need to set the default encryption on your S3 bucket to either SSE\-S3 or SSE\-KMS\. For more information, see [ How Do I Enable Default Encryption for an Amazon S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/default-bucket-encryption.html) in the *Amazon Simple Storage Service Console User Guide*\. When writing files to your S3 bucket, Amazon FSx will follow the default encryption policy of your S3 bucket\.

By default, Amazon FSx supports S3 buckets encrypted using SSE\-S3\. If you want to link your Amazon FSx file system to an S3 bucket encrypted using SSE\-KMS encryption, you need to add a statement to your customer\-managed KMS key policy that allows Amazon FSx to encrypt and decrypt objects in your S3 bucket using your Customer Master Key \(CMK\)\.

The following statement allows a specific Amazon FSx file system to encrypt and decrypt objects for a specific S3 bucket, *bucket\-name*\.

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