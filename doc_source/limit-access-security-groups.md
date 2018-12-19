# File System Access Control with Amazon VPC<a name="limit-access-security-groups"></a>

An Amazon FSx for Lustre file system is accessible through an elastic network interface that resides in the Amazon Virtual Private Cloud \(Amazon VPC\) that you associate with your file system\. You access your Amazon FSx for Lustre file system through its DNS name, which maps to the file system's network interface\. Only resources within the associated VPC, or a peered VPC, can access your file system's network interface\. For more information, see [What is Amazon VPC?](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) in the* Amazon VPC User Guide\.*

## Amazon VPC Security Groups<a name="fsx-vpc-security-groups"></a>

To further control network traffic going through your file system's network interface within your VPC, you use security groups to limit access to your file systems\. A *security group *acts as a virtual firewall to control the traffic for its associated resources\. In this case, the associated resource is your file system's network interface\.

To use a security group to control access to your Amazon FSx for Lustre file system, you add the inbound rules to control incoming traffic and outbound rules to control the outgoing traffic from your file system\. Make sure to have the right network traffic rules in your security group to map your Amazon FSx for Lustre file system's file share to a folder on your supported compute instance\.

For more information on security group rules, see [Security Group Rules](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html#security-group-rules) in the *Amazon EC2 User Guide for Linux Instances\.*

**To create a security group for Amazon FSx for Lustre**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2](https://console.aws.amazon.com/ec2)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. Specify a name and description for the security group\.

1. For **VPC**, choose the Amazon VPC associated with your file system to create the security group within that VPC\.

1. Add the following inbound rules\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/fsx/latest/LustreGuide/limit-access-security-groups.html)

**To associate a security group with your Amazon FSx for Lustre file system**

1. Open the Amazon FSx console at [https://console\.aws\.amazon\.com/fsx/](https://console.aws.amazon.com/fsx/)\.

1. On the console dashboard, select your file system to view its details\.

1. From the menu, locate the **Network & Security** tab, and choose your file system's network interface IDs \(for example, ENI\-01234567890123456\)\. Doing this redirects you to the Amazon EC2 console\.

1. Choose each network interface ID\. Each action opens a new instance of the Amazon EC2 console in your browser\. For each security group, choose **Actions**, **Change Security Groups**\.

1. In the **Change Security Groups** dialog box, select the security groups to use, and choose **Save**\.

## Amazon VPC Network ACLs<a name="limit-access-acl"></a>

Another option for securing access to the file system within your VPC is to establish network access control lists \(network ACLs\)\. Network ACLs are separate from security groups, but have similar functionality to add an additional layer of security to the resources in your VPC\. For more information on network ACLs, see [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_ACLs.html) in the *Amazon VPC User Guide\.*