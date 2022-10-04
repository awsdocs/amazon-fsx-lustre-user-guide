# You can't access your file system<a name="cant-access-fs"></a>

There are a number of potential causes for being unable to access your file system, each with their own resolution, as follows\.

## The Elastic IP address attached to the file system elastic network interface was deleted<a name="eni-epi-removed"></a>

Amazon FSx doesn't support accessing file systems from the public Internet\. Amazon FSx automatically detaches any Elastic IP address, which is a public IP address reachable from the Internet, that gets attached to a file system's elastic network interface\.

## The file system elastic network interface was modified or deleted<a name="eni-deleted"></a>

You must not modify or delete the file system's elastic network interface\. Modifying or deleting the network interface can cause a permanent loss of connection between your VPC and your file system\. Create a new file system, and do not modify or delete the FSx elastic network interface\. For more information, see [File System Access Control with Amazon VPC](limit-access-security-groups.md)\.