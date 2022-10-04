# Trying to create an FSx for Lustre file system fails<a name="cannot-create-fs"></a>

There are a number of potential causes when a file system creation request fails, as described in the following topics\.

## Cannot create a file system because of misconfigured security group<a name="fs-create-fails-port988"></a>

Creating an FSx for Lustre file system fails with the following error message:

```
The file system cannot be created because the default security group in the subnet provided
or the provided security groups do not permit Lustre LNET network traffic on port 988
```

**Action to take**

Make sure that the VPC security group you are using for the creation operation is configured as described in [File System Access Control with Amazon VPC](limit-access-security-groups.md)\. You must set up the security group to allow inbound traffic on ports 988 and 1021\-1023 from the security group itself or the full subnet CIDR, which is required to allow the file system hosts to communicate with each other\.

## Cannot create a file system that is linked to an S3 bucket<a name="slr-permissions-fails"></a>

If creating a new file system that is linked to an S3 bucket fails with an error message similar to the following\.

```
User: arn:aws:iam::012345678901:user/username is not authorized to perform: iam:PutRolePolicy on resource: resource ARN
```

This error can happen if you try to create a file system linked to an Amazon S3 bucket without the necessary IAM permissions\. The required IAM permissions support the Amazon FSx for Lustre service\-linked role that is used to access the specified Amazon S3 bucket on your behalf\.

**Action to take**

Ensure that your IAM entity \(user, group, or role\) has the appropriate permissions to create file systems\. Doing this includes adding the permissions policy that supports the Amazon FSx for Lustre service\-linked role\. For more information, see [Adding permissions to use data repositories in Amazon S3](setting-up.md#fsx-adding-permissions-s3)\.

For more information about service\-linked roles, see [Using service\-linked roles for Amazon FSx for Lustre](using-service-linked-roles.md)\.