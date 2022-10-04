# Unable to validate access to an S3 bucket when creating a data repository association<a name="s3-validation-error"></a>

Creating a data repository association \(DRA\) from the Amazon FSx console or using the `create-data-repository-association` CLI command \([CreateDataRepositoryAssociation](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateDataRepositoryAssociation.html) is the equivalent API action\) fails with the following error message\.

```
Amazon FSx is unable to validate access to the S3 bucket. Ensure the IAM role or user
you are using has s3:Get*, s3:List* and s3:PutObject permissions to the S3 bucket prefix.
```

**Note**  
You can also get the above error when creating a Scratch 1, Scratch 2, or Persistent 1 file system that is linked to a data repository \(S3 bucket or prefix\) using the Amazon FSx console or the `create-file-system` CLI command \([CreateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateFileSystem.html) is the equivalent API action\)\.

**Action to take**

If the FSx for Lustre file system is in the same account as the S3 bucket, this error means the IAM role you used for the create request doesn't have the necessary permissions to access the S3 bucket\. Make sure the IAM role has the permissions listed in the error message\. These permissions support the Amazon FSx for Lustre service\-linked role that is used to access the specified Amazon S3 bucket on your behalf\.

If the FSx for Lustre file system is in a different account as the S3 bucket \(cross\-account case\), in additional to making sure the IAM role you used has the required permissions, the S3 bucket policy should be configured to allow the access from the account that the FSx for Lustre is created in\. Following is a sample bucket policy,

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::file_system_account_ID:root"
            },
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::bucket_name",
                "arn:aws:s3:::bucket_name/*"
            ]
        }
    ]
}
```

For more information about S3 cross\-account bucket permissions, see [Example 2: Bucket owner granting cross\-account bucket permissions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-walkthroughs-managing-access-example2.html) in the *Amazon Simple Storage Service User Guide*\.