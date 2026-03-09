# AWS Labs

A collection of hands-on AWS lab guides covering core services including networking, databases, storage, security, and web application deployment.

## 📚 Labs

### 🌐 Networking

| Lab | Description |
|-----|-------------|
| [Creating a VPC](./Creating_a_VPC.md) | Build a Virtual Private Cloud from scratch with public and private subnets, an internet gateway, route tables, and security groups. Deploy an EC2 instance and verify connectivity. |
| [Creating a VPC Networking Environment for the Café](./Creating_a_VPC_Networking_Environment_for_the_Café.md) | Challenge lab — design a multi-tier VPC with a bastion host, NAT gateway, and layered security for a café application. |
| [Creating a VPC Peering Connection](./Creating_a_VPC_Peering_Connection.md) | Connect two VPCs using a peering connection, configure route tables, enable VPC Flow Logs, and test cross-VPC communication. |

### 🗄️ Databases

| Lab | Description |
|-----|-------------|
| [Creating an Amazon RDS Database](./Creating_an_Amazon_RDS_Database.md) | Create a MySQL RDS instance inside a VPC, connect a web application to it, and explore database configuration options. |
| [Migrating a Database to Amazon RDS](./Migrating_a_Database_to_Amazon_RDS.md) | Challenge lab — migrate a MariaDB database from an EC2 instance to Amazon RDS using `mysqldump`, and reconfigure the café web application to use the new managed database. |

### 💾 Storage

| Lab | Description |
|-----|-------------|
| [Introducing Amazon Elastic File System (Amazon EFS)](<./Introducing_Amazon_Elastic_File_System_(Amazon EFS).md>) | Create an EFS file system, configure mount targets and security groups (NFS port 2049), and mount the shared file system across EC2 instances. |

### 🔐 Security & Identity

| Lab | Description |
|-----|-------------|
| [Exploring AWS Identity and Access Management](./Exploring_AWS_Identity_and_Access_Management.md) | Explore IAM users, groups, and managed policies. Assign permissions through groups and test access controls for EC2 and S3 services. |

### 🖥️ Web Application Deployment

| Lab | Description |
|-----|-------------|
| [Creating a Dynamic Website for the Café](./Creating_a_Dynamic_Website_for_the_Café.md) | Challenge lab — deploy a PHP/MariaDB web application on EC2, integrate AWS Secrets Manager, create an AMI, and replicate the deployment to a second AWS Region. |

## 🛠️ AWS Services Covered

- **Compute:** Amazon EC2
- **Networking:** Amazon VPC, Subnets, Internet Gateways, NAT Gateways, Route Tables, Security Groups, VPC Peering, VPC Flow Logs
- **Databases:** Amazon RDS (MySQL, MariaDB)
- **Storage:** Amazon EFS
- **Security:** AWS IAM, AWS Secrets Manager
- **Management:** AWS Management Console

## 📋 Prerequisites

- Access to an AWS account (AWS Academy / Learner Lab or equivalent)
- Basic familiarity with the AWS Management Console
- Each lab is self-contained and includes step-by-step instructions with screenshots

## 📝 Notes

- Labs marked as **Challenge Lab** provide high-level objectives without detailed step-by-step guidance — you are expected to figure out the implementation on your own.
- Follow each lab in order when they are part of a series (e.g., creating a VPC before migrating a database).
- Resource names used in the labs (e.g., `Lab VPC`, `inventory-db`) are specific to the lab environment and should be used as-is to ensure grading scripts work correctly.