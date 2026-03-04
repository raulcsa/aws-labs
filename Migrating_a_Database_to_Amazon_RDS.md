Scenario

The café currently uses a single Amazon Elastic Compute Cloud (Amazon EC2) instance to host its web server, database, and application code.

Meanwhile, the café business has grown. The order history that is stored in the database provides valuable business information that the café staff doesn't want to lose. Martha uses the data for accounting, and Frank looks at it occasionally to plan how many of each dessert type he should bake.

Sofía has additional concerns. The database must be consistently upgraded and patched, and she doesn’t always have time to do these tasks. Also, administering the database is a specialized skill. Training others to do database administration isn’t something that she wants to spend time on. Meanwhile, Sofía is also concerned that the café isn’t doing data backups as often as they should.

Finally, Martha also wants to reduce labor costs that are associated with the technical learning investment that's needed to manage the database.

Lab overview and objectives

In this lab, you migrate data from a database on an EC2 instance to Amazon Relational Database Service (Amazon RDS). Specifically, you migrate a MariaDB database that runs on an EC2 instance to a MariaDB database that runs on Amazon RDS. You also update the café web application to use the new database to store data for all future orders.

After completing this lab, you should be able to do the following:

Create an RDS database instance.

Export data from a MariaDB database by using mysqldump.

Connect a SQL client to an RDS database.

Migrate data from a MariaDB database that runs on an EC2 instance to an RDS database instance.

Configure a web application to use the new RDS database instance for data storage.



# A business request: Creating an RDS instance for the café application (challenge 1)

After a conversation with Olivia—the AWS solutions architect who often comes in for coffee—Sofía decided that the café needs a database solution that is easier to maintain. In addition, the database should provide essential features such as durability, scalability, and high performance.

In the first part of this lab, you take on the role of Sofía. You create an RDS instance that the café can use as the data storage layer for the café website. You also connect to the EC2 instance and analyze the details of the cafe web application.

## Task 1: Creating an RDS instance

Your first challenge in this lab is to create an RDS instance.

Go to the Amazon RDS console, and create an RDS instance with the following specifications.

When you submit your work at the end of this lab, it will check for many of these settings. To get full credit, follow these guidelines.

In the Engine options section, for Engine type, choose MariaDB.

Engine version : 10.6.25 (Do not use default or any other version)

<img width="1679" height="759" alt="image" src="https://github.com/user-attachments/assets/c5e5f672-2b15-4185-8142-78b3103a79af" />

For Templates, choose Dev/Test.

In the Settings section, configure the following options:

DB instance identifier: Enter CafeDatabase.

Master username: Enter admin.

Master password and Confirm master password: Enter Caf3DbPassw0rd!.

You must use this precise password.

Tip: Copy and paste the password to set it.

<img width="1348" height="711" alt="image" src="https://github.com/user-attachments/assets/3b04154a-df26-4964-84be-eece742beb50" />

In the Instance configuration section, for DB instance class, choose Burstable classes (includes t classes), and then choose db.t3.micro.

<img width="1486" height="391" alt="image" src="https://github.com/user-attachments/assets/a2a3d703-709e-4350-83ea-4cf7dd484b2f" />

In the Storage section, configure the following options:

Storage type: Choose General Purpose SSD(gp2).

Allocated storage: Enter 20 GiB.

<img width="1509" height="371" alt="image" src="https://github.com/user-attachments/assets/c875b1e4-f7cb-440e-84a7-9558cac2b7d3" />

For Availability & durability, choose Do not create a standby instance.

<img width="891" height="168" alt="image" src="https://github.com/user-attachments/assets/94ef7022-474e-4989-acf8-da2e83709f5d" />

In the Connectivity section, configure the following options:

Virtual private cloud (VPC): Choose Lab VPC.

DB subnet group: Choose lab-db-subnet-group.

Public access: Choose No.

<img width="1514" height="652" alt="image" src="https://github.com/user-attachments/assets/d1ef08cb-1950-47e9-a636-5571b3849f48" />

Existing VPC security group: Choose dbSG, and clear the default security group.


Availability Zone: Choose the first Availability Zone in the list, which ends in a. For example, if the Region is us-east-1, choose us-east-1a.

Database port: Keep the default TCP port of 3306.

<img width="1463" height="495" alt="image" src="https://github.com/user-attachments/assets/e6248dde-6c20-4050-a7e8-562a8d7bb3ec" />

<img width="1059" height="130" alt="image" src="https://github.com/user-attachments/assets/7d66d903-b6fb-43ab-9b17-15f1d28d9bb7" />

In the Monitoring section, clear Enable Enhanced Monitoring. Enhanced monitoring is not supported in the lab environment.

Choose Create database.

<img width="1435" height="266" alt="image" src="https://github.com/user-attachments/assets/80868987-774f-499d-bffb-4964392bacb8" />

Note: You don't have to wait for the database to finish the creation process. Instead, after you have successfully started the database creation process, continue to the next step.

## Task 2: Analyzing the existing café application deployment

In this task, you connect to the existing EC2 instance that runs the current café application.

Browse to the Amazon EC2 console, and choose Instances.

Notice the running instance named CafeServer. This EC2 instance was created when you started the lab.

To test the café application, in the following URL, replace with the actual IPv4 public IP address of the CafeServer instance:

http://<public-ip-address>/cafe

<img width="1677" height="966" alt="image" src="https://github.com/user-attachments/assets/4cb682b9-8e4d-4d5d-886e-4e3c61a6ca61" />

Open a browser tab and enter your revised URL.

To test placing an order, go to the Menu page.

Change the quantity for at least one menu item to at least 1, and choose Submit Order.

An Order Confirmation page should display, which indicates that the café website is working as intended.

Choose Order History.

The page shows that many orders were placed. The current database contains past customer orders that you will migrate to a database that is hosted on Amazon RDS.

<img width="1175" height="899" alt="image" src="https://github.com/user-attachments/assets/cfe807d8-25a4-4a12-b010-aa2f7ed048bf" />

Next, you connect to the EC2 instance by using AWS Systems Manager to access a terminal session in the browser.

In the Amazon EC2 console, in the left navigation pane, choose Instances, and then choose the CafeServer EC2 instance.

To open the Connect to instance menu, choose Connect.

Choose Session Manager.

Choose Connect.

<img width="1609" height="715" alt="image" src="https://github.com/user-attachments/assets/3412e90c-6871-4967-a508-1045d5f23beb" />

You should now have a new browser tab open with a terminal session that is connected to the EC2 instance.

At the prompt, enter the following commands:


```
bash

sudo su

su ec2-user

whoami
```
<img width="326" height="159" alt="image" src="https://github.com/user-attachments/assets/51123d4d-8851-4f03-a16b-9e09bcd2f223" />

```
cd /home/ec2-user/
```

Analysis: The first command gives you a bash shell. The second command switches your session to use the root user account on the EC2 instance. The third command switches you to use the ec2-user account. The fourth command should return output that confirms that you are connected as the ec2-user. The last command switches your terminal to the home directory of the ec2-user.

Note: The Systems Manager agent (ssm agent) is installed by default on all Amazon Linux 2 instances (and some other operating system types). When you started the lab and the EC2 instance was created, the user data specified that the ssm agent service should be started on the instance. Also, an AWS Identity and Access Management (IAM) role that includes an IAM policy named AmazonSSMManagedInstanceCore was attached to the EC2 instance. These two actions made the instance accessible through the Systems Manager Session Manager.

New business requirement: Exporting data from the old database and establishing a connection to the new database (challenge 2)

Now that you have created a new RDS instance, you can move on to the next step in the café's database migration plan. Next, you export the data from the database that the café application currently uses. You also establish a network connection from the EC2 instance (where the application runs) to the new RDS database instance.

In this challenge, you continue as Sofía to complete these tasks.


## Task 3: Working with the database on the EC2 instance

In this task, you observe details about the MariaDB database that runs on the EC2 instance. You then export the existing order history data from the database by using the mysqldump utility.

To observe details of the database that runs on the EC2 instance, in the terminal, run the following commands:
```
service mariadb status

mysql --version
```
<img width="1651" height="459" alt="image" src="https://github.com/user-attachments/assets/4fcf347f-7e00-41c7-82b1-0bf937133600" />

The output should show that the locally installed MariaDB database on this EC2 instance is running. It should also show the version number of the database.

Leave this browser tab open. You use it throughout this lab.

On the AWS Management Console, in the search box, enter and choose Secrets Manager to open the AWS Secrets Manager console.

In the left navigation pane, choose Secrets.

There are seven secrets stored here. The café application PHP code references these values (for example, to retrieve the connection information for the database).

Choose the /cafe/dbPassword secret.

<img width="1662" height="648" alt="image" src="https://github.com/user-attachments/assets/14eec02b-28dd-435f-8130-36727b271bfe" />

In the Secret value section, choose Retrieve secret value, and copy the Value to your clipboard.

You use this value in a moment.

To connect to the database that is running on the EC2 instance, in the browser tab with the bash terminal, run the following command:


```
mysql -u root -p
```
When prompted for the database password, paste the dbPassword parameter value that you copied a moment ago.

You should now see a MariaDB> prompt. This prompt indicates that you are now connected to the MariaDB database that runs on this EC2 instance.

<img width="716" height="235" alt="image" src="https://github.com/user-attachments/assets/039e2606-4aba-4dba-9478-3ce0d01e2839" />

To observe the data in the existing database, run the following commands:
```
show databases;

use cafe_db;

show tables;

select * from `order`;
```
In particular, you should review the tables that support the café web application.

These commands show all the orders that were placed, including the order that you placed a moment ago.

<img width="657" height="793" alt="image" src="https://github.com/user-attachments/assets/8f017b61-4376-4df5-a534-274d6cdb38bf" />

To show the order line items, run the following command:


```
select * from `order_item`;
```
Each order number has a row for each type of item that was ordered, with details about the quantity of each item and the price.

All this data must be migrated to the new database.

<img width="642" height="801" alt="image" src="https://github.com/user-attachments/assets/da98b0a6-2594-493a-9fb3-4b9b579a7c53" />

To exit the SQL client, enter the following command:


```
exit;
```
To capture existing data in a file by using the mysqldump utility, run the following command:


```
mysqldump --databases cafe_db -u root -p > CafeDbDump.sql
```
<img width="711" height="54" alt="image" src="https://github.com/user-attachments/assets/93fb2d3e-b456-498a-a099-0b953c4bccd1" />

Este comando se utiliza para realizar un respaldo (backup) de mi base de datos.

mysqldump: Es la herramienta de utilidad de MySQL/MariaDB diseñada para "volcar" (exportar) bases de datos.

--databases cafe_db: Le indica al programa específicamente qué base de datos quieres exportar. En este caso, la que se llama cafe_db.

-u root: Especifica el usuario con el que te vas a conectar a la base de datos (en este caso, el superusuario root).

-p: Le indica al sistema que vas a introducir una contraseña. Al ejecutar el comando, la terminal se quedará pausada esperando a que la escribas.

>: Este es un operador de redirección. En lugar de mostrar toda la información de la base de datos en la pantalla de la terminal, "empuja" esa información hacia un archivo.

CafeDbDump.sql: Es el nombre del archivo que se creará. Contendrá todas las instrucciones SQL necesarias (como CREATE TABLE e INSERT INTO) para recrear tu base de datos en otro lugar.

When prompted for the database password, paste the dbPassword value from the Secrets Manager secrets.

To confirm that mysqldump succeeded, run the ls command in the terminal.

The output should show that the CafeDbDump.sql file was created.

<img width="257" height="36" alt="image" src="https://github.com/user-attachments/assets/6a6d2981-f595-46be-906a-12f940dcc1ac" />

To see the contents of the file, run the cat CafeDbDump.sql command.

In the next section of the lab, you import this data into the new RDS database.

## Task 4: Working with the RDS database

In this task, you first answer a few questions about the RDS instance that you created. Then, you confirm that you can connect to the RDS instance.

In the AWS Management Console, return to the Amazon RDS console and confirm that the cafedatabase RDS instance that you created is now available.

Answering questions about the RDS instance

Your answers are recorded when you choose Submit at the end of the lab.

To access the questions in this lab, at the top of these instructions, choose AWS Details.

Choose the Access the multiple choice questions link.

In the page you loaded, answer the four questions:

Question 1: Where is the RDS instance running?

Question 2: Does the RDS instance have an IPv4 public IP address assigned to it?

Question 3: What is the Name tag applied to the subnet in which the RDS instance is running?

Question 4: How many security group rules are defined for the RDS instance?

Establish a network connection from the terminal running on the EC2 instance to the new RDS instance.

Here are some tips to help you get started. Expand each tip for help solving the challenge.

Tip 1

Here is the syntax that you can use to connect: mysql -u admin -p --host <rds-endpoint>

Replace <rds-endpoint> with the actual RDS endpoint for your RDS instance.

After you run the command, it prompts you to enter the password for the RDS instance. You defined this password when you created the RDS instance.

Tip 2

Even if you enter the RDS endpoint and database password correctly, you still won't be able to connect. You must update the inbound rules of the security group that the RDS instance runs in. The MySQL client software tries to connect to the database on TCP port 3306.

Tip 3

Avoid opening port 3306 to all source IP addresses. That would not be secure. Instead, open it to only servers in the security group that is used by the EC2 instance that you're connecting from. Try entering sg- into the source field to see options.

Tip 4

You can confirm that the security group settings allow traffic on TCP port 3306 from the EC2 instance to the database. Try running these commands in the Systems Manager Session Manager terminal. In the following command, replace <rds-endpoint> with the actual Amazon RDS endpoint:

nmap -Pn <rds-endpoint>

If the output of the command shows that port 3306 is open for the MySQL service, then it confirms that the security group settings allow the traffic.

If nmap shows that the port is open, then the mysql -u admin -p --host <rds-endpoint> command should also work. However, you must enter the database password correctly. This password is the one that you set when you created the instance.

<img width="1611" height="351" alt="image" src="https://github.com/user-attachments/assets/c7b3cef1-28ec-4eca-bb50-fcc23d309059" />


Note: If you still can't solve the issue, you might find it helpful to submit your work, as documented in the Submitting your work section at the end of these lab instructions. The Submission Report can provide additional tips for parts of the lab that you didn't complete successfully. You can submit your work as many times as you like. Only the score you achieve on the last submission is retained.

It's important to confirm that you can connect to the RDS MariaDB instance before you go to the next step. If you already managed to connect, congratulations!
<img width="910" height="180" alt="image" src="https://github.com/user-attachments/assets/6595a692-1f00-4f1b-bae3-e009db7493b5" />

Run the show databases; command.

It should show the following output:

<img width="752" height="259" alt="image" src="https://github.com/user-attachments/assets/7038fc14-7a8f-4d94-8707-4c3b9958887f" />
​
Notice that cafe_db database is not in the list yet. This situation is expected because you haven't imported any data.

To disconnect, run the exit; command.

New business requirement: Importing data and connecting the application to the new database (challenge 3)

In the previous challenge, you exported the data from the database that the café application currently uses. You also established a network connection from the EC2 instance to the RDS instance. You can now work on the next business requirement.

In this challenge, you continue to take on the role of Sofía to import the cafe data into the RDS database instance. After you complete the import, you configure the application to use the new database.

## Task 5: Importing the data into the RDS database instance

To import the data that you exported in task 3 to the RDS database instance, in the following command, replace with the actual endpoint, and run your adjusted command.

mysql -u admin -p --host <rds-endpoint> < CafeDbDump.sql

<img width="1018" height="55" alt="image" src="https://github.com/user-attachments/assets/c3a3555a-2358-4719-8c63-c58ffc4de3ce" />

At the password prompt, enter the password for the RDS instance.

If you don't see any errors, the command likely succeeded.

To confirm that the data was imported and connect to the RDS database, run the following command:

mysql -u admin -p --host <rds-endpoint>

At the password prompt, enter the password for the RDS instance.

To confirm that the data was imported, run the following commands:

show databases;

use cafe_db;

show tables;

select * from `order`;

The output of the select statement should show at least 24 orders in the database.

To exit the SQL client, run the following command:
<img width="656" height="845" alt="image" src="https://github.com/user-attachments/assets/8afec1f3-a640-484c-8f02-e6fc6df8df22" />

exit;

## Task 6: Connecting the café application to the new database

In this last task in the lab, you connect the café application to the new database. You also stop the database that runs locally on the EC2 instance.

Return to the Secrets Manager console browser tab.

In the left navigation pane, choose Secrets.

Recall from an earlier challenge lab that the café application's PHP code references these values. For example, it uses the values to retrieve the connection information for the database.

Connect the café application to the RDS instance.

Because the database connection information has changed, you must update these values to connect the application to the new RDS database instance instead of to the database running on the EC2 instance.

Tip 5

After you update which database the application is connected to, use the http://<public-ip>/cafe/menu.php page to test whether you have successfully updated the connection.

Tip 6

The PHP code doesn't need any updates. Also, if you successfully completed the previous challenge in this lab, the network configuration doesn't need additional changes. The only updates that you must make will be to some values in the Secrets Manager secrets.

Tip 7

The currency, dbName, timeZone, and showServerInfo values don't need to be updated.

Tip 8

The dbUrl should be the RDS endpoint value.
<img width="1679" height="695" alt="image" src="https://github.com/user-attachments/assets/f423ac33-63b5-459b-b3fd-3d22b1235ad4" />

<img width="1679" height="702" alt="image" src="https://github.com/user-attachments/assets/2250c074-acea-4fe2-b3aa-882e08ca80d6" />

<img width="1629" height="715" alt="image" src="https://github.com/user-attachments/assets/c5547f1b-9006-4100-8b72-fab98cedb81f" />

To confirm that your web application now uses the new database and to stop the database that's still running on the EC2 instance, in the terminal, run the following command:

sudo service mariadb stop

Load the http://<public-ip>/cafe/menu.php page, and confirm that the application still works by placing an order.

Choose Order History.

Your latest order and all the other previous orders should be there. These orders are the data that you migrated to the new database.

Update from the café
<img width="1671" height="976" alt="image" src="https://github.com/user-attachments/assets/32492200-ee3c-4b8f-80da-359ed5fed7bd" />

Everyone at the café is happy with the results of the database migration. Sofía and Nikhil now have more free time on weekends, which means that Frank and Martha are saving money on labor costs.

Sofía takes a minute to relax with her friends. However, she's already thinking about improvements. A good next step would be to reduce the size of the Amazon Elastic Block Store (Amazon EBS) volume that the EC2 instance uses. They could also save on expenses by changing the EC2 instance type to a smaller size. Because the database no longer runs on the EC2 instance, the instance now has extra hard drive space, and it might also not need as many CPU and memory resources.

Conclusion

Congratulations! you have successfully completed the following:

Created an RDS database instance

Exported data from a MariaDB database by using mysqldump

Connected a SQL client to an RDS database.

Migrated data from a MariaDB database that runs on an EC2 instance to an RDS database instance

Configured a web application to use the new RDS database instance for data storage
