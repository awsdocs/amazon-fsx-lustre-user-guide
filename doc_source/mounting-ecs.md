# Mounting from Amazon Elastic Container Service<a name="mounting-ecs"></a>

You can access your FSx for Lustre file system from an Amazon Elastic Container Service \(Amazon ECS\) Docker container on an Amazon EC2 instance\. You can do so by using either of the following options:

1. By mounting your FSx for Lustre file system from the Amazon EC2 instance that is hosting your Amazon ECS tasks, and exporting this mount point to your containers\.

1. By mounting the file system directly inside your task container\.

For more information about Amazon ECS, see [What is Amazon Elastic Container Service?](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/Welcome.html) in the *Amazon Elastic Container Service Developer Guide*\.

We recommend using option 1 \([Mounting from an Amazon EC2 instance hosting Amazon ECS tasks](#mounting-from-ecs-ec2)\) because it provides better resource use, especially if you start many containers \(more than five\) on the same EC2 instance or if your tasks are short\-lived \(less than 5 minutes\)\. 

Use option 2 \([Mounting from a Docker container](#mounting-from-docker)\), if you're unable to configure the EC2 instance, or if your application requires the container's flexibility\.

**Note**  
Mounting FSx for Lustre on an AWS Fargate launch type isn't supported\.

The following sections describe the procedures for each of the options for mounting your FSx for Lustre file system from an Amazon ECS container\.

**Topics**
+ [Mounting from an Amazon EC2 instance hosting Amazon ECS tasks](#mounting-from-ecs-ec2)
+ [Mounting from a Docker container](#mounting-from-docker)

## Mounting from an Amazon EC2 instance hosting Amazon ECS tasks<a name="mounting-from-ecs-ec2"></a>

This procedure shows how you can configure an Amazon ECS on EC2 instance to locally mount your FSx for Lustre file system\. The procedure uses `volumes` and `mountPoints` container properties to share the resource and make this file system accessible to locally running tasks\. For more information, see [Launching an Amazon ECS Container Instance](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/launch_container_instance.html) in the *Amazon Elastic Container Service Developer Guide*\. 

This procedure is for an Amazon ECS\-Optimized Amazon Linux 2 AMI\. If you are using another Linux distribution, see [Installing the Lustre client](install-lustre-client.md)\.

**To mount your file system from Amazon ECS on an EC2 instance**

1. When launching Amazon ECS instances, either manually or using an Auto Scaling group, add the lines in the following code example to the end of the **User data** field\. Replace the following items in the example:
   + Replace `file_system_dns_name` with the actual file system's DNS name\.
   + Replace `mountname` with the file system's mount name\.
   + Replace `mountpoint` with the file system's mount point, which you need to create\.

   ```
   #!/bin/bash
   
   ...<existing user data>...
   
   fsx_dnsname=file_system_dns_name
   fsx_mountname=mountname
   fsx_mountpoint=mountpoint
   amazon-linux-extras install -y lustre2.10
   mkdir -p "$fsx_mountpoint"
   mount -t lustre ${fsx_dnsname}@tcp:/${fsx_mountname} ${fsx_mountpoint} -o noatime,flock
   ```

1. When creating your Amazon ECS tasks, add the following `volumes` and `mountPoints` container properties in the JSON definition\. Replace `mountpoint` with the file system's mount point \(such as `/mnt/fsx`\)\.

   ```
   {
       "volumes": [
              {
                    "host": {
                         "sourcePath": "mountpoint"
                    },
                    "name": "Lustre"
              }
       ],
       "mountPoints": [
              {
                    "containerPath": "mountpoint",
                    "sourceVolume": "Lustre"
              }
       ],
   }
   ```

## Mounting from a Docker container<a name="mounting-from-docker"></a>

The following procedure shows how you can configure an Amazon ECS task container to install the `lustre-client` package and mount your FSx for Lustre file system in it\. The procedure uses an Amazon Linux \(`amazonlinux`\) Docker image, but a similar approach can work for other distributions\.

**To mount your file system from a Docker container**

1. Install the `lustre-client` package and mount your FSx for Lustre file system with the `command` property\. Replace the following items in the example:
   + Replace `file_system_dns_name` with the actual file system's DNS name\.
   + Replace `mountname` with the file system's mount name\.
   + Replace `mountpoint` with the file system's mount point\.

   ```
   "command": [
     "/bin/sh -c \"amazon-linux-extras install -y lustre2.10; mount -t lustre file_system_dns_name@tcp:/mountname mountpoint -o noatime,flock;\""
   ],
   ```

1. Add `SYS_ADMIN` capability to your container to authorize it to mount your FSx for Lustre file system, using the `linuxParameters` property\.

   ```
   "linuxParameters": {
     "capabilities": {
         "add": [
           "SYS_ADMIN"
         ]
      }
   }
   ```