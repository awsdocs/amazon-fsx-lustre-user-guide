# Using data repository tasks<a name="managing-data-repo-task"></a>

You can create, duplicate, view details, and cancel data repository tasks using the Amazon FSx console, CLI, or API\.

## Creating a data repository task<a name="creating-data-repo-task"></a>

You can create a data repository task by using the Amazon FSx console, CLI, or API\. After you create a task, you can view the task's progress and status by using the console, CLI, or API\.

You can create two types of data repository tasks:
+ The **Export** data repository task exports from your Lustre file system to a linked S3 bucket\. For more information, see [Using data repository tasks to export data and metadata changes](export-data-repo-task-dra.md)\.
+ The **Import** data repository task imports from a linked S3 bucket to your Lustre file system\. For more information, see [Using data repository tasks to import metadata changes](import-data-repo-task-dra.md)\.

## Duplicating a task<a name="recreate-task"></a>

You can duplicate an existing data repository task in the Amazon FSx console\. When you duplicate a task, an exact copy of the existing task is displayed in the **Create import data repository task** or **Create export data repository task** page\. You can make changes to the paths to export or import, as needed, before creating and running the new task\.

**Note**  
A request to run a duplicate task will fail if an exact copy of that task is already running\. An exact copy of a task that is already running contains the same file system path\(s\) in case of an export task or the same data repository path\(s\) in case of an import task\.

You can duplicate a task from the task details view, the **Data Repository Tasks** pane in the **Data Repository** tab for the file system, or from the **Data repository tasks** page\.

**To duplicate an existing task**

1. Choose a task on the **Data Repository Tasks** pane in the **Data Repository** tab for the file system\.

1. Choose **Duplicate task**\. Depending on which type of task you chose, the **Create import data repository task** or **Create export data repository task** page appears\. All settings for the new task are identical to those for the task that you're duplicating\.

1. Change or add the paths that you want to import from or export to\.

1. Choose **Create**\.

## Accessing data repository tasks<a name="view-data-repo-tasks"></a>

After you create a data repository task, you can access the task, and all existing tasks in your account, using the Amazon FSx console, CLI, and API\. Amazon FSx provides the following detailed task information: 
+ All existing tasks\.
+ All tasks for a specific file system\.
+ All tasks for a specific data repository association\.
+ All tasks with a specific lifecycle status\. For more information about task lifecycle status values, see [Understanding a task's status and details](data-repo-task-status.md)\.

You can access all existing data repository tasks in your account by using the Amazon FSx console, CLI, or API, as described following\.

### To view data repository tasks and task details \(console\)<a name="access-all-tasks-console"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. On the navigation pane, choose **Data repository tasks \(Lustre\)**\. The **Data repository tasks** page appears, showing existing tasks\.

1. To see a task's details, choose **Task ID** or **Task name** in the **Data repository tasks** page\. The task detail page appears\.  
![\[Data repository tasks page\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/images/task-details-rprt.png)

### To retrieve data repository tasks and task details \(CLI\)<a name="task-details-cli"></a>

Using the Amazon FSx [https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-data-repository-tasks.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-data-repository-tasks.html) CLI command, you can view all the data repository tasks, and their details, in your account\. [https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeDataRepositoryTasks.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeDataRepositoryTasks.html) is the equivalent API command\.
+ Use the following command to view all data repository task objects in your account\.

  ```
  aws fsx describe-data-repository-tasks
  ```

  If the command is successful, Amazon FSx returns the response in JSON format\.

  ```
  {
      "DataRepositoryTasks": [
          {
              "Lifecycle": "EXECUTING",
              "Paths": [],
              "Report": {
                  "Path":"s3://dataset-01/reports",
                  "Format":"REPORT_CSV_20191124",
                  "Enabled":true,
                  "Scope":"FAILED_FILES_ONLY"
              },
              "StartTime": 1591863862.288,
              "EndTime": ,
              "Type": "EXPORT_TO_REPOSITORY",
              "Tags": [],
              "TaskId": "task-0123456789abcdef3",
              "Status": {
                  "SucceededCount": 4255,
                  "TotalCount": 4200,
                  "FailedCount": 55,
                  "LastUpdatedTime": 1571863875.289
              },
              "FileSystemId": "fs-0123456789a7",
              "CreationTime": 1571863850.075,
              "ResourceARN": "arn:aws:fsx:us-east-1:1234567890:task/task-0123456789abcdef3"
          },
          {
              "Lifecycle": "FAILED",
              "Paths": [],
              "Report": {
                  "Enabled": false,
              },
              "StartTime": 1571863862.288,
              "EndTime": 1571863905.292,
              "Type": "EXPORT_TO_REPOSITORY",
              "Tags": [],
              "TaskId": "task-0123456789abcdef1",
              "Status": {
                  "SucceededCount": 1153,
                  "TotalCount": 1156,
                  "FailedCount": 3,
                  "LastUpdatedTime": 1571863875.289
              },
              "FileSystemId": "fs-0123456789abcdef0",
              "CreationTime": 1571863850.075,
              "ResourceARN": "arn:aws:fsx:us-east-1:1234567890:task/task-0123456789abcdef1"
          },
          {
              "Lifecycle": "SUCCEEDED",
              "Paths": [],
              "Report": {
                  "Path":"s3://dataset-04/reports",
                  "Format":"REPORT_CSV_20191124",
                  "Enabled":true,
                  "Scope":"FAILED_FILES_ONLY"
              },
              "StartTime": 1571863862.288,
              "EndTime": 1571863905.292,
              "Type": "EXPORT_TO_REPOSITORY",
              "Tags": [],
              "TaskId": "task-04299453935122318",
              "Status": {
                  "SucceededCount": 258,
                  "TotalCount": 258,
                  "FailedCount": 0,
                  "LastUpdatedTime": 1771848950.012,
              },
              "FileSystemId": "fs-0123456789abcdef0",
              "CreationTime": 1771848950.012,
              "ResourceARN": "arn:aws:fsx:us-east-1:1234567890:task/task-0123456789abcdef0"
          }
      ]
  }
  ```

### Viewing tasks by file system<a name="view-tasks-by-fs"></a>

You can view all tasks for a specific file system using the Amazon FSx console, CLI, or API, as described following\.

#### To view tasks by file system \(console\)<a name="tasks-by-fs-console"></a>

1. Choose **File systems** on the navigation pane\. The **File systems** page appears\.

1. Choose the file system that you want to view data repository tasks for\. The file system details page appears\.

1. On the file system details page, choose the **Data repository** tab\. Any tasks for this file system appear on the **Data repository tasks** panel\.

#### To retrieve tasks by file system \(CLI\)<a name="task-by-fs-cli"></a>
+ Use the following command to view all data repository tasks for file system `fs-0123456789abcdef0`\.

  ```
  aws fsx describe-data-repository-tasks \
      --filters Name=file-system-id,Values=fs-0123456789abcdef0
  ```

  If the command is successful, Amazon FSx returns the response in JSON format\.

  ```
  {
      "DataRepositoryTasks": [
          {
              "Lifecycle": "FAILED",
              "Paths": [],
              "Report": {
                  "Path":"s3://dataset-04/reports",
                  "Format":"REPORT_CSV_20191124",
                  "Enabled":true,
                  "Scope":"FAILED_FILES_ONLY"
              },
              "StartTime": 1571863862.288,
              "EndTime": 1571863905.292,
              "Type": "EXPORT_TO_REPOSITORY",
              "Tags": [],
              "TaskId": "task-0123456789abcdef1",
              "Status": {
                  "SucceededCount": 1153,
                  "TotalCount": 1156,
                  "FailedCount": 3,
                  "LastUpdatedTime": 1571863875.289
              },
              "FileSystemId": "fs-0123456789abcdef0",
              "CreationTime": 1571863850.075,
              "ResourceARN": "arn:aws:fsx:us-east-1:1234567890:task/task-0123456789abcdef1"
          },
          {
              "Lifecycle": "SUCCEEDED",
              "Paths": [],
              "Report": {
                  "Enabled": false,
              },
              "StartTime": 1571863862.288,
              "EndTime": 1571863905.292,
              "Type": "EXPORT_TO_REPOSITORY",
              "Tags": [],
              "TaskId": "task-0123456789abcdef0",
              "Status": {
                  "SucceededCount": 258,
                  "TotalCount": 258,
                  "FailedCount": 0,
                  "LastUpdatedTime": 1771848950.012,
              },
              "FileSystemId": "fs-0123456789abcdef0",
              "CreationTime": 1771848950.012,
              "ResourceARN": "arn:aws:fsx:us-east-1:1234567890:task/task-0123456789abcdef0"
          }
      ]
  }
  ```

## Canceling a data repository task<a name="cancel-data-repo-task"></a>

You can cancel a data repository task while it's in either the PENDING or EXECUTING state\. When you cancel a task, the following occurs:
+ Amazon FSx doesn't process any files that are in the queue to be processed\.
+ Amazon FSx continues processing any files that are currently in process\.
+ Amazon FSx doesn't revert any files that the task already processed\.

### To cancel a data repository task \(console\)<a name="w209aac13c25c15c11b7b1"></a>

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. Click on the file system for which you want to cancel a data repository task\.

1. Open the **Data Repository** tab and scroll down to view the **Data Repository Tasks** panel\.

1. Choose **Task ID** or **Task name** for the task that you want to cancel\.

1. Choose **Cancel task** to cancel the task\.

1. Enter the task ID to confirm the cancellation request\.

### To cancel a data repository task \(CLI\)<a name="w209aac13c25c15c11b7b3"></a>

Use the Amazon FSx [https://docs.aws.amazon.com/cli/latest/reference/fsx/cancel-data-repository-task.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/cancel-data-repository-task.html) CLI command, to cancel a task\. [https://docs.aws.amazon.com/fsx/latest/APIReference/API_CancelDataRepositoryTask.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_CancelDataRepositoryTask.html) is the equivalent API command\.
+ Use the following command to view all data repository task objects in your account\.

  ```
  aws fsx cancel-data-repository-task \
      --task-id fs-0123456789abcdef0
  ```

  If the command is successful, Amazon FSx returns the response in JSON format\.

  ```
  {
      "Status": "CANCELING",
      "TaskId": "task-0123456789abcdef0"
  }
  ```