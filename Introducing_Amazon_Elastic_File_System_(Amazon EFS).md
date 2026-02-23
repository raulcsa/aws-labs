Task 1: Creating a security group to access your EFS file system
The security group that you associate with a mount target must allow inbound access for TCP on port 2049 for Network File System (NFS). This is the security group that you will now create, configure, and attach to your EFS mount targets.

At the top of the AWS Management Console, in the search box, search for and choose EC2.

In the navigation pane on the left, choose Security Groups.

Copy the Security group ID of the EFSClient security group to your text editor.

The Group ID should look similar to sg-03727965651b6659b.

<img width="1679" height="830" alt="image" src="https://github.com/user-attachments/assets/b6a2cf26-0c0a-4d0e-b779-fa286dd74d82" />

Choose Create security group then configure:

Security group name: EFS Mount Target

Description: Inbound NFS access from EFS clients

VPC: Lab VPC

<img width="1665" height="438" alt="image" src="https://github.com/user-attachments/assets/15308efb-c822-411d-b2ba-e5ed40036e16" />


Under the Inbound rules section, choose Add rule then configure:

Type: NFS

Source:

Custom

In the Custom box, paste the security group's Security group ID that you copied to your text editor

<img width="1635" height="248" alt="image" src="https://github.com/user-attachments/assets/01b73ecc-b1ae-437b-b33f-2fb1906927a5" />


Choose Create security group.

<img width="1430" height="541" alt="image" src="https://github.com/user-attachments/assets/b066622a-a90b-43b1-b48e-2c2e4822570a" />


Task 2: Creating an EFS file system
EFS file systems can be mounted to multiple EC2 instances that run in different Availability Zones in the same Region. These instances use mount targets that are created in each Availability Zone to mount the file system by using standard NFSv4.1 semantics. You can mount the file system on instances in only one virtual private cloud (VPC) at a time. Both the file system and the VPC must be in the same Region.

At the top of the AWS Management Console, in the search box, search for and choose EFS.

Choose Create file system.

In the Create file system window, choose Customize.

On Step 1:

Uncheck  Enable Automatic backups.

Lifecycle management:

for Transition into IA  Select None.

In the Tags optional section, configure:

Key: Name

Value: My First EFS File System

<img width="1535" height="778" alt="image" src="https://github.com/user-attachments/assets/edb9242d-2c4e-49b6-92f0-03df92ccb829" />

Choose Next.

For VPC, select Lab VPC.

Detach the default security group from each Availability Zone mount target by choosing the  check box on each default security group.

Attach the EFS Mount Target security group to each Availability Zone mount target by choosing EFS Mount Target for each Availability Zone.

Choose Next.

<img width="1359" height="698" alt="image" src="https://github.com/user-attachments/assets/14e0d1db-bd8f-4e57-888c-2a6faa951c78" />

On Step 3, choose Next.

On Step 4:

Review your configuration.

Choose Create.

 Congratulations! You have created a new EFS file system in your Lab VPC and mount targets in each Lab VPC subnet. In a few seconds, the File system state of the file system will change to Available, followed by the mount targets 2–3 minutes later.
<img width="1674" height="489" alt="image" src="https://github.com/user-attachments/assets/f4d872c0-7976-47de-9e98-71dc502b9902" />


Proceed to the next step after the Mount target state for each mount target changes to Available. Choose the screen refresh button after 2–3 minutes to check its progress.


Task 3: Connecting to your EC2 instance
In this task, you will connect to your EC2 instance by using AWS Systems Manager Session Manager sign-in URL.

To connect to the EC2 instance, from the top of this page, choose i AWS Details  and copy the value for InstanceSessionURL .

Paste it into the new browser tab or window to  connect to the EC2 instance using AWS Systems Manager Session Manager.

<img width="1679" height="179" alt="image" src="https://github.com/user-attachments/assets/afd83d26-0a57-4ee2-a1b9-3080ce33505d" />

You should now be connected to the instance.

 

Task 4: Creating a new directory and mounting the EFS file system
 Amazon EFS supports the NFSv4.1 and NFSv4.0 protocols when it mounts your file systems on EC2 instances. Though NFSv4.0 is supported, we recommend that you use NFSv4.1. When you mount your EFS file system on your EC2 instance, you must also use an NFS client that supports your chosen NFSv4 protocol. The EC2 instance that was launched as a part of this lab includes an NFSv4.1 client, which is already installed on it.

In your EC2 terminal session, run the following command to install the required utilities:

sudo su -l ec2-user
sudo yum install -y amazon-efs-utils
Tip: to paste into the terminal in the browser, place your cursor just to the right of the command prompt and right-click to see the paste option.
<img width="1662" height="687" alt="image" src="https://github.com/user-attachments/assets/284ffa21-3c4c-4ec9-8744-d3875f763b62" />

Run the following command to create directory for mount: sudo mkdir efs.
<img width="428" height="56" alt="image" src="https://github.com/user-attachments/assets/d781fd41-2b83-4a10-aab0-64d92158a77f" />


At the top of the AWS Management Console, in the search box, search for and choose EFS. 

Choose My First EFS File System.

In the Amazon EFS Console, on the top right corner of the page, choose Attach to open the Amazon EC2 mount instructions.

<img width="1679" height="558" alt="image" src="https://github.com/user-attachments/assets/1c87d7c1-47a2-4278-8482-fe4f8074ec32" />


In your EC2 terminal session, Copy and run the entire command in the Using the NFS client section.

The mount command should look similar to this example:

sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-bce57914.efs.us-west-2.amazonaws.com:/ efs

 The provided sudo mount... command uses the default Linux mount options.

Get a full summary of the available and used disk space usage by entering:

sudo df -hT

<img width="1659" height="194" alt="image" src="https://github.com/user-attachments/assets/881b3e88-9e51-4d7a-8833-476cf05fc4c8" />


This following screenshot is an example of the output from the following disk filesystem command: 

Following image shows disk space occupied by all drives inlcluding the new one mounted

Notice the Type and Size of your mounted EFS file system, similar to the following.

fs-0e2e45d50de5916b3.efs.us-east-1.amazonaws.com:/ nfs4      8.0E     0  8.0E   0% /home/ec2-user/efs

 

Task 5: Examining the performance behavior of your new EFS file system
Examining the performance by using Flexible IO
 Flexible IO (fio) is a synthetic I/O benchmarking utility for Linux. It is used to benchmark and test Linux I/O subsystems. During boot, fio was automatically installed on your EC2 instance.

Examine the write performance characteristics of your file system by entering:

sudo fio --name=fio-efs --filesize=10G --filename=./efs/fio-efs-test.img --bs=1M --nrfiles=1 --direct=1 --sync=0 --rw=write --iodepth=200 --ioengine=libaio
 The fio command will take few minutes to complete. The output should look like the example in the following screenshot. Make sure that you examine the output of your fio command, specifically the summary status information for this WRITE test.

Following image shows the output for fio command 

<img width="1658" height="532" alt="image" src="https://github.com/user-attachments/assets/428b7c8b-aa3e-4407-84b5-e4a92434e453" />



Monitoring performance by using Amazon CloudWatch
At the top of the AWS Management Console, in the search box, search for and choose CloudWatch. 

In the navigation pane on the left, choose All Metrics.

In the All metrics tab, choose EFS.

Choose File System Metrics.

Select the row that has the PermittedThroughput Metric Name.

 You might need to wait 2–3 minutes and refresh the screen several times before all available metrics, including PermittedThroughput, calculate and populate.

On the graph, If you do not see the line graph, adjust the time range of the graph down to 1h to display the period during which you ran the fio command.

Following graph shows the performance of the efs in terms of Premitted Throughput 

Note the Peak Throughput Value on the Y-axis (Bytes/Second) line in the graph. The value should be around 3G.
<img width="1414" height="437" alt="image" src="https://github.com/user-attachments/assets/29b6fcd8-62a6-4797-9e8f-0e6aeca8a52c" />


The throughput of Amazon EFS scales as the file system grows. File-based workloads are typically spiky. They drive high levels of throughput for short periods of time, and low levels of throughput the rest of the time. Because of this behavior, Amazon EFS is designed to burst to high throughput levels for periods of time.

In the All metrics tab, uncheck the box for PermittedThroughput.

Select the check box for DataWriteIOBytes.

 If you do not see DataWriteIOBytes in the list of metrics, use the File System Metrics search to find it.

Choose the Graphed metrics tab.

On the Statistics column, select Sum.

On the Period column, select 1 Minute.

Note the the peak value, which is around 7.6G. Take this number (in bytes) and divide it by the duration in seconds (60 seconds). The result gives you the write throughput (B/s) of your file system during your test.

Following graph shows the write throughput of the efs mounted
<img width="1430" height="502" alt="image" src="https://github.com/user-attachments/assets/94662a5c-f3ad-4cb4-8d50-d7bb55aadf35" />


The throughput that is available to a file system scales as a file system grows. All file systems deliver a consistent baseline performance of 50 MiB/s per TiB of storage. Also, all file systems (regardless of size) can burst to 100 MiB/s. File systems that are larger than 1T B can burst to 100 MiB/s per TiB of storage. As you add data to your file system, the maximum throughput that is available to the file system scales linearly and automatically with your storage.

File system throughput is shared across all EC2 instances that are connected to a file system. For more information about performance characteristics of your EFS file system, see the documentation link in the resources section.

With EFS you can also create access points for application-specific entry points into an EFS file system to provide secured access to shared datasets. Access points can enforce a user identity, including the user's POSIX groups, for all file system requests that are made through the access point. Refer to the section at the bottom for additional information.
