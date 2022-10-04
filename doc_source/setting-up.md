# Setting up<a name="setting-up"></a>

Before you use Amazon FSx for Lustre for the first time, complete the following tasks:

1. [Sign up for AWS](#setting-up-signup)

1. [Create an IAM user](#setting-up-iam)

## Sign up for AWS<a name="setting-up-signup"></a>

When you sign up for Amazon Web Services, your AWS account is automatically signed up for all services in AWS, including Amazon FSx for Lustre\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html#aws_tasks-that-require-root)\.

Note your AWS account number, because you need it for the next task\.

## Create an IAM user<a name="setting-up-iam"></a>

Services in AWS, such as Amazon FSx for Lustre, require that you provide credentials when you access them, so that the service can determine whether you have permissions to access its resources\. AWS recommends that you don't use the root credentials of your AWS account to make requests\. Instead, create an AWS Identity and Access Management \(IAM\) user and grant that user full access\. We call these users administrator users\.

You can use the administrator user credentials, instead of root credentials of your account, to interact with AWS and perform tasks, such as create users and grant them permissions\. For more information, see [Root Account Credentials vs\. IAM User Credentials](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html) in the *AWS General Reference* and [IAM Best](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\. 

If you signed up for AWS but have not created an IAM user for yourself, you can create one using the IAM Management Console\.

To create an administrator user, choose one of the following options\.


****  

| Choose one way to manage your administrator | To | By | You can also | 
| --- | --- | --- | --- | 
| In IAM Identity Center \(Recommended\) | Use short\-term credentials to access AWS\.This aligns with the security best practices\. For information about best practices, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#bp-users-federation-idp) in the *IAM User Guide*\. | Following the instructions in [Getting started](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html) in the AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide\. | Configure programmatic access by [Configuring the AWS CLI to use AWS IAM Identity Center \(successor to AWS Single Sign\-On\)](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html) in the AWS Command Line Interface User Guide\. | 
| In IAM \(Not recommended\) | Use long\-term credentials to access AWS\. | Following the instructions in [Creating your first IAM admin user and user group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the IAM User Guide\. | Configure programmatic access by [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the IAM User Guide\. | 

To sign in as this new IAM user, first sign out of the AWS Management Console\. Then use the following URL, where *your\_aws\_account\_id* is your AWS account number without the hyphens \(for example, if your AWS account number is `1234-5678-9012`, your AWS account ID is `123456789012`\)\.

```
https://your_aws_account_id.signin.aws.amazon.com/console/
```

Enter the IAM user name and password that you just created\. When you're signed in, the navigation bar displays ***your\_user\_name*****@*****your\_aws\_account\_id***\.

If you don't want the URL for your sign\-in page to contain your AWS account ID, you can create an account alias\. To do so, from the IAM dashboard, choose **Create Account Alias** and enter an alias, such as your company name\. To sign in after you create an account alias, use the following URL\.

```
https://your_account_alias.signin.aws.amazon.com/console/
```

To verify the sign\-in link for IAM users for your account, open the IAM console and check under **AWS Account Alias** on the dashboard\.

## Adding permissions to use data repositories in Amazon S3<a name="fsx-adding-permissions-s3"></a>

Amazon FSx for Lustre is deeply integrated with Amazon S3\. This integration means that applications that access your FSx for Lustre file system can also seamlessly access the objects stored in your linked Amazon S3 bucket\. For more information, see [Using data repositories with Amazon FSx for Lustre](fsx-data-repositories.md)\.

To use data repositories, you must first allow Amazon FSx for Lustre certain IAM permissions in a role associated with the account for your administrator user\.

**To embed an inline policy for a role using the console**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com//iam/](https://console.aws.amazon.com//iam/)\.

1. In the navigation pane, choose **Roles**\.

1. In the list, choose the name of the role to embed a policy in\.

1. Choose the **Permissions** tab\.

1. Scroll to the bottom of the page and choose **Add inline policy**\.
**Note**  
You can't embed an inline policy in a service\-linked role in IAM\. Because the linked service defines whether you can modify the permissions of the role, you might be able to add additional policies from the service console, API, or AWS CLI\. To view the service\-linked role documentation for a service, see **AWS Services That Work with IAM** and choose **Yes** in the **Service\-Linked Role** column for your service\. 

1. Choose **Creating Policies with the Visual Editor**

1. Add the following permissions policy statement\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": {
           "Effect": "Allow",
           "Action": [
               "iam:CreateServiceLinkedRole",
               "iam:AttachRolePolicy",
               "iam:PutRolePolicy"
           ],
           "Resource": "arn:aws:iam::*:role/aws-service-role/s3.data-source.lustre.fsx.amazonaws.com/*"
       }
   }
   ```

After you create an inline policy, it is automatically embedded in your role\. For more information about service\-linked roles, see [Using service\-linked roles for Amazon FSx for Lustre](using-service-linked-roles.md)\.

## How FSx for Lustre checks for access to linked S3 buckets<a name="fsx-lustre-permissions-s3-bucket"></a>

If the IAM role that you use to create the FSx for Lustre file system does not have the `iam:AttachRolePolicy` and `iam:PutRolePolicy` permissions, then Amazon FSx checks whether it can update your S3 bucket policy\. Amazon FSx can update your bucket policy if the `s3:PutBucketPolicy` permission is included in your IAM role to allow the Amazon FSx file system to import or export data to your S3 bucket\. If allowed to modify the bucket policy, Amazon FSx adds the following permissions to the bucket policy:
+ `s3:AbortMultipartUpload`
+ `s3:DeleteObject`
+ `s3:PutObject`
+ `s3:Get*`
+ `s3:List*`
+ `s3:PutBucketNotification`
+ `s3:PutBucketPolicy`
+ `s3:DeleteBucketPolicy`

If Amazon FSx can't modify the bucket policy, it then checks if the existing bucket policy grants Amazon FSx access to the bucket\.

If all of these options fail, then the request to create the file system fails\. The following diagram illustrates the checks that Amazon FSx follows when determining whether a file system can access the S3 bucket to which it will be linked\. 

![\[Progression of checks that Amazon FSx uses to determine if it will have permission to import or export data to the S3 bucket to which it will be linked.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/fsx-lustre-permissons-create-fs-linked-s3.png)

## Next step<a name="setting-up-next-step"></a>

[Getting started with Amazon FSx for Lustre](getting-started.md)