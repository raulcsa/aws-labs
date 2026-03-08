Challenge Lab: Creating a VPC Networking Environment for the Café
Scenario
Sofía and Nikhil are now confident in their ability to create a two-tier architecture because of their experience migrating the café's data. They successfully moved from a MariaDB database on an Amazon Elastic Compute Cloud (Amazon EC2) instance to an Amazon Relational Database Service (Amazon RDS) database instance. In addition, they also moved their database resources from a public subnet to a private subnet.

When Mateo—a café regular and an AWS systems administrator and engineer—visits the café, Sofía and Nikhil tell him about the database migration. Mateo tells them that they can enhance security by running the café's application server in another private subnet that's separate from the database instance. They could then go through a bastion host (or jump box) to gain administrative access to the application server. The application server must also be able to download needed patches.

Knowing that the cloud makes experimentation easier, Sofía and Nikhil are eager to set up a nonproduction VPC environment. They can use it to implement the new architecture and test different security layers without accidentally disrupting the café's production environment.

Lab overview and objectives
In this lab, you use Amazon Virtual Private Cloud (Amazon VPC) to create a networking environment on AWS and implement security layers to protect your resources.

After completing this lab, you should be able to do the following:

Create a virtual private cloud (VPC) environment that you can use to securely connect to private resources.

Enable your private resources to connect to the internet.

Create an additional layer of security by using a NAT gateway to control access to and from private resources.

When you start the lab, you will have only a VPC created for you in the AWS account.

At the end of this lab, your architecture should look like the following example:

<img width="1072" height="790" alt="image" src="https://github.com/user-attachments/assets/511e7fa9-4bd4-4c88-89b5-01df477d04eb" />

Image shows the final architecture with all components placed in respective subnets and security groups.

(In the diagram, the communication arrows were omitted for simplicity.)

Note: In this challenge lab, step-by-step instructions are not provided for most of the tasks. You must figure out how to complete the tasks on your own.

Duration
This lab requires approximately 90 minutes to complete.

AWS service restrictions
In this lab environment, access to AWS services and service actions might be restricted to the ones that are needed to complete the lab instructions. You might encounter errors if you attempt to access other services or perform actions beyond the ones that are described in this lab.

Accessing the AWS Management Console
At the top of these instructions, choose  Start Lab.

The lab session starts.

A timer displays at the top of the page and shows the time remaining in the session.

 Tip: To refresh the session length at any time, choose  Start Lab again before the timer reaches 0:00.

Before you continue, wait until the circle icon to the right of the AWS  link in the upper-left corner turns green. When the lab environment is ready, the AWS Details panel displays.

To connect to the AWS Management Console, choose the AWS link in the upper-left corner above the terminal window.

A new browser tab opens and connects you to the console.

 Tip: If a new browser tab does not open, a banner or icon is usually at the top of your browser with a message that says your browser is preventing the site from opening pop-up windows. Choose the banner or icon, and then choose Allow pop-ups.

Arrange the AWS Management Console tab so that it displays alongside these instructions. Ideally, you have both browser tabs open at the same time so that you can follow the lab steps.

A business request for the café: Creating a VPC network that the café staff can use to remotely and securely administer the web application server (challenge 1)
In this challenge, you take on the role of one of the café's system administrators. You create and configure a VPC network so that you can securely connect from a bastion host in a public subnet to an EC2 instance in a private subnet. You also create a NAT gateway to enable the EC2 instance in your private subnet to access the internet.  

## Task 1: Creating a public subnet
Your first task in this lab is to create a public subnet in the Lab VPC. After you create a public subnet, you create an internet gateway to allow communication from the subnet to the internet. You update the routing table that's attached to the subnet to route internet-bound network traffic through the internet gateway.  

Open the Amazon VPC console.

Note that a VPC called Lab VPC has been created for you.

Create a public subnet with the following options:

VPC ID: Choose Lab VPC.

Subnet name: Enter Public Subnet.

Availability Zone: Choose Availability Zone a in your Region. For example, if your Region is us-east-1, choose us-east-1a.

IPv4 subnet CIDR block: Enter 10.0.0.0/24.

<img width="1425" height="792" alt="image" src="https://github.com/user-attachments/assets/8563c41e-cf50-4dd0-8288-d3293a2adc59" />

Create a new internet gateway and attach it to the Lab VPC.

<img width="1501" height="481" alt="image" src="https://github.com/user-attachments/assets/cc6ac5a0-d57b-4f1f-a820-b0f3b1c964fc" />

<img width="1581" height="355" alt="image" src="https://github.com/user-attachments/assets/d5dd78e4-7909-477d-bcff-cfc8cd91814e" />

Edit the route table that was created in your VPC by adding the route 0.0.0.0/0. For the target, choose the internet gateway that you created in the previous step.

<img width="1532" height="327" alt="image" src="https://github.com/user-attachments/assets/256d9a2f-2bb0-44e9-9141-a0ccd31938c5" />

Hint: To successfully complete this task, you must create a few resources. If you get stuck, see the AWS Documentation.

## Task 2: Creating a bastion host
In this task, you create a bastion host in the public subnet. In later tasks, you create an EC2 instance in a private subnet and connect to it from this bastion host.

From the Amazon EC2 console, create an EC2 instance in the public subnet of the Lab VPC. Configure the following options:

For Name, enter Bastion Host.

For Amazon Machine Image (AMI), choose Amazon Linux 2023 AMI.

<img width="994" height="381" alt="image" src="https://github.com/user-attachments/assets/453591ae-47b3-4377-8cfc-1086f99deae0" />

For Instance type, choose t2.micro.

For Key pair name - required, choose vockey.

<img width="1172" height="835" alt="image" src="https://github.com/user-attachments/assets/026159aa-18d4-4701-ae3f-34f0a6176c2c" />

In the Network settings section, choose Edit, and configure the following options:

For VPC - required, choose Lab VPC.

For Subnet, choose Public Subnet.

For Auto-assign public IP, choose Enable.

For Security group, create a security group called Bastion Host SG that allows only the following traffic:

For Type, choose ssh.

For Port, enter 22.

For Source type, choose My IP.

<img width="889" height="790" alt="image" src="https://github.com/user-attachments/assets/8a116835-560c-4ee5-bc79-6b0dcb27392a" />

Note: In practice, hardening a bastion host involves more work than only restricting Secure Shell (SSH) traffic from your IP address. A bastion host is typically placed in a network that's closed off from other networks. It's often protected with multi-factor authentication (MFA) and monitored with auditing tools. Most enterprises require an auditable access trail to the bastion host.

## Task 3: Testing the connection to the bastion host
In this task, you use the SSH key (.pem file or .ppk file) to test the SSH connection to your bastion host. This key was created for you.

In the upper-right area of these instructions, choose AWS Details.

Download the SSH key.

Microsoft Windows PuTTY users: Choose Download PPK.

macOS or Linux users: Choose Download PEM.

Note that the file is named labuser.*.

To close the window, choose Close.

Connect to your bastion host by using SSH.

After you have tested your connection to the bastion host, you can close the terminal or PuTTY.

<img width="928" height="282" alt="image" src="https://github.com/user-attachments/assets/1b69d0c0-0739-4d23-90ca-8796097cf0f6" />

Hint: If you get stuck, see the AWS Documentation. This page provides detailed instructions about how to use SSH to connect to an EC2 instance.

Note for Microsoft Windows users: If you don't have PuTTY installed, you must download and install PuTTY. It is recommend that you configure PuTTY so that your connection doesn't expire. To keep the PuTTY session open longer, for Seconds between keepalives, enter 30.

## Task 4: Creating a private subnet
In this task, you create a private subnet in the Lab VPC.

In the Amazon VPC console, create a private subnet with the following options:

VPC ID: Choose Lab VPC.

Subnet name: Enter Private Subnet.

Availability Zone: Choose the same Availability Zone that you choose for the Public Subnet in one of the previous tasks.

IPv4 subnet CIDR block: Enter 10.0.1.0/24.

<img width="1564" height="835" alt="image" src="https://github.com/user-attachments/assets/981557c2-655b-464a-a20d-9a60670cf830" />

## Task 5: Creating a NAT gateway
In this task, you create a NAT gateway, which enables resources in the Private Subnet to connect to the internet.

Create a NAT gateway that has the following options:

Name - optional: Enter Lab NAT Gateway.

Subnet: Choose Public Subnet.

Elastic IP allocation ID: Choose Allocate Elastic IP.

Tip: Your NAT gateway needs an Elastic IP address.

<img width="1642" height="818" alt="image" src="https://github.com/user-attachments/assets/326b398f-4608-4b06-9a48-7bf604881074" />

Create a new route table that has the following options:

Name - optional: Enter Private Route Table.

VPC: Chose Lab VPC.

<img width="1593" height="469" alt="image" src="https://github.com/user-attachments/assets/257c0bff-674c-4e97-bd99-fb474d27ba58" />

Attach this route table to the Private Subnet that you created earlier using the following options: 

Destination: Enter 0.0.0.0/0.

Target: Choose NAT Gateway.

<img width="1704" height="335" alt="image" src="https://github.com/user-attachments/assets/958ee7c1-7566-4988-ad86-c23e9526d9b9" />

<img width="1621" height="415" alt="image" src="https://github.com/user-attachments/assets/304a5f27-91bd-460a-bd06-5f53fbaa7404" />

Hint: If you get stuck, see the AWS Documentation.

## Task 6: Creating an EC2 instance in the private subnet
In this task, you create an EC2 instance in the Private Subnet, and you configure it to allow SSH traffic from the bastion host. You also create a new key pair to access this instance.

Create a new key pair named vockey2, and download the appropriate .ppk (Microsoft Windows) or .pem (macOS or Linux) file.

Create an EC2 instance in the Private Subnet of the Lab VPC that has the following options:

For Name, enter Private Instance.

For Amazon Machine Image (AMI), choose Amazon Linux 2023 AMI.

<img width="1132" height="790" alt="image" src="https://github.com/user-attachments/assets/70de0220-7398-429d-8029-5687fa170530" />

For Instance type, choose t2.micro.

For Key pair name - required, choose the vockey2 key pair that you created earlier.

<img width="894" height="346" alt="image" src="https://github.com/user-attachments/assets/d11e6c9f-e09f-4671-a42d-afaaf1e2dce7" />

In the Network settings section, choose Edit, and configure the following options:

For VPC - required, choose Lab VPC.

For Subnet, choose Private Subnet.

For Security group, create a security group called Private Instance SG that allows only the following traffic:

For Type, choose ssh.

For Port, enter 22.

For Source type, choose Custom.

For Source, choose the bastion host security group (Hint: for more information, see the AWS Documentation).

<img width="881" height="757" alt="image" src="https://github.com/user-attachments/assets/7a4b6f03-50c5-4297-94d8-2b918d4ec5e3" />

##Task 7: Configuring your SSH client for SSH passthrough

Habilitar el servicio ssh-agent en Windows

<img width="477" height="119" alt="image" src="https://github.com/user-attachments/assets/4e80e1d2-2303-41b7-b3de-31004cd6d2b4" />

Enciendo el servicio:

<img width="648" height="78" alt="image" src="https://github.com/user-attachments/assets/a6742263-b493-4b16-a314-243aed21f469" />

Añado mis claves al agente

<img width="769" height="107" alt="image" src="https://github.com/user-attachments/assets/c64e887d-1570-4d16-9ed5-0d4cccd194c1" />



## Task 8: Testing the SSH connection from the bastion host
In this task, you test the SSH connection from your bastion host to the EC2 instance that is running in the Private Subnet.

Connect to the bastion host instance by using SSH.
Utilizando la bandera -A. Esta letra mayúscula es la que habilita el "SSH passthrough" (Agent Forwarding):
<img width="785" height="387" alt="image" src="https://github.com/user-attachments/assets/02a2d706-465f-46c8-acf2-c7e7a0433416" />

Tip: Use the connection method that was described in the SSH passthrough section.

Connect to the private instance by using SSH and the IP address for the private instance. Your command should look similar to the following. In this command, replace <private-ip-address-of-instance-in-private-subnet> with the private IP address of the instance in the private subnet:

ssh ec2-user@<private-ip-address-of-instance-in-private-subnet>
Now that you are connected to the EC2 instance in the Private Subnet, use the following command to test its connection to the internet:

ping 8.8.8.8
Tip: Press Ctrl+C to exit the command.

<img width="702" height="406" alt="image" src="https://github.com/user-attachments/assets/196bc43f-1a8e-43ae-a0e3-28936d4ee0af" />

You have now established a communication between the Bastion Host in the Public Subnet and the EC2 instance in the Private Subnet as in the following diagram:

Bastion host to private EC2 instance.

Architecture best practice

In this first challenge, you implemented the architectural best practice of giving people the ability to perform actions at a distance. 

Expand the following tip to learn more.

Tip 1
According to the AWS Well-Architected Framework, compute resources require multiple layers of defense to help protect them from external and internal threats. In practice, you should remove the ability for interactive access to reduce the risk of human error and the potential for manual configuration or management. The Well-Architected Framework recommends that you use a change management workflow to deploy EC2 instances by using infrastructure as code. Then, you should use tools, such as AWS Systems Manager, to manage EC2 instances instead of allowing direct access or using a bastion host. For more information, see "Replacing a Bastion Host with Amazon EC2 Systems Manager" in the AWS Cloud Operations & Migrations Blog.
New business requirement: Enhancing the security layer for private resources (challenge 2)
Sofía and Nikhil are proud of the changes they made to the cafe's application architecture. They are pleased by the additional security they built, and they are also glad to have a test environment that they can use before they deploy updates to the production instance. They tell Mateo about their new application architecture, and he's impressed. To further improve their application security, Mateo advises them to build an additional layer of security by using custom network access control lists (network ACLs).

In this challenge, you continue to take on the role of one of the café's system administrators. Now that you established secure access from the bastion host to the EC2 instance in the private subnet, you must enhance the security layer of the private subnet. To accomplish this task, you create and configure a custom network ACL.

## Task 9: Creating a network ACL
In this task, you create a custom network ACL to control traffic to and from the Private Subnet.

You can use network ACLs to control traffic between subnets. It's a good practice to use network ACLs to implement rules that are similar to your security group rules. The network ACLs provide an additional layer of protection.

For this challenge, you create an EC2 instance in the Public Subnet. You create a security group that allows Internet Control Message Protocol (ICMP) traffic from the local network. Next, you create and configure your custom network ACL to deny ICMP traffic between the Private Subnet and this test instance. ICMP is used by the ping utility.

Go to the Amazon VPC console, and inspect the default network ACL of the Lab VPC.  

Note 1: The subnets that you created are automatically associated with the default network ACL.
Note 2: The inbound and outbound rules of the default network ACL allow all traffic.

Create a custom network ACL called Lab Network ACL for the Lab VPC.  

<img width="1598" height="482" alt="image" src="https://github.com/user-attachments/assets/fb2bb111-5628-4d83-b75b-241525ac4911" />

Note: The default inbound and outbound rules of the custom network ACL deny all traffic.

Configure your custom network ACL to allow all traffic that goes into and out of the Private Subnet.

<img width="1633" height="277" alt="image" src="https://github.com/user-attachments/assets/ddc67055-c9ae-4e9e-a184-fd0615cc8361" />

<img width="1688" height="293" alt="image" src="https://github.com/user-attachments/assets/752e53b9-6bc7-4b13-a423-71446eee1c43" />

<img width="1655" height="387" alt="image" src="https://github.com/user-attachments/assets/9b6b24b7-2dde-4383-b9b7-6ef506dd3550" />

Hint: If you get stuck, see the AWS Documentation.

Task 10: Testing your custom network ACL
Create an EC2 instance in the Public Subnet of the Lab VPC with the following options.

For Name, enter Test Instance

For Amazon Machine Image (AMI), choose Amazon Linux 2023 AMI.

<img width="888" height="770" alt="image" src="https://github.com/user-attachments/assets/b9fe7a6a-5f97-4f09-b635-3277c40ee082" />

For Instance type, choose t2.micro.

For Key pair name - required, choose vockey.

<img width="890" height="339" alt="image" src="https://github.com/user-attachments/assets/6ac332ca-25a8-49f6-b5f0-16a41aab2515" />

In the Network settings section, choose Edit, and configure the following options:

For VPC - required, choose Lab VPC.

For Subnet, choose Public Subnet.

For Auto-assign public IP, choose Enable.

For Security group, create a security group called Test SG, and configure the following options:

In the the Inbound Security Group Rules section, for Type, choose All ICMP – IPv4.

Leave all other values as default.

<img width="860" height="810" alt="image" src="https://github.com/user-attachments/assets/43a06f4c-5ad3-4820-a362-f0e4beffd313" />

Note the private IP address of the Test Instance.

To test that you can reach the private IP address of the Test Instance from the Private Instance, from the Private Instance terminal window, run the following ping command. In the command, replace <private-ip-address-of-test-instance> with the private IP address of the Test Instance:

ping <private-ip-address-of-test-instance>
Leave the ping utility running.

<img width="581" height="152" alt="image" src="https://github.com/user-attachments/assets/84aa0733-99ce-481c-a575-2cdf10bf8c75" />

Modify your custom network ACL to deny all ICMP IPv4 traffic to the <private-ip-address-of-test-instance>/32.

Make sure to add /32 to the end of the private IP address.

<img width="1681" height="346" alt="image" src="https://github.com/user-attachments/assets/810589b7-8f3e-4e41-bdca-f045d8e80029" />

Make sure that this rule is evaluated first.

In the Private Instance terminal window, the ping command should stop responding. The traffic to the Test Instance has been blocked.

You have now denied traffic from the Private Subnet to the Test Instance, as shown in the following diagram:

Inage shows ability the comminucation between Private EC2 instance and the Test EC2 instance in public subnet.

Architecture best practice

In this second challenge, you protected your network resources by implementing the architectural best practice of controlling traffic at all layers.



 

