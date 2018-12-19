# Amazon FSx for Lustre Maintenance Windows<a name="maintenance-windows"></a>

Amazon FSx for Lustre performs routine software patching for the Lustre software it manages\. The maintenance window is your opportunity to control what day and time of the week this software patching occurs\.

Patching occurs infrequently, typically once every several weeks\. Patching should require only a fraction of your 30\-minute maintenance window\. During these few minutes of time, your file system will be temporarily unavailable\. 

You choose the maintenance window during file system creation\. If you have no time preference, then a 30\-minute default window is assigned\.

## Changing the Maintenance Window of an Existing File System<a name="change-maintenance-windows"></a>

You can use the AWS CLI or one of the AWS SDKs to change the maintenance window for your file systems\.

**To update the weekly maintenance window of your file system**

1. Open a command prompt or terminal on your computer\.

1. Run the following command, replacing the file system ID with the ID for your file system, and the date and time with when you want to begin the window\.

   ```
   aws fsx update-file-system --file-system-id fs-01234567890123456 --lustre-configuration WeeklyMaintenanceStartTime=1:01:30
   ```