# Internetwork traffic privacy<a name="internetwork-privacy"></a>

This topic describes how Amazon FSx secures connections from the service to other locations\.

## Traffic between Amazon FSx and on\-premises clients<a name="inter-network-traffic-privacy-on-prem"></a>

You have two connectivity options between your private network and AWS:
+ An AWS Site\-to\-Site VPN connection\. For more information, see [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html)
+ An AWS Direct Connect connection\. For more information, see [What is AWS Direct Connect?](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html)

You can access FSx for Lustre over the network to reach AWS\-published API operations for performing administrative tasks and Lustre ports to interact with the file system\.

### Encrypting API traffic<a name="encrypt-api-traffic"></a>

To access AWS\-published API operations, clients must support Transport Layer Security \(TLS\) 1\.0\. We recommend TLS 1\.2 or higher\. Clients must also support cipher suites with Perfect Forward Secrecy \(PFS\), such as Ephemeral Diffie\-Hellman \(DHE\) or Elliptic Curve Diffie\-Hellman Ephemeral \(ECDHE\)\. Most modern systems such as Java 7 and later support these modes\. Additionally, requests must be signed by using an access key ID and a secret access key that is associated with an IAM principal\. Or you can use the [AWS Security Token Service \(STS\)](https://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html) to generate temporary security credentials to sign requests\.

### Encrypting data traffic<a name="encrypt-data-traffic"></a>

 Encryption of data in transit is enabled from supported EC2 instances accessing the file systems from within the AWS Cloud\. For more information, see [Encrypting data in transit](encryption-in-transit-fsxl.md)\. FSx for Lustre does not natively offer encryption in transit between on\-premise clients and file systems\.  