# Installing the Lustre Client<a name="install-lustre-client"></a>

To mount your Amazon FSx for Lustre file system from a Linux instance, first you need to install the open\-source Lustre client\. When you install the Lustre client, keep the following information in mind:
+ Amazon FSx for Lustre supports access from the Lustre client version 2\.10\.5\.
+ The Lustre client supports CentOS 7, CentOS 7\.5, RedHat 7, RedHat 7\.5, SUSE Linux 12 SP3, and Ubuntu 16\.04\.

------
#### [ CentOS 7\.5 or RedHat 7\.5  ]

**To install the Lustre client as an RPM package**

1. Open a terminal on your client\.

1. If you haven't done so already, install the `wget` package with the following command:

   ```
   sudo yum -y install wget
   ```

1. Download the Lustre client with the following commands\. The client comes in two packages that must be downloaded\.

   ```
   wget https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7.5.1804/client/RPMS/x86_64/kmod-lustre-client-2.10.5-1.el7.x86_64.rpm
   wget https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7.5.1804/client/RPMS/x86_64/lustre-client-2.10.5-1.el7.x86_64.rpm
   ```

1. Install the packages with the following command\.

   ```
   sudo yum localinstall -y *lustre-client-2.10.5*.rpm
   ```

**Note**  
You might need to reboot your compute instance for the client to finish installing\.

------
#### [ CentOS 7\.0 or RedHat 7\.0  ]

**To install the Lustre client as an RPM package**

1. Open a terminal on your client\.

1. If you haven't done so already, install the `wget` package with the following command:

   ```
   sudo yum -y install wget
   ```

1. Download the Lustre client with the following commands\. The client comes in two packages that must be downloaded\.

   ```
   wget https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7/client/RPMS/x86_64/kmod-lustre-client-2.10.5-1.el7.x86_64.rpm
   wget https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7/client/RPMS/x86_64/lustre-client-2.10.5-1.el7.x86_64.rpm
   ```

1. Install the packages with the following command\.

   ```
   sudo yum localinstall -y *lustre-client-2.10.5*.rpm
   ```

**Note**  
You might need to reboot your compute instance for the client to finish installing\.

------
#### [ SUSE Linux 12 SP3  ]

**To install the Lustre client as an RPM package**

1. Open a terminal on your client\.

1. If you haven't done so already, install the `wget` package with the following command:

   ```
   sudo yum -y install wget
   ```

1. Download the Lustre client with the following commands\. The client comes in two packages that must be downloaded\.

   ```
   wget https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/sles12sp3/client/RPMS/x86_64/lustre-client-2.10.5-1.x86_64.rpm
   wget https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/sles12sp3/client/RPMS/x86_64/lustre-client-kmp-default-2.10.5_k4.4.132_94.33-1.x86_64.rpm
   ```

1. Install the packages with the following command\.

   ```
   sudo rpm -ivh lustre-client*-2.10.5*.rpm
   ```

**Note**  
You might need to reboot your compute instance for the client to finish installing\.

------
#### [ Ubuntu 16\.04 ]

**To install the Lustre client as a DEB package**

1. Open a terminal on your client\.

1. Install the supported kernel with the following command:

   ```
   sudo apt-get install linux-image-4.4.0-131-generic
   ```

1. Once that completes, edit the `/etc/default/grub` file with the following command:

   ```
   sudo vim /etc/default/grub
   ```

1. Make the following changes in the file:

   ```
   GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 4.4.0-131-generic"
   ```

1. Update the file with the following command:

   ```
   sudo update-grub
   ```

1. Reboot your instance with the following command:

   ```
   sudo reboot
   ```

1. Download the Lustre client with the following commands\. The client comes in two packages that must be downloaded\.

   ```
   wget https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/ubuntu1604/client/lustre-client-modules-4.4.0-131-generic_2.10.5-1_amd64.deb
   wget https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/ubuntu1604/client/lustre-utils_2.10.5-1_amd64.deb
   ```

1. Install the packages with the following command\.

   ```
   sudo apt-get install -y
   ./lustre-client-modules-4.4.0-131-generic_2.10.5-1_amd64.deb
   ./lustre-utils_2.10.5-1_amd64.deb
   ```

------