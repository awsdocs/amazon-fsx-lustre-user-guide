# Encrypting data in transit<a name="encryption-in-transit-fsxl"></a>

Scratch 2 and persistent file systems automatically encrypt data in transit when they are accessed from Amazon EC2 instances that support encryption in transit\. To learn which EC2 instances support encryption in transit, see [Encryption in Transit](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/data-protection.html#encryption-transit) in the *Amazon EC2 User Guide for Linux Instances*\.

In\-transit encryption of data for scratch 2 and persistent file systems is available in the following AWS Regions\.


| AWS Region | Scratch\_2 | Persistent\_1 | Persistent\_2 | 
| --- | --- | --- | --- | 
|  US East \(Ohio\)  |  ✓  |  ✓  |  ✓  | 
|  US East \(N\. Virginia\)  |  ✓  |  ✓  |  ✓  | 
|  US West \(Oregon\)  |  ✓  |  ✓  |  ✓  | 
|  US West \(N\. California\) \*  |  ✓  |  ✓  |    | 
|  AWS GovCloud \(US\-East\) \*  |  ✓  |  ✓  |    | 
|  AWS GovCloud \(US\-West\)   |  ✓  |  ✓  |    | 
|  Canada \(Central\) \*  |  ✓  |  ✓  |  ✓  | 
|  Europe \(Ireland\)  |  ✓  |  ✓  |  ✓  | 
|  Europe \(Frankfurt\)  |  ✓  |  ✓  |  ✓  | 
|  Europe \(Paris\)  |  ✓  |  ✓  |    | 
|  Europe \(London\)  |  ✓  |  ✓  |  ✓  | 
|  Europe \(Stockholm\) \*  |  ✓  |  ✓  |    | 
|  Asia Pacific \(Singapore\)  |  ✓  |  ✓  |  ✓  | 
|  Asia Pacific \(Tokyo\) \*  |  ✓  |  ✓  |  ✓  | 
|  Asia Pacific \(Mumbai\) \*  |  ✓  |  ✓  |    | 
|  Asia Pacific \(Hong Kong\) \*  |  ✓  |  ✓  |    | 
|  Asia Pacific \(Sydney\) \*  |  ✓  |  ✓  |  ✓  | 
|  South America \(São Paulo\) \*  |  ✓  |  ✓  |    | 

**Note**  
\* In\-transit data encryption is available for file systems created after April 11, 2021\.