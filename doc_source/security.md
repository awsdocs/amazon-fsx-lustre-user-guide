# Security<a name="security"></a>

Amazon FSx for Lustre provides various features to secure your file systems\. In the following sections, you can find information on how to secure your file system data and configure access controls for your Amazon FSx for Lustre file systems\.

Amazon FSx for Lustre is PCI\-DSS and ISO compliant and HIPAA eligible\.

## Encryption<a name="encryption"></a>

All Amazon FSx for Lustre file systems are encrypted at rest with keys managed by the service\. Data is encrypted using an XTS\-AES\-256 block cipher\. Data is automatically encrypted before being written to the file system, and automatically decrypted as it is read\. These processes are handled transparently by Amazon FSx for Lustre, so you don’t have to modify your applications\.