# Troubleshooting<a name="troubleshooting"></a>

Use the following information to help you resolve issues that you might encounter when working with Amazon FSx for Lustre\.

**Topics**
+ [File system mount fails right away](#mount-fails-right-away)
+ [File system mount hangs and then fails with timeout error](#mount-hangs-fails-timeout)
+ [Automatic mounting fails and the instance is unresponsive](#lustre-automount-fails)
+ [File system mount fails during system boot](#mount-fails-boot-up)
+ [File system mount using DNS name fails](#mount-fails-dns-name)
+ [You can't access your file system](#cant-access-fs)
+ [Unable to validate access to an S3 bucket when creating a data repository association](#s3-validation-error)
+ [Cannot create a file system that is linked to an S3 bucket](#slr-permissions-fails)
+ [Troubleshooting a misconfigured linked S3 bucket](#troubleshooting-misconfigured-data-repository)
+ [Troubleshooting storage issues](#lfs-migrate-ts)

## File system mount fails right away<a name="mount-fails-right-away"></a>

The file system mount command fails right away\. The following code shows an example\.

```
mount.lustre: mount fs-0123456789abcdef0.fsx.us-east-1.aws@tcp:/fsx at /lustre
failed: No such file or directory

Is the MGS specification correct?
Is the filesystem name correct?
```

This error can occur if you aren't using the correct `mountname` value when mounting a persistent or scratch 2 file system by using the mount command\. You can get the `mountname` value from the response of the [https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) AWS CLI command or the [https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API operation\.

## File system mount hangs and then fails with timeout error<a name="mount-hangs-fails-timeout"></a>

The file system mount command hangs for a minute or two, and then fails with a timeout error\. 

The following code shows an example\.

```
sudo mount -t lustre file_system_dns_name@tcp:/mountname /mnt/fsx

[2+ minute wait here]
Connection timed out
```

This error can occur because the security groups for the Amazon EC2 instance or the file system aren't configured properly\.

**Action to take**

Make sure that your security groups for the file system have the inbound rules specified in [Amazon VPC Security Groups](limit-access-security-groups.md#fsx-vpc-security-groups)\.

## Automatic mounting fails and the instance is unresponsive<a name="lustre-automount-fails"></a>



In some cases, automatic mounting might fail for a file system and your Amazon EC2 instance might stop responding\.

This issue can occur if the `_netdev` option wasn't declared\. If `_netdev` is missing, your Amazon EC2 instance can stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\.

**Action to take**  
If this issue occurs, contact AWS Support\.

## File system mount fails during system boot<a name="mount-fails-boot-up"></a>

The file system mount fails during the system boot\. The mounting is automated using `/etc/fstab`\. When the file system is not mounted, the following error is seen in the syslog for the instance booting time frame\.

```
LNetError: 3135:0:(lib-socket.c:583:lnet_sock_listen()) Can't create socket: port 988 already in use
LNetError: 122-1: Can't start acceptor on port 988: port already in use
```

This error can occur when port 988 is not available\. When the instance is configured to mount NFS file systems, it is possible that the NFS mounts will bind its client port to port 988

**Action to take**

You can work around this problem by tuning the NFS client's `noresvport` and `noauto` mount options where possible\.

## File system mount using DNS name fails<a name="mount-fails-dns-name"></a>

Misconfigured Domain Name Service \(DNS\) names can cause file system mount failures, as shown in the following scenarios\.

**Scenario 1:** A file system mount that is using a Domain Name Service \(DNS\) name fails\. The following code shows an example\.

```
sudo mount -t lustre file_system_dns_name@tcp:/mountname /mnt/fsx
mount.lustre: Can't parse NID 
'file_system_dns_name@tcp:/mountname'
```

**Action to take**

Check your virtual private cloud \(VPC\) configuration\. If you are using a custom VPC, make sure that DNS settings are enabled\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the *Amazon VPC User Guide*\. 

To specify a DNS name in the `mount` command, do the following:
+ Ensure that the Amazon EC2 instance is in the same VPC as your Amazon FSx for Lustre file system\.
+ Connect your Amazon EC2 instance inside a VPC configured to use the DNS server provided by Amazon\. For more information, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.
+ Ensure that the Amazon VPC of the connecting Amazon EC2 instance has DNS host names enabled\. For more information, see [Updating DNS Support for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-updating) in the *Amazon VPC User Guide*\.

**Scenario 2:** A file system mount that is using a Domain Name Service \(DNS\) name fails\. The following code shows an example\.

```
mount -t lustre file_system_dns_name@tcp:/mountname /mnt/fsx
mount.lustre: mount file_system_dns_name@tcp:/mountname at /mnt/fsx failed: Input/output error Is the MGS running?
```

**Action to take**

 Make sure that the client's VPC security groups have the correct outbound traffic rules applied\. This recommendation holds true especially if you aren't using the default security group, or if you have modified the default security group\. For more information, see [Amazon VPC Security Groups](limit-access-security-groups.md#fsx-vpc-security-groups)\. 

## You can't access your file system<a name="cant-access-fs"></a>

There are a number of potential causes for being unable to access your file system, each with their own resolution, as follows\.

### The Elastic IP address attached to the file system elastic network interface was deleted<a name="eni-epi-removed"></a>

Amazon FSx doesn't support accessing file systems from the public Internet\. Amazon FSx automatically detaches any Elastic IP address, which is a public IP address reachable from the Internet, that gets attached to a file system's elastic network interface\.

### The file system elastic network interface was modified or deleted<a name="eni-deleted"></a>

You must not modify or delete the file system's elastic network interface\. Modifying or deleting the network interface can cause a permanent loss of connection between your VPC and your file system\. Create a new file system, and do not modify or delete the FSx elastic network interface\. For more information, see [File System Access Control with Amazon VPC](limit-access-security-groups.md)\.

## Unable to validate access to an S3 bucket when creating a data repository association<a name="s3-validation-error"></a>

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

## Cannot create a file system that is linked to an S3 bucket<a name="slr-permissions-fails"></a>

If creating a new file system that is linked to an S3 bucket fails with an error message similar to the following\.

```
User: arn:aws:iam::012345678901:user/username is not authorized to perform: iam:PutRolePolicy on resource: resource ARN
```

This error can happen if you try to create a file system linked to an Amazon S3 bucket without the necessary IAM permissions\. The required IAM permissions support the Amazon FSx for Lustre service\-linked role that is used to access the specified Amazon S3 bucket on your behalf\.

**Action to take**

Ensure that your IAM entity \(user, group, or role\) has the appropriate permissions to create file systems\. Doing this includes adding the permissions policy that supports the Amazon FSx for Lustre service\-linked role\. For more information, see [Adding permissions to use data repositories in Amazon S3](setting-up.md#fsx-adding-permissions-s3)\.

For more information about service\-linked roles, see [Using service\-linked roles for Amazon FSx for Lustre](using-service-linked-roles.md)\.

## Troubleshooting a misconfigured linked S3 bucket<a name="troubleshooting-misconfigured-data-repository"></a>

In some cases, an FSx for Lustre file system's linked S3 bucket might have a misconfigured data repository lifecycle state\. 

**Possible cause**

This error can occur if Amazon FSx does not have the necessary AWS Identity and Access Management \(IAM\) permissions that are required to access the linked data repository\. The required IAM permissions support the Amazon FSx for Lustre service\-linked role that is used to access the specified Amazon S3 bucket on your behalf\.

**Action to take**

1. Ensure that your IAM entity \(user, group, or role\) has the appropriate permissions to create file systems\. Doing this includes adding the permissions policy that supports the Amazon FSx for Lustre service\-linked role\. For more information, see [Adding permissions to use data repositories in Amazon S3](setting-up.md#fsx-adding-permissions-s3)\.

1. Using the Amazon FSx CLI or API, refresh the file system's `AutoImportPolicy` with the `update-file-system` CLI command \([UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) is the equivalent API action\), as follows\. 

   ```
   aws fsx update-file-system \
   --file-system-id fs-0123456789abcdef0 \
   --lustre-configuration AutoImportPolicy=the_existing_AutoImportPolicy
   ```

For more information about service\-linked roles, see [Using service\-linked roles for Amazon FSx for Lustre](using-service-linked-roles.md)\.

**Possible Cause**

This error can occur if the linked Amazon S3 data repository has an existing event notification configuration with event types that overlap with the Amazon FSx event notification configuration \(`s3:ObjectCreated:*`, `s3:ObjectRemoved:*`\)\.

This can also occur if the Amazon FSx event notification configuration on the linked S3 bucket was deleted or modified\.

**Action to take**

1. Remove any existing event notification on the linked S3 bucket that uses either or both of the event types that the FSx event configuration uses, `s3:ObjectCreated:*` and `s3:ObjectRemoved:*`\.

1. Please ensure that there is an S3 Event Notification Configuration in you linked S3 bucket with the name `FSx`, event types `s3:ObjectCreated:*` and `s3:ObjectRemoved:*`, and send to the SNS topic with `ARN:topic_arn_returned_in_API_response`\.

1. Reapply the FSx event notification configuration on the S3 bucket by using the Amazon FSx CLI or API, to refresh the file system's `AutoImportPolicy`\. Do so with the `update-file-system` CLI command \([UpdateFileSystem](https://docs.aws.amazon.com/fsx/latest/APIReference/API_UpdateFileSystem.html) is the equivalent API action\), as follows\. 

   ```
   aws fsx update-file-system \
   --file-system-id fs-0123456789abcdef0 \
   --lustre-configuration AutoImportPolicy=the_existing_AutoImportPolicy
   ```

## Troubleshooting storage issues<a name="lfs-migrate-ts"></a>

In some cases, you may experience storage issues with your file system\. You can troubleshoot these issues by using `lfs` commands, such as the `lfs migrate` command\.

### Write error due to no space on storage target<a name="lfs-migrate-no-storage"></a>

You can check the storage usage of your file system by using the `lfs df -h` command, as described in [File system storage layout](performance.md#storage-layout)\. The `filesystem_summary` field reports the total file system storage usage\.

If the file system disk usage is 100%, consider increasing the storage capacity of your file system\. For more information, see [Managing storage and throughput capacity](managing-storage-capacity.md)\.

If the file system storage usage is not 100% and you still get write errors, the file you are writing to may be striped on an OST that is full\.

**Action to take**

If many of your OSTs are full, increase the storage capacity of your file system\. Check for unbalanced storage on OSTs by following the actions of the [Unbalanced storage on OSTs](#lfs-migrate-unbalanced-storage) section\.

### Unbalanced storage on OSTs<a name="lfs-migrate-unbalanced-storage"></a>

Amazon FSx for Lustre distributes new file stripes evenly across OSTs\. However, your file system may still become unbalanced due to I/O patterns or file storage layout\. As a result, some storage targets can become full while others remain relatively empty\.

**Action to take**

1. Launch a relatively large client instance \(such as the Amazon EC2 `c5n.4xlarge` instance type\) to mount to the file system\.

1. Before running the `migrate` command, first run the following commands on each client instance to speed up the process:

   ```
   sudo lctl set_param 'mdc.*.max_rpcs_in_flight=60'
   sudo lctl set_param 'mdc.*.max_mod_rpcs_in_flight=59'
   ```

1. Start a screen session and run the following script\. Make sure to change the following variables:
   + Replace the values in `OSTS` with the values of your OSTs\.
   + Provide an integer value to `nproc` to set the number of max\-procs processes to run in parallel\. For example, the Amazon EC2 `c5n.4xlarge` instance type has 16 vCPUs, so you can use `16` \(or a value < 16\) for `nproc`\.
   + Provide your mount directory path in `mnt_dir_path`\.

   ```
   # find all OSTs with usage above a certain threshold; for example, greater than or equal to 85% full 
   for OST in $(lfs df -h |egrep '( 8[5-9]| 9[0-9]|100)%'|cut -d' ' -f1); do echo ${OST};done|tr '\012' ','
   
   # customer can also just pass OST values directly to OSTS variable
   OSTS='dzfevbmv-OST0000_UUID,dzfevbmv-OST0002_UUID,dzfevbmv-OST0004_UUID,dzfevbmv-OST0005_UUID,dzfevbmv-OST0006_UUID,dzfevbmv-OST0008_UUID'
   
   nproc=<Run up to max-procs processes if client is c5n.4xlarge with 16 vcpu, this value can be set to 16>
   
   mnt_dir_path=<mount dir, e.g. '/my_mnt'>
   
   lfs find ${mnt_dir_path} --ost ${OSTS}| xargs -P ${nproc} -n2 lfs migrate -E 1g -c 1 -E -1 -c5
   ```

**Notes**
+ If you notice that there is an impact on the performance of the reads of the file system, you can stop the migrations at any time by using `ctrl-c` or k`ill -9`, and reduce the number of threads \(`nproc` value\) back to a lower number \(such as 8\), and resume migrating files\.
+ The `lfs migrate` command will fail on a file that is also opened by the client workload\. It will throw an error and move to the next file; therefore, it is possible if there are many files being accessed, the script will not be able to migrate any files, and it will be reflected as the migration is making very slow progress\.
+ You can monitor OST usage using either of the following methods
  + On client mount, run the following command to monitor OST usage and find the OST with usage greater than 85%:

    ```
    lfs df -h |egrep '( 8[5-9]| 9[1-9]|100)%'
    ```
  + Check the Amazon CloudWatch metric, `OST FreeDataStorageCapacity`, check `Minimum`\. If your script is finding OSTs that are over 85% full, then when the metric is close to 15%, use `ctrl-c` or `kill -9` to stop the migration\.
+ You may also consider changing the stripe configuration of your file system or a directory, so that new files are striped across multiple storage targets\. For more information, see in [Striping data in your file system](performance.md#striping-data)\. 