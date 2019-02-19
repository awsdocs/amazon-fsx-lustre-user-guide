# Installing the Lustre Client<a name="install-lustre-client"></a>

To mount your Amazon FSx for Lustre file system from a Linux instance, first you need to install the open\-source Lustre client\. When you install the Lustre client, keep the following information in mind:
+ Amazon FSx for Lustre supports access from the Lustre client versions 2\.10\.5 and 2\.10\.6\.
+ The Lustre client version 2\.10\.5 supports CentOS 7\.5 and RedHat 7\.5\.
+ The Lustre client version 2\.10\.6 supports CentOS 7\.6, RedHat 7\.6, SUSE Linux 12 SP3, and Ubuntu 16\.04\.

------
#### [ CentOS 7\.5 or RedHat 7\.5  ]

**To install the Lustre client as an RPM package**

1. Open a terminal on your client\.

1. Download and install the Lustre client with the following commands\. The client comes in two packages that must be downloaded and installed\.

   ```
   sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7.5.1804/client/RPMS/x86_64/kmod-lustre-client-2.10.5-1.el7.x86_64.rpm
   sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7.5.1804/client/RPMS/x86_64/lustre-client-2.10.5-1.el7.x86_64.rpm
   ```

**Note**  
You might need to reboot your compute instance for the client to finish installing\.

------
#### [ CentOS 7\.6 or RedHat 7\.6  ]

**To install the Lustre client as an RPM package**

1. Open a terminal on your client\.

1. Download and install the Lustre client with the following commands\. The client comes in two packages that you need to download and install\.

   ```
   sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/el7/client/RPMS/x86_64/kmod-lustre-client-2.10.6-1.el7.x86_64.rpm
   sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/el7/client/RPMS/x86_64/lustre-client-2.10.6-1.el7.x86_64.rpm
   ```

**Note**  
You might need to reboot your compute instance for the client to finish installing\.

------
#### [ SUSE Linux 12 SP3  ]

**To install the Lustre client as an RPM package**

1. Open a terminal on your client\.

1. Download and install the Lustre client with the following commands\. The client comes in two packages that must be downloaded and installed\.

   ```
   sudo rpm -ivh https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/sles12sp3/client/RPMS/x86_64/lustre-client-kmp-default-2.10.6_k4.4.155_94.50-1.x86_64.rpm
   sudo rpm -ivh https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/sles12sp3/client/RPMS/x86_64/lustre-client-2.10.6-1.x86_64.rpm
   ```

**Note**  
You might need to reboot your compute instance for the client to finish installing\.

------
#### [ Ubuntu 16\.04 ]

**To install the Lustre client as a DEB package**

1. Open a terminal on your client\.

1. The Lustre client requires kernel 4\.4\.0\-131\-generic\. Determine which kernel is currently running on the compute instance using the following command:

   ```
   uname -r
   ```

   If the command returns `4.4.0-131-generic` then continue with [step 7](#install-lustre) to download the Lustre client\. If the result is NOT `4.4.0-131-generic` continue to the next step to install the supported kernel and headers\.

1. Install the supported kernel and associated Linux kernel headers with the following commands\.

   ```
   sudo apt-get install linux-image-4.4.0-131-generic
   sudo apt-get install -y linux-headers-4.4.0-131-generic
   ```

1. Once that completes, edit the `/etc/default/grub` file with the following command\.

   ```
   sudo sed -i 's/GRUB_DEFAULT=.\+/GRUB\_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 4.4.0-131-generic"/' /etc/default/grub
   ```

1. Update the file with the following command\.

   ```
   sudo update-grub
   ```

1. Reboot your instance with the following command\.

   ```
   sudo reboot
   ```

1. <a name="install-lustre"></a>Download the Lustre client with the following commands\. The client comes in two packages that must be downloaded\.

   ```
   wget https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/ubuntu1604/client/lustre-client-modules-4.4.0-131-generic_2.10.6-1_amd64.deb
   wget https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/ubuntu1604/client/lustre-utils_2.10.6-1_amd64.deb
   ```

1. Install the Lustre client packages with the following command\.

   ```
   sudo apt-get install -y ./lustre-*_2.10.6*.deb
   ```

------