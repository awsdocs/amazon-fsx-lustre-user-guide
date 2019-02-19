# Mounting Your Amazon FSx for Lustre File System Automatically<a name="mount-fs-auto-mount-onreboot"></a>

You can use `fstab` to automatically mount your Amazon FSx for Lustre file system when the Amazon EC2 instance it is mounted on reboots\. You can set up automatic mounting in two ways\. You can update the `/etc/fstab` file in your Amazon EC2 instance after you connect to the instance for the first time, or you can configure automatic mounting of your Amazon FSx for Lustre file system when you create your Amazon EC2 instance\.

## Updating an Existing EC2 Instance to Mount Automatically<a name="lustre-mount-fs-auto-mount-update-fstab"></a>

To automatically remount your Amazon FSx for Lustre file system directory when the Amazon EC2 instance reboots, you can use the `fstab` file\. The `fstab` file contains information about file systems\. The command `mount -a`, which runs during instance startup, mounts the file systems listed in the `fstab` file\.

**Note**  
Before you can update the `/etc/fstab` file of your EC2 instance, make sure that you've already created your Amazon FSx for Lustre file system\. For more information, see [Step 1: Create Your Amazon FSx for Lustre File System](getting-started.md#getting-started-step1) in the Getting Started exercise\.

**To update the /etc/fstab file in your EC2 instance**

1. Connect to your EC2 instance, and open the `/etc/fstab` file in an editor\.

1. Add the following line to the `/etc/fstab` file\.

   ```
   file_system_dns_name@tcp:/fsx /mnt/fsx lustre defaults,_netdev 0 0
   ```
**Warning**  
Use the `_netdev` option, used to identify network file systems, when mounting your file system automatically\. If `_netdev` is missing, your EC2 instance might stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\. For more information, see [Automatic Mounting Fails and the Instance Is Unresponsive](troubleshooting.md#lustre-automount-fails)\.

1. Save the changes to the file\.

Your EC2 instance is now configured to mount the Amazon FSx for Lustre file system whenever it restarts\.

**Note**  
In some cases, your Amazon EC2 instance might need to start regardless of the status of your mounted Amazon FSx for Lustre file system\. In these cases, add the `nofail` option to your file system's entry in your `/etc/fstab` file\.

The fields in the line of code that you added to the `/etc/fstab` file do the following\.


| Field | Description | 
| --- | --- | 
|  `file_system_dns_name@tcp:/fsx`  |  The DNS name for your Amazon FSx for Lustre file system, which identifies the file system\. You can get this name from the console or programmatically from the AWS CLI or an AWS SDK\.  | 
|  `/mnt/fsx`  |  The mount point for the Amazon FSx for Lustre file system on your EC2 instance\.  | 
|  `lustre`  |  Describes the type of file system, Amazon FSx for Lustre\.  | 
|  `mount options`  |  Mount options for the file system, presented as a comma\-separated list of the following options: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/mount-fs-auto-mount-onreboot.html)  | 
|  `0`  |  A value that indicates whether the file system should be backed up by `dump`\. For Amazon FSx, this value should be `0`\.  | 
|  `0`  |  A value that indicates the order in which `fsck` checks file systems at boot\. For Amazon FSx file systems, this value should be `0` to indicate that `fsck` should not run at startup\.  | 