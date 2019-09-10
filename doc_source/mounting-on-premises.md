# Mounting from On\-Premises or a Peered Amazon VPC<a name="mounting-on-premises"></a>

You can access your Amazon FSx for Lustre file system in two ways\. One is from Amazon EC2 instances located in an Amazon VPC that's peered to the file system's VPC\. The other is from on\-premises clients that are connected to your file system's VPC using AWS Direct Connect or VPN\.

You can mount your file system from outside its VPC using the IP address of its primary network interface\. The primary network interface is the first network interface returned when you run the `aws fsx describe-file-systems` AWS CLI command\. You can also get this IP address from the AWS Management Console\.

**Note**  
Amazon FSx can support access from resources outside the VPC associated with your file system\. It can do this if those resources have an IP address in the following private IP version 4 \(IPv4\) address ranges, as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html):  
10\.0\.0\.0–10\.255\.255\.255 \(10/8 prefix\)
172\.16\.0\.0–172\.31\.255\.255 \(172\.16/12 prefix\)
192\.168\.0\.0–192\.168\.255\.255 \(192\.168/16 prefix\)

**To get the IP address of the primary network interface for a file system**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. In the navigation pane, choose **File systems**\.

1. Choose your file system from the dashboard\.

1. From the file system details page, choose **Network & security**\.

1. For **Network interface**, choose the ID for your primary elastic network interface\. Doing this takes you to the Amazon EC2 console\.

1. On the **Details** tab, find the **Primary private IPv4 IP**\. This is the IP address for your primary network interface\.

**Note**  
You can't use Domain Name System \(DNS\) name resolution when mounting an Amazon FSx for Lustre file system from outside the VPC it is associated with\.