# How Amazon FSx for Lustre uses AWS KMS<a name="FSXKMS"></a>

Amazon FSx for Lustre encrypts your file system in the following way:
+ **Encrypting data at rest** – Amazon FSx for Lustre encrypts data automatically before being it is written to the file system, and automatically decrypts data as it is read\. Data is encrypted using an XTS\-AES\-256 block cipher\. All scratch FSx for Lustre file systems are encrypted at rest with keys managed by AWS\. The keys used to encrypt scratch file systems at\-rest are unique per file system and destroyed after the file system is deleted\. For persistent file systems, you choose the KMS key used to encrypt and decrypt data, either the AWS managed key for Amazon FSx or a custom KMS key\. You specify which key to use when you create a persistent file system\. You can enable, disable, or revoke grants on this KMS key\. This KMS key can be one of the two following types:
  + **AWS managed key for Amazon FSx** – This is the default KMS key\. You're not charged to create and store a KMS key, but there are usage charges\. For more information, see [AWS Key Management Service pricing](https://aws.amazon.com/kms/pricing/)\.
  + **Customer managed key** – This is the most flexible KMS key to use, because you can configure its key policies and grants for multiple users or services\. For more information on creating customer managed keys, see [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the* AWS Key Management Service Developer Guide\.*

    If you use a customer managed key as your KMS key for file data encryption and decryption, you can enable key rotation\. When you enable key rotation, AWS KMS automatically rotates your key once per year\. Additionally, with a customer managed key, you can choose when to disable, re\-enable, delete, or revoke access to your customer managed key at any time\. 

**Important**  
Amazon FSx accepts only symmetric encryption KMS keys\. You can't use asymmetric KMS keys with Amazon FSx\.

## Amazon FSx key policies for AWS KMS<a name="FSxKMSPolicy"></a>

Key policies are the primary way to control access to KMS keys\. For more information on key policies, see [Using key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide\. *The following list describes all the AWS KMS–related permissions supported by Amazon FSx for encrypted at rest file systems:
+ **kms:Encrypt** – \(Optional\) Encrypts plaintext into ciphertext\. This permission is included in the default key policy\.
+ **kms:Decrypt** – \(Required\) Decrypts ciphertext\. Ciphertext is plaintext that has been previously encrypted\. This permission is included in the default key policy\.
+ **kms:ReEncrypt** – \(Optional\) Encrypts data on the server side with a new KMS key, without exposing the plaintext of the data on the client side\. The data is first decrypted and then re\-encrypted\. This permission is included in the default key policy\.
+ **kms:GenerateDataKeyWithoutPlaintext** – \(Required\) Returns a data encryption key encrypted under a KMS key\. This permission is included in the default key policy under **kms:GenerateDataKey\***\.
+ **kms:CreateGrant** – \(Required\) Adds a grant to a key to specify who can use the key and under what conditions\. Grants are alternate permission mechanisms to key policies\. For more information on grants, see [Using grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) in the *AWS Key Management Service Developer Guide\.* This permission is included in the default key policy\.
+ **kms:DescribeKey** – \(Required\) Provides detailed information about the specified KMS key\. This permission is included in the default key policy\.
+ **kms:ListAliases** – \(Optional\) Lists all of the key aliases in the account\. When you use the console to create an encrypted file system, this permission populates the list to select the KMS key\. We recommend using this permission to provide the best user experience\. This permission is included in the default key policy\.