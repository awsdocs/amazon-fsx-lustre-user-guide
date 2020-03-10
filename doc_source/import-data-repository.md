# Importing Data from Your Amazon S3 Bucket<a name="import-data-repository"></a>

During file system creation, Amazon FSx automatically preloads a listing of file metadata into your file system\. This file metadata includes POSIX metadata such as file name, size, modification time, ownership, and permissions\. Doing this allows clients to view the listing of files in your data repository as soon as your file system is available\.

Importing data from your Amazon S3 data repository happens during file system creation\. If you have a large number of files to import, this will impact the amount of time it takes for Amazon FSx to create your file system\.

**Note**  <a name="import-posix-note"></a>
 Amazon FSx only imports S3 objects that have POSIX\-compliant object keys, such as these:  

```
test/mydir/ 
test/
```
Amazon FSx does not import S3 object keys that are not POSIX\-compliant, such as these:  

```
.
.. 
test/. 
test/..
```

Amazon FSx automatically copies file data for a given file from the linked Amazon S3 durable data repository into your file system the first time you open that file\. This data movement is managed by Amazon FSx and occurs transparently to your applications\. Subsequent reads of these files are served directly out of the Amazon FSx file system with consistent submillisecond latencies\.

## Preloading Files into Your File System<a name="preload-file-contents-hsm"></a>

Amazon FSx copies data from your Amazon S3 data repository when a file is first accessed\. Because of this approach, the initial read or write to a file incurs a small latency penalty\. If your application is sensitive to this latency and you know which file your application needs to access, you can optionally preload contents of an individual file\. You do so using the `hsm_restore` command, as follows\.

You can use the `hsm_action` command to verify that the file's contents have finished loading into the file system\. A return value of `NOOP` indicates that the file has successfully been loaded\. Run the following commands from a compute instance with the file system mounted\.

```
sudo lfs hsm_restore path/to/file
sudo lfs hsm_action path/to/file
```

You can preload your whole file system or an entire directory within your file system by using the following commands\. If you request the preloading of multiple files simultaneously, Amazon FSx loads your files from your Amazon S3 data repository in parallel\.

```
nohup find local/directory -type f -print0 | xargs -0 -n 1 sudo lfs hsm_restore &
```