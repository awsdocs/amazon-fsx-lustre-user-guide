# Accessing File Systems<a name="accessing-fs"></a>

Amazon FSx for Lustre enables you to burst your compute\-intensive workloads from on\-premises into the AWS cloud by importing data over AWS Direct Connect or VPN\. You can access your Amazon FSx for Lustre file system from on\-premises, copy data into your file system as\-needed, and run compute\-intensive workloads on in\-cloud instances\.

In the following section, you can learn how to access your Amazon FSx for Lustre file system on a Linux instance\. In addition, you can find how to use the file `fstab` to automatically remount your file system after any system restarts\.

Before you can mount a file system, you must create, configure, and launch your related AWS resources\. For detailed instructions, see [Getting Started with Amazon FSx for Lustre](getting-started.md)\. Next, you can install and configure the Lustre client on your compute instance\.

**Topics**
+ [Installing the Lustre Client](install-lustre-client.md)
+ [Mounting from On\-Premises or a Peered Amazon VPC](mounting-on-premises.md)
+ [Mounting Your Amazon FSx for Lustre File System Automatically](mount-fs-auto-mount-onreboot.md)
+ [Unmounting File Systems](unmounting-fs.md)