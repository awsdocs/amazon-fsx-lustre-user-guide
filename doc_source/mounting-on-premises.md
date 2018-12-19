# Mounting from On\-Premises or a Peered Amazon VPC<a name="mounting-on-premises"></a>

You can access your Amazon FSx for Lustre file system in two ways\. One is from Amazon EC2 instances located in an Amazon VPC that's peered to the file system's VPC\. The other is from on\-premises clients that are connected to your file system's VPC using AWS Direct Connect or VPN\.

You can mount your file system from outside its VPC using the IP address of its primary network interface\. The primary network interface is the first network interface returned when you run the `aws fsx describe-file-systems` AWS CLI command\. You can also get this IP address from the console\.

**To get the IP address of the primary network interface for a file system**

1. Sign into the Amazon FSx for Lustre AWS Management Console\.

1. From the left\-side navigation, choose **File systems**\.

1. Choose your file system from the dashboard\.

1. From the file system details page, choose **Network & security**\.

1. From **Network interface**, choose the ID for your primary elastic network interface\. Doing this takes you to the Amazon EC2 console\.

1. From the **Details** tab, towards the bottom of the page, find the **Primary private IPv4 IP**\. This is the IP address for your primary network interface\.

**Note**  
You can't use Domain Name System \(DNS\) name resolution when mounting an Amazon FSx for Lustre file system from outside the VPC it is associated with\.