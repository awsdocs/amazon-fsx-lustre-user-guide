# Walkthrough: Attaching POSIX permissions when uploading objects into an Amazon S3 bucket<a name="attach-s3-posix-permissions"></a>

The following procedure walks you through the process of uploading objects into Amazon S3 with POSIX permissions\. Doing so allows you to import the POSIX permissions when you create an Amazon FSx file system that is linked to that S3 bucket\.

**To upload objects with POSIX permissions to Amazon S3**

1. From your local computer or machine, use the following example commands to create a test directory \(`s3cptestdir`\) and file \(`s3cptest.txt`\) that will be uploaded to the S3 bucket\.

   ```
   $ mkdir s3cptestdir
   $ echo "S3cp metadata import test" >> s3cptestdir/s3cptest.txt
   $ ls -ld s3cptestdir/ s3cptestdir/s3cptest.txt
   drwxr-xr-x 3 500 500 96 Jan 8 11:29 s3cptestdir/
   -rw-r--r-- 1 500 500 26 Jan 8 11:29 s3cptestdir/s3cptest.txt
   ```

   The newly created file and directory have a file owner user ID \(UID\) and group ID \(GID\) of 500 and permissions as shown in the preceding example\.

1. Call the Amazon S3 API to create the directory `s3cptestdir` with metadata permissions\. You must specify the directory name with a trailing slash \(`/`\)\. For information about supported POSIX metadata, see [POSIX metadata support for data repositories](overview-dra-data-repo.md#posix-metadata-support)\.

   Replace `bucket_name` with the actual name of your S3 bucket\.

   ```
   $ aws s3api put-object --bucket bucket_name --key s3cptestdir/ --metadata '{"user-agent":"aws-fsx-lustre" , \
         "file-atime":"1595002920000000000ns" , "file-owner":"500" , "file-permissions":"0100664","file-group":"500" , \
         "file-mtime":"1595002920000000000ns"}'
   ```

1. Verify the POSIX permissions are tagged to S3 object metadata\.

   ```
   $ aws s3api head-object --bucket bucket_name --key s3cptestdir/
   {
       "AcceptRanges": "bytes",
       "LastModified": "Fri, 08 Jan 2021 17:32:27 GMT",
       "ContentLength": 0,
       "ETag": "\"d41d8cd98f00b204e9800998ecf8427e\"",
       "VersionId": "bAlhCoWq7aIEjc3R6Myc6UOb8sHHtJkR",
       "ContentType": "binary/octet-stream",
       "Metadata": {
           "user-agent": "aws-fsx-lustre",
           "file-atime": "1595002920000000000ns",
           "file-owner": "500",
           "file-permissions": "0100664",
           "file-group": "500",
           "file-mtime": "1595002920000000000ns"
       }
   }
   ```

1. Upload the test file \(created in step 1\) from your computer to the S3 bucket with metadata permissions\.

   ```
   $ aws s3 cp s3cptestdir/s3cptest.txt s3://bucket_name/s3cptestdir/s3cptest.txt \
         --metadata '{"user-agent":"aws-fsx-lustre" , "file-atime":"1595002920000000000ns" , \
         "file-owner":"500" , "file-permissions":"0100664","file-group":"500" , "file-mtime":"1595002920000000000ns"}'
   ```

1. Verify the POSIX permissions are tagged to S3 object metadata\.

   ```
   $ aws s3api head-object --bucket bucket_name --key s3cptestdir/s3cptest.txt
   {
       "AcceptRanges": "bytes",
       "LastModified": "Fri, 08 Jan 2021 17:33:35 GMT",
       "ContentLength": 26,
       "ETag": "\"eb33f7e1f44a14a8e2f9475ae3fc45d3\"",
       "VersionId": "w9ztRoEhB832m8NC3a_JTlTyIx7Uzql6",
       "ContentType": "text/plain",
       "Metadata": {
           "user-agent": "aws-fsx-lustre",
           "file-atime": "1595002920000000000ns",
           "file-owner": "500",
           "file-permissions": "0100664",
           "file-group": "500",
           "file-mtime": "1595002920000000000ns"
       }
   }
   ```

1. Verify permissions on the Amazon FSx file system linked to the S3 bucket\.

   ```
   $ sudo lfs df -h /fsx
   UUID                       bytes        Used   Available Use% Mounted on
   3rnxfbmv-MDT0000_UUID       34.4G        6.1M       34.4G   0% /fsx[MDT:0]
   3rnxfbmv-OST0000_UUID        1.1T        4.5M        1.1T   0% /fsx[OST:0]
    
   filesystem_summary:         1.1T        4.5M        1.1T   0% /fsx
    
   $ cd /fsx/s3cptestdir/
   $ ls -ld s3cptestdir/
   drw-rw-r-- 2 500 500 25600 Jan  8 17:33 s3cptestdir/
   
   $ ls -ld s3cptestdir/s3cptest.txt
   -rw-rw-r-- 1 500 500 26 Jan 8 17:33 s3cptestdir/s3cptest.txt
   ```

Both the `s3cptestdir` directory and the `s3cptest.txt` file have POSIX permissions imported\.