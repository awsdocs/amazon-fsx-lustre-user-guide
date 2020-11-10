# Exporting Changes to the Data Repository<a name="export-changed-data-meta"></a>

You can export data and metadata changes, including POSIX metadata, from your Amazon FSx file system to its linked data repository\. Associated POSIX metadata includes ownership, permissions, and timestamps\. To perform this export, you use a data repository task\.

Data repository tasks optimize data transfer by tracking changes between your Amazon FSx file system and its linked data repository\. Only files or directories with new or modified data or metadata are exported\. By exporting this data, you can implement and maintain access controls between your Amazon FSx for Lustre file system and its linked durable data repository on Amazon S3\. For more information, see [Data Repository Tasks](data-repository-tasks.md)\. 

**Important**  
To ensure that Amazon FSx can export your data to your S3 bucket, it must be stored in a UTF\-8 compatible format\.

**Topics**
+ [Using Data Repository Tasks to Export Data and Metadata Changes](export-data-repo-task.md)
+ [Exporting files using HSM commands](exporting-files-hsm.md)