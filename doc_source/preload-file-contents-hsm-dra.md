# Preloading files into your file system<a name="preload-file-contents-hsm-dra"></a>

Amazon FSx copies data from your Amazon S3 data repository when a file is first accessed\. Because of this approach, the initial read or write to a file incurs a small amount of latency\. If your application is sensitive to this latency, and you know which files or directories your application needs to access, you can optionally preload contents of individual files or directories\. You do so using the `hsm_restore` command, as follows\.

You can use the `hsm_action` command \(issued with the `lfs` user utility\) to verify that the file's contents have finished loading into the file system\. A return value of `NOOP` indicates that the file has successfully been loaded\. Run the following commands from a compute instance with the file system mounted\. Replace *path/to/file* with the path of the file you're preloading into your file system\.

```
sudo lfs hsm_restore path/to/file
sudo lfs hsm_action path/to/file
```

You can preload your whole file system or an entire directory within your file system by using the following commands\. \(The trailing ampersand makes a command run as a background process\.\) If you request the preloading of multiple files simultaneously, Amazon FSx loads your files from your Amazon S3 data repository in parallel\.

```
nohup find local/directory -type f -print0 | xargs -0 -n 1 sudo lfs hsm_restore &
```

**Note**  
If your linked S3 bucket is larger than your file system, you should be able to import all the file metadata into your file system\. However, you can load only as much actual file data as will fit into the file system's remaining storage space\. You'll receive an error if you attempt to access file data when there is no more storage left on the file system\. If this occurs, you can increase the amount of storage capacity as needed\. For more information, see [Managing storage and throughput capacity](managing-storage-capacity.md)\.