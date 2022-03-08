# Mounting specific filesets<a name="mounting-from-fileset"></a>

By using the Lustre fileset feature, you can mount only a subset of the file system namespace, which is called a *fileset*\. To mount a fileset of the file system, on the client you specify the subdirectory path after the file system name\. A fileset mount \(also called a subdirectory mount\) limits the file system namespace visibility on a specific client\.

**Example – Mount a Lustre fileset**

1. Assume you have an FSx for Lustre file system with the following directories:

   ```
   team1/dataset1/
   team2/dataset2/
   ```

1. You mount only the `team1/dataset1` fileset, making only this part of the file system visible locally on the client\. Use the following command and replace the following items:
   + Replace `file_system_dns_name` with the actual file system's DNS name\.
   + Replace `mountname` with the file system's mount name\. This mount name is returned in the `CreateFileSystem` API operation response\. It's also returned in the response of the describe\-file\-systems AWS CLI command, and the [DescribeFileSystems](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API operation\.

   ```
   mount -t lustre file_system_dns_name@tcp:/mountname/team1/dataset1 /fsx
   ```

When using the Lustre fileset feature, keep the following in mind:
+ There are no constraints preventing a client from remounting the file system using a different fileset, or no fileset at all\.
+ When using a fileset, some Lustre administrative commands requiring access to the `.lustre/` directory may not work, such as the `lfs fid2path` command\.
+ If you plan to mount several subdirectories from the same file system on the same host, be aware that this consumes more resources than a single mount point, and it could be more efficient to mount the file system root directory only once instead\.

For more information on the Lustre fileset feature, see the *Lustre Operations Manual* on the [Lustre documentation website](https://doc.lustre.org/lustre_manual.xhtml#SystemConfigurationUtilities.fileset)\.