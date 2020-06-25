# Step 2: Install and Configure the Lustre Client on Your Instance Before Mounting Your File System<a name="getting-started-step2"></a>

To mount your Amazon FSx for Lustre file system from your Amazon EC2 instance, first install the Lustre client\.

**To download the Lustre client onto your Amazon EC2 instance**

1. Connect to your Amazon EC2 instance\. For more information, see [Connecting to Your Linux Instance from Windows Using PuTTY](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html) or [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) in the *Amazon EC2 User Guide for Linux Instances*\.

1. Open a terminal on your client\.

1. Determine which kernel is currently running on the compute instance with the following command\.

   ```
   uname -r
   ```

1. Do one of the following:
   + If the instance is running kernel version `3.10.0-862.*`, download and install the Lustre 2\.10\.5 client with the following commands\. The client comes in two packages to download and install\.

     ```
     sudo yum -y install https://downloads.whamcloud.com/public/lustre/fsx-data-repositorieslustre-2.10.5/el7/client/RPMS/x86_64/kmod-lustre-client-2.10.5-1.el7.x86_64.rpm
     sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7/client/RPMS/x86_64/lustre-client-2.10.5-1.el7.x86_64.rpm
     ```
   + PERSISTENT\_1

     If the instance is running kernel version `3.10.0-957.*`, download and install the Lustre 2\.10\.6 client with the following commands\. The client comes in two packages to download and install\.

     ```
     sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/el7/client/RPMS/x86_64/kmod-lustre-client-2.10.6-1.el7.x86_64.rpm
     sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/el7/client/RPMS/x86_64/lustre-client-2.10.6-1.el7.x86_64.rpm
     ```
**Note**  
You might need to reboot your compute instance for the client to finish installing\.

**To mount your file system**

1. Make a directory for the mount point with the following command\.

   ```
   $ sudo mkdir -p /mnt/fsx
   ```

1. Mount the Amazon FSx for Lustre file system to the directory that you created\. Use the following command and replace the following items:
   + Replace `file_system_dns_name` with the actual file system's Domain Name System \(DNS\) name\.
   + Replace `mountname` with the file system's mount name, which you can get by running the describe\-file\-systems AWS CLI command or the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API operation\.

   ```
   sudo mount -t lustre -o noatime,flock file_system_dns_name@tcp:/mountname /mnt/fsx
   ```

    This command mounts your file system with two options, `-o noatime` and `flock`: 
   +  `noatime` – Turns off updates to inode access times\. To update inode access times, use the `mount` command without `noatime`\. 
   +  `flock` – Enables file locking for your file system\. If you don't want file locking enabled, use the `mount` command without `flock`\. 

1. Verify that the mount command was successful by listing the contents of the directory to which you mounted the file system `/mnt/fsx`, by using the following command\.

   ```
   $ ls /mnt/fsx
   import-path  lustre
   $
   ```

   You can also use the `df` command, following\.

   ```
   $ df
   Filesystem                      1K-blocks    Used  Available Use% Mounted on
   devtmpf                          1001808       0    1001808   0% /dev
   tmpfs                            1019760       0    1019760   0% /dev/shm
   tmpfs                            1019760     392    1019368   1% /run
   tmpfs                            1019760       0    1019760   0% /sys/fs/cgroup
   /dev/xvda1                       8376300 1263180    7113120  16% /
   123.456.789.0@tcp:/mountname  3547698816   13824 3547678848   1% /mnt/fsx
   tmpfs                             203956       0     203956   0% /run/user/1000
   ```

   The results show the Amazon FSx file system mounted on /mnt/fsx\.