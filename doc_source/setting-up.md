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

Note your AWS account number, because you need it for the next task\.

## Create an IAM user<a name="setting-up-iam"></a>

Services in AWS, such as Amazon FSx for Lustre, require that you provide credentials when you access them, so that the service can determine whether you have permissions to access its resources\. AWS recommends that you don't use the root credentials of your AWS account to make requests\. Instead, create an AWS Identity and Access Management \(IAM\) user and grant that user full access\. We call these users administrator users\.

You can use the administrator user credentials, instead of root credentials of your account, to interact with AWS and perform tasks, such as create users and grant them permissions\. For more information, see [Root Account Credentials vs\. IAM User Credentials](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html) in the *AWS General Reference* and [IAM Best](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\. 

If you signed up for AWS but have not created an IAM user for yourself, you can create one using the IAM Management Console\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user that follows and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add user**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \- job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

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