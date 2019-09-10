# Mounting from an Amazon EC2 Instance<a name="mounting-ec2-instance"></a>

You can mount your file system from an Amazon EC2 instance\.

**To mount your file system from Amazon EC2**

1. Make a directory on your Amazon FSx for Lustre file system for the mount point with the following command\.

   ```
   $ sudo mkdir -p /fsx
   ```

1. Connect to your Amazon EC2 instance\.

1. Mount the Amazon FSx for Lustre file system to the directory that you created\. Use the following command and replace `file_system_dns_name` with the actual file system's DNS name\.

   ```
   sudo mount -t lustre -o noatime,flock file_system_dns_name@tcp:/fsx /fsx
   ```

    This command mounts your file system with 2 options, `-o noatime,flock`, following: 
   +  `noatime` – mounts your file system with inode access time updates turned off\. If you want to update inode access times, use the command without `noatime`\. 
   +  `flock` – mounts your file system with file locking enabled\. If you do not want file locking enable, use the command without `flock`\. 

1. Verify that the mount command was successful by listing the contents of the directory to which you mounted the file system, /fsx by using the following command\.

   ```
   $ ls /fsx
   import-path  lustre
   $
   ```

   You can also use the `df` command, following\.

   ```
   $ df
   Filesystem              1K-blocks    Used  Available Use% Mounted on
   devtmpfs                  1001808       0    1001808   0% /dev
   tmpfs                     1019760       0    1019760   0% /dev/shm
   tmpfs                     1019760     392    1019368   1% /run
   tmpfs                     1019760       0    1019760   0% /sys/fs/cgroup
   /dev/xvda1                8376300 1263180    7113120  16% /
   123.456.789.0@tcp:/fsx 3547698816   13824 3547678848   1% /fsx
   tmpfs                      203956       0     203956   0% /run/user/1000
   ```

   The results show the Amazon FSx file system mounted on /fsx\.