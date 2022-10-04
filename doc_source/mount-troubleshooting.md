# Troubleshooting file system mount issues<a name="mount-troubleshooting"></a>

There are a number of potential causes when a file system mount command fails, as described in the following topics\.

## File system mount fails right away<a name="mount-fails-right-away"></a>

The file system mount command fails right away\. The following code shows an example\.

```
mount.lustre: mount fs-0123456789abcdef0.fsx.us-east-1.aws@tcp:/fsx at /lustre
failed: No such file or directory

Is the MGS specification correct?
Is the filesystem name correct?
```

This error can occur if you aren't using the correct `mountname` value when mounting a persistent or scratch 2 file system by using the mount command\. You can get the `mountname` value from the response of the [https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html](https://docs.aws.amazon.com/cli/latest/reference/fsx/describe-file-systems.html) AWS CLI command or the [https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html](https://docs.aws.amazon.com/fsx/latest/APIReference/API_DescribeFileSystems.html) API operation\.

## File system mount hangs and then fails with timeout error<a name="mount-hangs-fails-timeout"></a>

The file system mount command hangs for a minute or two, and then fails with a timeout error\. 

The following code shows an example\.

```
sudo mount -t lustre file_system_dns_name@tcp:/mountname /mnt/fsx

[2+ minute wait here]
Connection timed out
```

This error can occur because the security groups for the Amazon EC2 instance or the file system aren't configured properly\.

**Action to take**

Make sure that your security groups for the file system have the inbound rules specified in [Amazon VPC Security Groups](limit-access-security-groups.md#fsx-vpc-security-groups)\.

## Automatic mounting fails and the instance is unresponsive<a name="lustre-automount-fails"></a>



In some cases, automatic mounting might fail for a file system and your Amazon EC2 instance might stop responding\.

This issue can occur if the `_netdev` option wasn't declared\. If `_netdev` is missing, your Amazon EC2 instance can stop responding\. This result is because network file systems need to be initialized after the compute instance starts its networking\.

**Action to take**  
If this issue occurs, contact AWS Support\.

## File system mount fails during system boot<a name="mount-fails-boot-up"></a>

The file system mount fails during the system boot\. The mounting is automated using `/etc/fstab`\. When the file system is not mounted, the following error is seen in the syslog for the instance booting time frame\.

```
LNetError: 3135:0:(lib-socket.c:583:lnet_sock_listen()) Can't create socket: port 988 already in use
LNetError: 122-1: Can't start acceptor on port 988: port already in use
```

This error can occur when port 988 is not available\. When the instance is configured to mount NFS file systems, it is possible that the NFS mounts will bind its client port to port 988

**Action to take**

You can work around this problem by tuning the NFS client's `noresvport` and `noauto` mount options where possible\.

## File system mount using DNS name fails<a name="mount-fails-dns-name"></a>

Misconfigured Domain Name Service \(DNS\) names can cause file system mount failures, as shown in the following scenarios\.

**Scenario 1:** A file system mount that is using a Domain Name Service \(DNS\) name fails\. The following code shows an example\.

```
sudo mount -t lustre file_system_dns_name@tcp:/mountname /mnt/fsx
mount.lustre: Can't parse NID 
'file_system_dns_name@tcp:/mountname'
```

**Action to take**

Check your virtual private cloud \(VPC\) configuration\. If you are using a custom VPC, make sure that DNS settings are enabled\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the *Amazon VPC User Guide*\. 

To specify a DNS name in the `mount` command, do the following:
+ Ensure that the Amazon EC2 instance is in the same VPC as your Amazon FSx for Lustre file system\.
+ Connect your Amazon EC2 instance inside a VPC configured to use the DNS server provided by Amazon\. For more information, see [DHCP Options Sets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html) in the *Amazon VPC User Guide*\.
+ Ensure that the Amazon VPC of the connecting Amazon EC2 instance has DNS host names enabled\. For more information, see [Updating DNS Support for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-updating) in the *Amazon VPC User Guide*\.

**Scenario 2:** A file system mount that is using a Domain Name Service \(DNS\) name fails\. The following code shows an example\.

```
mount -t lustre file_system_dns_name@tcp:/mountname /mnt/fsx
mount.lustre: mount file_system_dns_name@tcp:/mountname at /mnt/fsx failed: Input/output error Is the MGS running?
```

**Action to take**

 Make sure that the client's VPC security groups have the correct outbound traffic rules applied\. This recommendation holds true especially if you aren't using the default security group, or if you have modified the default security group\. For more information, see [Amazon VPC Security Groups](limit-access-security-groups.md#fsx-vpc-security-groups)\. 