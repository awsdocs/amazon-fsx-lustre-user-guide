# Data repository tasks<a name="data-repository-tasks"></a>

By using data repository tasks, you can manage the transfer of data and metadata between your FSx for Lustre file system and any of its durable data repositories on Amazon S3\.

*Data repository tasks* optimize data and metadata transfers between your FSx for Lustre file system and a data repository on S3\. One way that they do this is by tracking changes between your Amazon FSx file system and its linked data repository\. They also do this by using parallel transfer techniques to transfer data at speeds up to hundreds of GB/s\. You create and view data repository tasks using the Amazon FSx console, the AWS CLI, and the Amazon FSx API\. 

Data repository tasks maintain the file system's Portable Operating System Interface \(POSIX\) metadata, including ownership, permissions, and timestamps\. Because the tasks maintain this metadata, you can implement and maintain access controls between your FSx for Lustre file system and its linked data repositories\.

## Types of data repository tasks<a name="data-repo-task-types"></a>

There are two types of data repository tasks:
+ The **Export** data repository task exports from your Lustre file system to a linked S3 bucket\.
+ The **Import** data repository task imports from a linked S3 bucket to your Lustre file system\.

For more information, see [Creating a data repository task](managing-data-repo-task.md#creating-data-repo-task)\.

**Topics**
+ [Types of data repository tasks](#data-repo-task-types)
+ [Understanding a task's status and details](data-repo-task-status.md)
+ [Using data repository tasks](managing-data-repo-task.md)
+ [Working with task completion reports](task-completion-report.md)
+ [Troubleshooting import and export failures](failed-tasks.md)