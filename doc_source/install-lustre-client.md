# Installing the Lustre Client<a name="install-lustre-client"></a>

To mount your Amazon FSx for Lustre file system from a Linux instance, first you need to install the open\-source Lustre client\. Amazon FSx for Lustre supports access from the Lustre client versions 2\.10\.5 and 2\.10\.6\.

Then, depending on your operating system version, use one of the procedures following\.

## To install the Lustre client as an RPM package \(Amazon Linux\)<a name="install-lustre-client-amazon-linux"></a>

1. Open a terminal on your client\.

1. Determine which kernel is currently running on the compute instance\. The Lustre client requires Amazon Linux kernel 4\.14, version 104 or higher\. Run the following command to determine which kernel is running\.

   ```
   uname -r
   ```

   If the command returns `4.14.104-78.84.amzn1.x86_64` or a higher version of 4\.14, continue to step 4 to download and install the Lustre client\. If the result is less than 4\.14\.104, continue to the next step to install the supported kernel\.

1.  Update the kernel and reboot your Amazon EC2 instance by running the following command\. 

   ```
   sudo yum -y update kernel && sudo reboot
   ```

1. Download and install the Lustre client with the following command\.

   ```
   sudo yum install -y lustre-client
   ```

## To install the Lustre client as an RPM package \(Amazon Linux 2\)<a name="install-lustre-client-amazon-linux-2"></a>

1. Open a terminal on your client\.

1. Determine which kernel is currently running on the compute instance\. The Lustre client requires Amazon Linux kernel 4\.14, version 104 or higher\. Run the following command to determine which kernel is running\.

   ```
   uname -r
   ```

   If the command returns `4.14.104-95.84.amzn2.x86_64` or a higher version of 4\.14, continue to step 4 to download and install the Lustre client\. If the result is less than 4\.14\.104, continue to the next step to install the supported kernel\.

1.  Update the kernel and reboot your EC2 instance by running the following command\. 

   ```
   sudo yum -y update kernel && sudo reboot
   ```

1. Download and install the Lustre client with the following command\.

   ```
   sudo amazon-linux-extras install -y lustre2.10
   ```

## To install the Lustre client as an RPM package \(CentOS and RedHat 7\.5 or 7\.6\)<a name="install-lustre-client-amazon-centos-7.5"></a>

1. Open a terminal on your client\.

1. Determine which kernel is currently running on the compute instance with the following command\.

   ```
   uname -r
   ```
   + If the instance is running kernel version `3.10.0-862.*`, then continue with [step 3](#lustre-2.10.5) to download and install the Lustre 2\.10\.5 client\.
   + If the instance is running kernel version `3.10.0-957.*`, then continue with [step 4](#lustre-2.10.6) to download and install the Lustre 2\.10\.6 client\.

1. <a name="lustre-2.10.5"></a>For instances running linux kernel 3\.10\.0\-862\.\* – download and install the Lustre 2\.10\.5 client with the following commands\. The client comes in two packages that must be downloaded and installed\.

   ```
   sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7/client/RPMS/x86_64/kmod-lustre-client-2.10.5-1.el7.x86_64.rpm
   sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.5/el7/client/RPMS/x86_64/lustre-client-2.10.5-1.el7.x86_64.rpm
   ```

1. <a name="lustre-2.10.6"></a>For instances running linux kernel 3\.10\.0\-957\.\* – download and install the Lustre 2\.10\.6 client with the following commands\. The client comes in two packages that you need to download and install\.

   ```
   sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/el7/client/RPMS/x86_64/kmod-lustre-client-2.10.6-1.el7.x86_64.rpm
   sudo yum -y install https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/el7/client/RPMS/x86_64/lustre-client-2.10.6-1.el7.x86_64.rpm
   ```

**Note**  
You might need to reboot your compute instance for the client to finish installing\.

## To install the Lustre client as an RPM package \(SUSE Linux 12 SP3\)<a name="install-lustre-client-SUSE-Linux"></a>

1. Open a terminal on your client\.

1. Download and install the Lustre client with the following commands\. The client comes in two packages that must be downloaded and installed\.

   ```
   sudo rpm -ivh https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/sles12sp3/client/RPMS/x86_64/lustre-client-kmp-default-2.10.6_k4.4.155_94.50-1.x86_64.rpm
   sudo rpm -ivh https://downloads.whamcloud.com/public/lustre/lustre-2.10.6/sles12sp3/client/RPMS/x86_64/lustre-client-2.10.6-1.x86_64.rpm
   ```

**Note**  
You might need to reboot your compute instance for the client to finish installing\.

## To install the Lustre client as a \.deb package \(Ubuntu 16\.04\)<a name="install-lustre-client-Ubuntu-16"></a>

1. Open a terminal on your client\.

1. Determine which kernel is currently running on the compute instance\. The Lustre client requires kernel 4\.4\.0\-131\-generic\. Run the following command to determine which kernel is running\.

   ```
   uname -r
   ```

   If the command returns `4.4.0-131-generic`, then continue with [step 7](#install-lustre) to download the Lustre client\. If the result is not `4.4.0-131-generic`, continue to the next step to install the supported kernel and headers\.

1. Install the supported kernel and associated Linux kernel headers with the following commands\.

   ```
   sudo apt-get install linux-image-4.4.0-131-generic
   sudo apt-get install -y linux-headers-4.4.0-131-generic
   ```

1. After installation completes, edit the `/etc/default/grub` file with the following command\.

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