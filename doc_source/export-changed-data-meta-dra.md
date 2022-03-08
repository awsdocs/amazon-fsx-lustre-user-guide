# Exporting changes to the data repository<a name="export-changed-data-meta-dra"></a>

You can export data and metadata changes, including POSIX metadata, from your Amazon FSx file system to a linked data repository\. Associated POSIX metadata includes ownership, permissions, and timestamps\.

To export changes from the file system, use one of the following methods\.
+ You configure your file system to automatically export new, changed, or deleted files to your linked data repository\. For more information, see [Automatically export updates to your S3 bucket](autoexport-data-repo-dra.md)\.
+ You use an on\-demand export data repository task\. For more information, see [Using data repository tasks to export data and metadata changes](export-data-repo-task-dra.md)

Automatic export and export data repository tasks cannot run at the same time\.

Both automatic export and export data repository tasks optimize data transfer by tracking changes between your Amazon FSx file system and its linked data repository\. When you turn on automatic export on a data repository association, your file system can export file data and metadata as they are created, modified, or deleted\. When you export a file or directory using an export data repository task, your file system exports only data files and metadata that were created or modified since the last export\. Both automatic export and export data repository tasks include POSIX metadata\.

**Important**  
To ensure that Amazon FSx can export your data to your S3 bucket, it must be stored in a UTF\-8 compatible format\.

**Topics**
+ [Automatically export updates to your S3 bucket](autoexport-data-repo-dra.md)
+ [Using data repository tasks to export data and metadata changes](export-data-repo-task-dra.md)
+ [Exporting files using HSM commands](exporting-files-hsm.md)