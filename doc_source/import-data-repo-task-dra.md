# Using data repository tasks to import metadata changes<a name="import-data-repo-task-dra"></a>

Use the following procedures to import metadata changes by using the Amazon FSx console and CLI\. Note that you can use one data repository task for multiple DRAs\.

## To import metadata changes \(console\)<a name="create-import-data-repo-task-dra-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. On the navigation pane, choose **File systems**, then choose your Lustre file system\.

1. Choose the **Data repository** tab\.

1. In the **Data repository associations** pane, choose the data repository associations you want to create the import task for\.

1. From the **Actions** menu, choose **Import task**\. This choice is not available if the file system isn't linked to a data repository\. The **Create import data repository task** page appears\.  
![\[Create import data repository task page\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/create-import-task.png)

1. \(Optional\) Specify up to 32 directories or files to import from your linked S3 buckets by providing the paths to those directories or files in **Data repository paths to import**\.
**Note**  
If a path that you provide isn't valid, the task fails\.

1. \(Optional\) Choose **Enable** under **Completion report** to generate a task completion report after the task completes\. A *task completion report* provides details about the files processed by the task that meet the scope provided in **Report scope**\. To specify the location for Amazon FSx to deliver the report, enter a relative path on a linked S3 data repository for **Report path**\.

1. Choose **Create**\. 

   A notification at the top of the **File systems** page shows the task that you just created in progress\. 

To view the task status and details, scroll down to the **Data Repository Tasks** pane in the **Data Repository** tab for the file system\. The default sort order shows the most recent task at the top of the list\.

To view a task summary from this page, choose **Task ID** for the task you just created\. The **Summary** page for the task appears\. 

## To import metadata changes \(CLI\)<a name="create-import-data-repo-task-dra-cli"></a>
+ Use the [https://docs.aws.amazon.com/cli/latest/reference/fsx/create-data-repository-task.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/create-data-repository-task.html) CLI command to import metadata changes on your FSx for Lustre file system\. The corresponding API operation is [https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateDataRepositoryTask.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CreateDataRepositoryTask.html)\.

  ```
  $ aws fsx create-data-repository-task \
      --file-system-id fs-0123456789abcdef0 \
      --type IMPORT_METADATA_FROM_REPOSITORY \
      --paths s3://bucketname1/dir1/path1 \
      --report Enabled=true
  ```

  After successfully creating the data repository task, Amazon FSx returns the task description as JSON\.

After creating the task to import metadata from the linked data repository, you can check the status of the import\. For more information about viewing data repository tasks, see [Accessing data repository tasks](managing-data-repo-task.md#view-data-repo-tasks)\.