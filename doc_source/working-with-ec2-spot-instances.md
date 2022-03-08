# Working with Amazon EC2 Spot Instances<a name="working-with-ec2-spot-instances"></a>

FSx for Lustre can be used with EC2 Spot Instances to significantly lower your Amazon EC2 costs\. A Spot Instance is an unused EC2 instance that is available for less than the On\-Demand price\. Amazon EC2 can interrupt your Spot Instance when the Spot price exceeds your maximum price, when the demand for Spot Instances rises, or when the supply of Spot Instances decreases\.

When Amazon EC2 interrupts a Spot Instance, it provides a Spot Instance interruption notice, which gives the instance a two\-minute warning before Amazon EC2 interrupts it\. For more information, see [Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\. 

To ensure that Amazon FSx file systems are unaffected by EC2 Spot Instances Interruptions, we recommend unmounting Amazon FSx file systems prior to terminating or hibernating EC2 Spot Instances\. For more information, see [Unmounting file systems](unmounting-fs.md)\. 

## Handling Amazon EC2 Spot Instance interruptions<a name="handling-ec2-spot-interruptions-in-fsx"></a>

 FSx for Lustre is a distributed file system where server and client instances cooperate to provide a performant and reliable file system\. They maintain a distributed and coherent state across both client and server instances\. FSx for Lustre servers delegate temporary access permissions to clients while they are actively doing I/O and caching file system data\. Clients are expected to reply in a short period of time when servers request them to revoke their temporary access permissions\. To protect the file system against misbehaving clients, servers can evict Lustre clients that do not respond after a few minutes\. To avoid having to wait multiple minutes for a non\-responding client to reply to the server request, it is important to cleanly unmount Lustre clients, especially before terminating EC2 Spot Instances\. 

 EC2 Spot sends termination notices 2 minutes in advance before shutting down an instance\. We recommend that you automate the process of cleanly unmounting Lustre clients before terminating EC2 Spot Instances\. 

**Example – Script to cleanly unmount terminating EC2 Spot Instances**  
This example script cleanly unmounts terminating EC2 Spot Instances by doing the following:  
+ Watches for Spot termination notices\.
+ When it receives a termination notice:
  + Stop applications that are accessing the file system\.
  + Unmounts the file system before the instance is terminated\.
You can adapt the script as needed, especially for gracefully shutting down your application\. For more information about best practices for handling Spot Instance interruptions, see [ Best practices for handling EC2 Spot Instance interruptions](http://aws.amazon.com/blogs/compute/best-practices-for-handling-ec2-spot-instance-interruptions/)\.  

```
#!/bin/bash

# TODO: Specify below the FSx mount point you are using
*FSXPATH=/fsx*

cd /

TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
if [ "$?" -ne 0 ]; then
    echo "Error running 'curl' command" >&2
    exit 1
fi

# Periodically check for termination
while sleep 5
do

    HTTP_CODE=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" -s -w %{http_code} -o /dev/null http://169.254.169.254/latest/meta-data/spot/instance-action)

    if [[ "$HTTP_CODE" -eq 401 ]] ; then
        # Refreshing Authentication Token
        TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 30")
        continue
    elif [[ "$HTTP_CODE" -ne 200 ]] ; then
        # If the return code is not 200, the instance is not going to be interrupted
        continue
    fi

    echo "Instance is getting terminated. Clean and unmount '$FSXPATH' ..."
    curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/spot/instance-action
    echo

    # Gracefully stop applications accessing the filesystem
    #
    # TODO*: Replace with the proper command to stop your application if possible*

    # Kill every process still accessing Lustre filesystem
    echo "Kill every process still accessing Lustre filesystem..."
    fuser -kMm -TERM "${FSXPATH}"; sleep 2
    fuser -kMm -KILL "${FSXPATH}"; sleep 2

    # Unmount FSx For Lustre filesystem
    if ! umount -c "${FSXPATH}"; then
        echo "Error unmouting '$FSXPATH'. Processes accessing it:" >&2
        lsof "${FSXPATH}"

        echo "Retrying..."
        continue
    fi

    # Start a graceful shutdown of the host
    shutdown now

done
```