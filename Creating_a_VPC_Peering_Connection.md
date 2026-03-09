# Creating a VPC Peering Connection
Lab overview and objectives
You need to connect your virtual private clouds (VPCs) when you must transfer data between them. This lab shows you how to create a private VPC peering connection between two VPCs.

After completing this lab, you should be able to do the following:

Create a VPC peering connection.  

Configure route tables to use the VPC peering connection.

Enable VPC Flow Logs to provide insight on the data moving across the network.

Test a peering connection.

Analyze the VPC flow logs.

At the end of this lab, your architecture will look like the following example:

Following architecture diagram shows the final state of infrastructure components with peering connection setup between VPCs

<img width="819" height="406" alt="image" src="https://github.com/user-attachments/assets/587a4dfe-1e71-4396-be8f-da506201dc87" />

## Task 1: Creating a VPC peering connection
Your task is to create a VPC peering connection between two VPCs.

A VPC peering connection is a one-to-one networking connection between two VPCs that enables you to route traffic between them privately. Instances in either VPC can communicate with each other like they are in the same network. You can create a VPC peering connection between your own VPCs, in a VPC in another AWS account, or with a VPC in a different AWS Region.

Two VPCs are provided as part of this lab: Lab VPC and Shared VPC. Lab VPC has an inventory application that runs on an Amazon Elastic Compute Cloud (Amazon EC2) instance in a public subnet. Shared VPC has a database instance that runs in a private subnet.

<img width="819" height="406" alt="image" src="https://github.com/user-attachments/assets/587a4dfe-1e71-4396-be8f-da506201dc87" />

Following architecture diagram shows the infrastructure components available to learner at the begining of the lab

On the AWS Management Console, in the Search bar at the top, enter and choose VPC to open the VPC Management Console.

In the left navigation pane, choose Peering connections. 

Choose Create peering connection and configure the following settings:

Name - optional: Lab-Peer

VPC ID (Requester): Lab VPC

VPC ID (Accepter): Shared VPC

Choose Create peering connection.

<img width="1497" height="837" alt="image" src="https://github.com/user-attachments/assets/f09aa18d-5524-420a-b815-7512c7da236e" />

When a VPC peering connection is created, the target VPC must accept the connection request because it might be owned by a different account. Alternatively, the user that creates the peering connection might not have permission to accept the connection request for the target VPC. However, in this lab, you will accept the connection yourself.

On the next screen, from the Actions dropdown list, choose Accept request.

<img width="1456" height="636" alt="image" src="https://github.com/user-attachments/assets/0b738b67-b3f9-44e0-9e38-86dd051397dc" />

## Task 2: Configuring route tables
You will now update the route tables in both VPCs to send traffic from Lab VPC to the peering connection for Shared VPC.

Following diagram shows route tables with Peering Connections

<img width="756" height="242" alt="image" src="https://github.com/user-attachments/assets/f1ff2e99-f8d1-4872-8095-009cc007c5be" />

In the left navigation pane, choose Route Tables.

Select  Lab Public Route Table (for Lab VPC).

You will configure the Public Route Table associated with Lab VPC. If the destination IP address falls in the range of Shared VPC, the Public Route Table will send traffic to the peering connection.

In the Routes tab, choose Edit routes, and then configure the following settings:

Choose Add route.

Destination: 10.5.0.0/16 (The setting is the Classless Inter-Domain Route, or CIDR, block range of Shared VPC.)

Target: Choose Peering Connection from the dropdown list, and then from the search bar, choose Lab-Peer.

Choose Save changes.

<img width="1655" height="480" alt="image" src="https://github.com/user-attachments/assets/de331803-6de2-4915-991a-c8280573ca6d" />

You will now configure the reverse flow for traffic that comes from Shared VPC and goes to Lab VPC.

Go back to Route tables and select  Shared-VPC Route Table. If the check boxes for any other route tables are selected, clear them.

This route table is for Shared VPC. You will now configure it to send traffic to the peering connection if the destination IP address falls in the range of Lab VPC.

In the Routes tab, choose Edit routes, and then configure the following settings:

Choose Add route.

Destination: 10.0.0.0/16 (This setting is the CIDR block range of Lab VPC.)

Target: Choose Peering Connection from the dropdown list, and then from the search bar, choose Lab-Peer.

Choose Save changes.

<img width="1679" height="409" alt="image" src="https://github.com/user-attachments/assets/196ff884-56a2-4a2e-ac72-f2a71cc51044" />

The route tables are now configured to send traffic through the peering connection when the traffic is destined for the other VPC.

## Task 3: Enabling VPC Flow Logs to provide insight on the data moving across the network
Now that the peering connection is established between the two VPCs, you setup VPC Flow Logs to monitor the network traffic moving between two networks. In this lab, you setup VPC Flow Logs to monitor the traffic on the VPC hosting database.

In the left navigation pane, choose Your VPCs, and then select Shared VPC.

From the bottom panel, choose the Flow logs tab.

Choose Create flow log.

<img width="1637" height="686" alt="image" src="https://github.com/user-attachments/assets/443ed7dc-01b3-4bbc-8a07-bd52b09db933" />

On the Create flow log page, configure the following settings:

Name (optional): SharedVPCLogs

Maximum aggregation interval: 1 minute.

Destination: Send to CloudWatch Logs.

Destination log group: Enter ShareVPCFlowLogs to create a new log group with the same name.

IAM Role: Choose vpc-flow-logs-Role.

Choose Create flow log.

<img width="1490" height="820" alt="image" src="https://github.com/user-attachments/assets/caf8bbac-82cc-498e-bc84-6a0c1da2433e" />

<img width="1350" height="566" alt="image" src="https://github.com/user-attachments/assets/07702db7-0393-45a8-89a2-509acbb046b1" />

An alert is displayed at the top indicating the flow log was created for Shared VPC.

From the bottom pane, choose the Flow logs tab and notice that SharedVPCLogs was created.

Below Destination name, choose the hyperlink ShareVPCFlowLogs to display the CloudWatch log group that was created.

Note: Refresh after few minutes if you get a message that says Log group does not exist.

Keep this window open.

<img width="1677" height="819" alt="image" src="https://github.com/user-attachments/assets/5c5600b1-ead7-4355-b237-4bf3ff37bd6a" />

## Task 4: Testing the VPC peering connection
Now that you configured VPC peering, you will test the VPC peering connection. You will perform the test by configuring the inventory application to access the database across the peering connection.

From the top of this guide, choose AWS Details.

Copy the value for EC2PublicIP and paste it into a new web browser tab.

You should see the inventory application and the following message: Please configure Settings to connect to database. 

Choose  Settings, and configure the following settings:

Endpoint: Paste the database endpoint. To find this endpoint, choose AWS Details on the lab instructions page. Then, copy the Endpoint.

Database: inventory

Username: admin

Password: lab-password

Choose Save.

<img width="1432" height="468" alt="image" src="https://github.com/user-attachments/assets/7a6faa86-59b3-4f4f-b3de-9a4d01e4073e" />

The application should now show data from the database.

<img width="1259" height="477" alt="image" src="https://github.com/user-attachments/assets/cf85bba2-a92f-4886-8bc0-a74acc6cf9a9" />

This step confirms that the VPC peering connection was established because Shared VPC does not have an internet gateway. The only way to access the database is through the VPC peering connection.

## Task 5: Analyzing the VPC flow logs
In this task, you analyze the VPC flow logs to understand the traffic between application and database in peered VPCs.

Go to the browser tab or window that's displaying ShareVPCFlowLogs.

Select the Log stream eni-**.

After few minutes, the network traffic starts showing up.

Notice the traffic pattern from the logs, which looks similar to the following:

Traffic pattern

<img width="1679" height="596" alt="image" src="https://github.com/user-attachments/assets/5665a2e5-962e-4d17-bb71-bba01a1d33d9" />

You might notice lot of lines in the log, but you analyze few lines with port 3306; this represents the traffic between the application server and database.

Explanation: 
AWS Accountid	Shows the account in which the VPC is hosted (Hidden here)	 
Network interface	eni-* - The elastic network interface ID on which network traffic is recorded.	 
From IP	10.5.1.185 - The private IP of the database instance.	 
To IP	10.0.0.02 - The private IP of the destination, which is the EC2 instance running the application.	 
Port	3306 - RDS database port	 
action	Accept/Reject	 
status	OK	 
Note: There are many fields in the record, and the table is describing the important ones.

