# Troubleshooting storage issues<a name="lfs-migrate-ts"></a>

In some cases, you may experience storage issues with your file system\. You can troubleshoot these issues by using `lfs` commands, such as the `lfs migrate` command\.

## Write error due to no space on storage target<a name="lfs-migrate-no-storage"></a>

You can check the storage usage of your file system by using the `lfs df -h` command, as described in [File system storage layout](performance.md#storage-layout)\. The `filesystem_summary` field reports the total file system storage usage\.

If the file system disk usage is 100%, consider increasing the storage capacity of your file system\. For more information, see [Managing storage and throughput capacity](managing-storage-capacity.md)\.

If the file system storage usage is not 100% and you still get write errors, the file you are writing to may be striped on an OST that is full\.

**Action to take**

If many of your OSTs are full, increase the storage capacity of your file system\. Check for unbalanced storage on OSTs by following the actions of the [Unbalanced storage on OSTs](#lfs-migrate-unbalanced-storage) section\.

## Unbalanced storage on OSTs<a name="lfs-migrate-unbalanced-storage"></a>

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