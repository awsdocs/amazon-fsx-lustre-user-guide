# How Amazon FSx for Lustre Uses AWS KMS<a name="FSXKMS"></a>

Amazon FSx for Lustre integrates with AWS Key Management Service \(AWS KMS\) for key management for encrypting data at rest\. Amazon FSx uses customer master keys \(CMKs\) to encrypt your file system in the following way:
+ **Encrypting data at rest** – Amazon FSx for Lustre uses a single customer master key \(CMK\), either the AWS managed key for Amazon FSx or a custom CMK, to encrypt and decrypt file system data\. All scratch FSx for Lustre file systems are encrypted at rest with keys managed by the service\. Data is encrypted using an XTS\-AES\-256 block cipher\. Data is automatically encrypted before being written to the file system, and is automatically decrypted as it is read\. The keys used to encrypt scratch file systems at\-rest are unique per file system and destroyed after the file system is deleted\. For persistent file systems, you choose the CMK used to encrypt and decrypt data, either the AWS managed key for Amazon FSx or a custom CMK\. You specify which key to use when you create a persistent file system\. You can enable, disable, or revoke grants on this CMK\. This CMK can be one of the two following types:
  + **AWS managed keys for Amazon FSx** – This is the default key\. You're not charged to create and store this type of key, but there are usage charges\. For more information, see [AWS Key Management Service pricing](https://aws.amazon.com/kms/pricing/)\.
  + **Customer\-managed keys** – This is the most flexible master key to use, because you can configure its key policies and grants for multiple users or services\. For more information on creating CMKs, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the* AWS Key Management Service Developer Guide\.*

    If you use a CMK as your master key for file data encryption and decryption, you can enable key rotation\. When you enable key rotation, AWS KMS automatically rotates your key once per year\. Additionally, with a CMK, you can choose when to disable, re\-enable, delete, or revoke access to your CMK at any time\. 

**Important**  
Amazon FSx accepts only symmetric CMKs\. You can't use asymmetric CMKs with Amazon FSx\.

## Amazon FSx Key Policies for AWS KMS<a name="FSxKMSPolicy"></a>

Key policies are the primary way to control access to CMKs\. For more information on key policies, see [Using Key Policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide\. *The following list describes all the AWS KMS–related permissions supported by Amazon FSx for encrypted at rest file systems:
+ **kms:Encrypt** – \(Optional\) Encrypts plaintext into ciphertext\. This permission is included in the default key policy\.
+ **kms:Decrypt** – \(Required\) Decrypts ciphertext\. Ciphertext is plaintext that has been previously encrypted\. This permission is included in the default key policy\.
+ **kms:ReEncrypt** – \(Optional\) Encrypts data on the server side with a new customer master key \(CMK\), without exposing the plaintext of the data on the client side\. The data is first decrypted and then re\-encrypted\. This permission is included in the default key policy\.
+ **kms:GenerateDataKeyWithoutPlaintext** – \(Required\) Returns a data encryption key encrypted under a CMK\. This permission is included in the default key policy under **kms:GenerateDataKey\***\.
+ **kms:CreateGrant** – \(Required\) Adds a grant to a key to specify who can use the key and under what conditions\. Grants are alternate permission mechanisms to key policies\. For more information on grants, see [Using Grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) in the *AWS Key Management Service Developer Guide\.* This permission is included in the default key policy\.
+ **kms:DescribeKey** – \(Required\) Provides detailed information about the specified customer master key\. This permission is included in the default key policy\.
+ **kms:ListAliases** – \(Optional\) Lists all of the key aliases in the account\. When you use the console to create an encrypted file system, this permission populates the **Select KMS master key** list\. We recommend using this permission to provide the best user experience\. This permission is included in the default key policy\.

Here is an example CMK policy that you can use to allow the FSx service to use it for the encryption of your FSx filesystem. Remember, you will need to adjust the region used in the `kms:ViaService` condition in the policy below to match the region of your FSx filesystem and the CMK, you will also need to replace the account number `111122223333` with the account number where you are using the FSx filesystem:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Allow access through FSx for all principals in the account that are authorized to use FSx",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": [
                "kms:Encrypt",
                "kms:Decrypt",
                "kms:ReEncrypt*",
                "kms:GenerateDataKey*",
                "kms:CreateGrant",
                "kms:ListGrants",
                "kms:DescribeKey"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "kms:ViaService": "fsx.us-east-1.amazonaws.com",
                    "kms:CallerAccount": "111122223333"
                }
            }
        },
        {
            "Sid": "Allow direct access to key metadata to the account",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::111122223333:root"
            },
            "Action": [
                "kms:Describe*",
                "kms:Get*",
                "kms:List*",
                "kms:RevokeGrant"
            ],
            "Resource": "*"
        }
    ]
}
```
