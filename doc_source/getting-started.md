# Getting Started with Amazon FSx for Lustre<a name="getting-started"></a>

Following, you can learn how to get started using Amazon FSx for Lustre\. These steps walk you through creating an Amazon FSx for Lustre file system, accessing it from your compute instances, and \(optionally\) using your Amazon FSx for Lustre file system to process the data in your Amazon S3 bucket with your file\-based applications\.

This getting started exercise includes these steps:

**Topics**
+ [Prerequisites](#prerequisites)
+ [Step 1: Create Your Amazon FSx for Lustre File System](#getting-started-step1)
+ [Step 2: Install and Configure the Lustre Client on your Instance Before Mounting Your File System](#getting-started-step2)
+ [Step 3: Run Your Analysis](#getting-started-step3)
+ [Step 4: Clean Up Resources](#getting-started-step4)

## Prerequisites<a name="prerequisites"></a>

To perform this getting started exercise, you need the following:
+ An AWS account with the permissions necessary to create an Amazon FSx for Lustre file system and an Amazon EC2 instance\. For more information, see [Setting Up](setting-up.md)\.
+ An Amazon EC2 instance running a supported Linux release in your virtual private cloud \(VPC\) based on the Amazon VPC service\. The Lustre client supports Amazon Linux, Amazon Linux 2, CentOS 7\.5, RedHat 7\.5, SUSE Linux 12 SP3, and Ubuntu 16\.04\. For this getting started exercise, we recommend CentOS 7\.5, which is available in the [AWS Marketplace](https://aws.amazon.com/marketplace/pp/B07DPPMZ6R?qid=1551900841128&sr=0-1&ref_=srh_res_product_title)\. When creating your Amazon EC2 instance for this getting started exercise, keep the following in mind:
  + We recommend that you create your instance in your default VPC\.
  + Verify that an inbound rule exists for the security group you're using with the following values\.
    + **Type:** TCP
    + **Protocol:** 6
    + **Port Range:** 988
    + **Source:** Anywhere 0\.0\.0\.0/0

## Step 1: Create Your Amazon FSx for Lustre File System<a name="getting-started-step1"></a>

Next, you create your file system in the console\.

**To create your file system**

1. Open the [Amazon FSx for Lustre console](https://console.aws.amazon.com/fsx)\.

1. From the dashboard, choose **Create file system** to start the file system creation wizard\.

1. Choose **FSx for Lustre** and then choose **Next** to display the Create File System page\.

1. Provide information in the **File system detail** section, following\.
   + Provide a name for your file system\. You can use up to 256 Unicode letters, white space, and numbers plus the special characters **\+ \- = \. \_ : /**\.
   + Provide the **storage capacity** for your file system, in GiB\. This value can be any whole number in increments of 3,600 GiB\.  
![\[File system details section of the Amazon FSx for Lustre Create File System console page.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/CreateFSxLustre-details.png)

   The estimated costs for your file system are displayed in **Your file system**\.  
![\[File system cost estimates display in Your file system.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/FSxLustre-2.png)

1. Provide networking and security group information in the **Network & security** section, following\.  
![\[Network & Security section of the Amazon FSx for Lustre Create File System console page.\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/FSxLustreNetworkSecurity-3.png)
   + Choose the VPC that you want to associate with your file system\. For the purposes of this getting started exercise, choose the same VPC that you chose for your Amazon EC2 instance\.
   + Choose any value for the **Availability Zones** and **Subnet**\.
   + For **VPC security groups**, the ID for the default security group for your VPC should be already added\. If you're not using the default security group, make sure that the following inbound rule is added to the security group you're using for this getting started exercise\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/getting-started.html)

1. \(Optional\) For **Data repository integration**, choose ** Amazon S3** and specify the Amazon S3 bucket \(with optional prefix\) as the data repository source\.

   Keep **Export prefix** at the default setting\. For more information about the data repository integration, see [Using Data Repositories](fsx-data-repositories.md)
**Important**  
If you link one or more Amazon FSx for Lustre file systems to an Amazon S3 bucket, don't delete the Amazon S3 bucket until all linked file systems have been deleted\.

1. Choose **Review and create**\.

1. Review the settings for your Amazon FSx for Lustre file system, and choose **Create file system**\.

Now that you've created your file system, make a note of its fully qualified domain name for a later step\. You can find the fully qualified domain name for a file system by choosing the name of the file system in the **File Systems** dashboard, and then choosing **Attach**\.

## Step 2: Install and Configure the Lustre Client on your Instance Before Mounting Your File System<a name="getting-started-step2"></a>

To mount your Amazon FSx for Lustre from your Amazon EC2 instance, first install the Lustre client\.

**To download the Lustre client onto your Amazon EC2 instance**

1. Connect to your Amazon EC2 instance\.

1. Install the Lustre client on your CentOS 7\.5 instance with the following procedure:

   1. Open a terminal on your client\.

   1. Download and install the Lustre client with the following commands\. The client comes in two packages that must be downloaded and installed\.

      ```
      sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7.5.1804/client/RPMS/x86_64/kmod-lustre-client-2.10.5-1.el7.x86_64.rpm
      sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7.5.1804/client/RPMS/x86_64/lustre-client-2.10.5-1.el7.x86_64.rpm
      ```
**Note**  
You might need to reboot your compute instance for the client to finish installing\.

**To mount your file system**

1. Make a directory for the mount point with the following command\.

   ```
   $ sudo mkdir -p /mnt/fsx
   ```

1. Mount the Amazon FSx for Lustre file system to the directory that you created\. Use the following command and replace `file_system_dns_name` with the actual file system's DNS name\.

   ```
   sudo mount -t lustre file_system_dns_name@tcp:/fsx /mnt/fsx
   ```

1. To see the contents of your data repository in your file system, use the following command\.

   ```
   ls /mnt/fsx
   ```

## Step 3: Run Your Analysis<a name="getting-started-step3"></a>

Now that your file system has been created and mounted to a compute instance, you can use it to run your high\-performance compute workload\.

If you linked your file system to an Amazon S3 data repository, you can export data that you've written to your file system back to your Amazon S3 bucket at any time\. From a terminal on one of your compute instances, run the following command to export a file to your Amazon S3 bucket\.

```
sudo lfs hsm_archive filename
```

For more information on how to run this command on a folder or large collection of files quickly, see [Using Data Repositories](fsx-data-repositories.md)\.

## Step 4: Clean Up Resources<a name="getting-started-step4"></a>

After you have finished this exercise, you should follow these steps to clean up your resources and protect your AWS account\.

**To clean up resources**

1. If you want to do a final export, run the following command\.

   ```
   nohup find /mnt/fsx -type f -print0 | xargs -0 -n 1 sudo lfs hsm_archive &
   ```

1. On the Amazon EC2 console, terminate your instance\. For more information, see [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances\.*

1. On the Amazon FSx for Lustre console, delete your file system with the following procedure:

   1. In the navigation pane, choose **File systems**\.

   1. Choose the file system that you want to delete from list of file systems on the dashboard\.

   1. For **Actions**, choose **Delete file system**\.

   1. In the dialog box that appears, confirm that you want to delete your file system, and choose **Delete file system**\.

1. If you created an Amazon S3 bucket for this exercise, and if you don't want to preserve the data you exported, you can now delete it\. For more information, see [How Do I Delete an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/delete-bucket.html) in the *Amazon Simple Storage Service Console User Guide\.*