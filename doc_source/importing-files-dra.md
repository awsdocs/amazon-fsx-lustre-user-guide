# Importing files from your data repository<a name="importing-files-dra"></a>

At any time, you can create a data repository association \(DRA\) to link your Amazon FSx file system to an Amazon S3 data repository\. You can configured the data repository association to automatically import file metadata for new, changed, or deleted objects from your S3 bucket\. For more information, see [Automatically import updates from your S3 bucket](autoimport-data-repo-dra.md)\.

You can manually import metadata changes from a linked S3 bucket or a subset of the linked bucket by using import data repository tasks\. For more information, see [Using data repository tasks to import metadata changes](import-data-repo-task-dra.md)\.

Amazon FSx transparently copies the content of a file from your repository and loads it into the ﬁle system when your application first accesses the file in Amazon FSx \. You can also preload your whole ﬁle system or an entire directory within your ﬁle system, for more information, see [Preloading files into your file system](preload-file-contents-hsm-dra.md)\. If you request the preloading of multiple ﬁles simultaneously, Amazon FSx loads your ﬁles from your Amazon S3 data repository in parallel\.

When creating the DRA, you can specify that an import task should run after the DRA is created\. The task automatically imports the ﬁle metadata \(the name, ownership, timestamp, and permissions\) of the objects in your S3 bucket\. Amazon FSx makes the imported objects visible as new ﬁles and directories in the ﬁle system\. If an object does not include metadata, then Amazon FSx uses the default permissions of `root root 755`\.

 Amazon FSx *only* imports S3 objects that have POSIX\-compliant object keys, such as these:

```
test/mydir/ 
test/
```

Amazon FSx automatically copies file data for a given file from the linked durable data repository into your file system the first time you open that file\. This data movement is managed by Amazon FSx and occurs transparently to your applications\. Subsequent reads of these files are served directly out of the Amazon FSx file system with consistent sub millisecond latencies\.

**Note**  
Amazon FSx doesn't support importing metadata for symbolic links \(symlinks\) from S3 Glacier Flexible Retrieval and S3 Glacier Deep Archive storage classes\. Metadata for S3 Glacier Flexible Retrieval objects that are not symlinks can be imported \(that is, an inode is created on the Amazon FSx file system with the correct metadata\)\. However, to retrieve the data, you must restore the S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive object first and then use an `hsm_restore` command to import the object\. Importing file data directly from Amazon S3 objects in the S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive storage class into Amazon FSx is not supported\.