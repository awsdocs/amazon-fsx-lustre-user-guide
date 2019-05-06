# Mounting from an Amazon EC2 Instance<a name="mounting-ec2-instance"></a>

You can mount your file system from an Amazon EC2 instance\.

**To mount your file system from Amazon EC2**

1. Make a directory on your Amazon FSx for Lustre file system for the mount point with the following command\.

   ```
   $ sudo mkdir -p /mnt/fsx
   ```

1. Connect to your Amazon EC2 instance\.

1. Mount the Amazon FSx for Lustre file system to the directory that you created\. Use the following command and replace `file_system_dns_name` with the actual file system's DNS name\.

   ```
   sudo mount -t lustre file_system_dns_name@tcp:/fsx /mnt/fsx
   ```

1. View the contents of your data repository in your file system by using the following command\.

   ```
   ls /mnt/fsx
   ```