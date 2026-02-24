Challenge Lab: Creating a Dynamic Website for the Café
 

Scenario
After the café launched the first version of its website, customers told the café staff how nice the website looks. However, in addition to the providing praise, customers often asked whether they could place online orders.

Sofía, Nikhil, Frank, and Martha discussed the situation. They agreed that their business strategy and decisions should focus on delighting their customers and providing them with the best possible café experience.

Lab overview and objectives
In this lab, you deploy an application on an Amazon Elastic Compute Cloud (Amazon EC2) instance. The application gives the café the ability to accept online orders. After testing that the application works as intended in the first AWS Region (the development environment), you then create an Amazon Machine Image (AMI) from the EC2 instance. You also deploy a second instance of the same application as the production environment in another AWS Region.

After completing this lab, you should be able to do the following:

Connect to the Visual Studio Code Integrated Development Environment (VS Code IDE) on an existing EC2 instance.

Configure the EC2 instance environment and confirm web server accessibility.

Install a web application on an EC2 instance that also uses AWS Secrets Manager.

Test the web application.

Create an AMI.

Deploy a second copy of the web application to another AWS Region.

When you start the lab, some resources are already created for you in the AWS account:

Starting architecture showing the AWS resources available to learners when the lab starts.

At the end of this lab, your architecture should look like the following example:

<img width="972" height="366" alt="image" src="https://github.com/user-attachments/assets/2b42a13e-9ec9-40a1-bda2-86ac9b584e83" />

Final architecture showing AWS resources built after the lab challenge is complete.

Duration
This lab requires approximately 60 minutes to complete.

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

 Tip: If a new browser tab does not open, a banner or icon is usually at the top of your browser with a message that your browser is preventing the site from opening pop-up windows. Choose the banner or icon, and then choose Allow pop-ups.

Arrange the AWS Management Console tab so that it displays alongside these instructions. Ideally, you have both browser tabs open at the same time so that you can follow the lab steps.

 

A business request for the café: Preparing an EC2 instance to host a website (challenge #1)
The café wants to introduce online ordering for customers and give café staff the ability to view submitted orders. Their current website architecture, where the website is hosted on Amazon S3, does not support the new business requirements.

In the first part of this lab, you take on the role of Sofía. You configure an EC2 instance so that it is ready to host a website for the café.

Task 1: Analyzing the existing EC2 instance
In this task, you note details about an existing EC2 instance that has been created for you in the AWS account.

On the AWS Management Console, in the search box, enter and choose EC2 to open the Amazon EC2 console.

Choose Instances.

Notice the running instance named Lab IDE. This EC2 instance was created when you started the lab.  

Amazon EC2 console.

Task 1.1: Answering questions about the instance
Your answers are evaluated when you choose the blue Submit button at the end of the lab.

To access the questions in this lab, at the top of these instructions, choose i AWS Details.

Choose the Access the multiple choice questions link.

On the page that you loaded, answer the first four questions about the Lab IDE EC2 instance:

Question 1: Is the instance in a public subnet?

Question 2: Does the EC2 instance have an IPv4 Public IP address assigned to it?

Question 3: What inbound TCP port numbers are open for this instance?

Question 4: Does the EC2 instance have an AWS Identity and Access Management (IAM) role associated with it?

Note: Leave the questions webpage open in your browser tab. You return to it later in this lab.

<img width="1916" height="655" alt="image" src="https://github.com/user-attachments/assets/9d4ad3aa-3c13-4305-a0d0-e8591e12fa45" />


Task 2: Connecting to the IDE on the EC2 instance
In this first task, you will connect to VS Code IDE and configure the environment to support the development that you will work on during the rest of the lab.

At the top of these instructions, choose i AWS Details   

Copy values from the table for the following and paste it into an editor of your choice for use later.

LabIDEURL

LabIDEPassword

In a new browser tab, paste the value for LabIDEURL to open the VS Code IDE.

On the prompt window Welcome to code-server: 

Enter the value for LabIDEPassword you copied to the editor earlier

Choose Submit to open the VS Code IDE similar to below.

Note: User Interface is displayed as shown below.

The IDE includes the following:

A bash terminal in the bottom-right panel.

A file browser in the left panel that shows files in the /home/ec2-user/environment directory on the instance.

A file editor in the upper-right panel. If you select a file in the file browser, such as the README.md file, it displays in the editor.

<img width="1919" height="994" alt="image" src="https://github.com/user-attachments/assets/6ed04ffd-90a8-4fb0-b28b-5e7add1d2986" />


VS Code IDE.

Task 3: Configuring the LAMP stack environment and confirming that the web server is accessible
Recall that the objective of this challenge lab is to configure an EC2 instance to host the new dynamic website for the café. In this task, you analyze what is already installed and install the database.

To observe the operating system version, in the VS Code IDE bash terminal, run the following command:

cat /proc/version
Notice how the output indicates that it is an Amazon Linux instance roughly analogous to Red Hat 7.
<img width="1493" height="81" alt="image" src="https://github.com/user-attachments/assets/1b468588-5d61-48f3-8c82-de0b815671c8" />


To observe the web server, PHP details, and server state, run the following commands:

sudo sed -i 's/Listen 80/Listen 8000/g' /etc/httpd/conf/httpd.conf
sudo systemctl start httpd
sudo systemctl enable httpd
sudo service httpd status
php --version
The output should show the versions of the web server and that they are not currently running.
<img width="942" height="566" alt="image" src="https://github.com/user-attachments/assets/4709c0e3-3d68-4076-b244-adf8c02e2257" />


Note: For this lab, you run the web server on port 8000 since the IDE is running on port 80.

To install the database, and set them to start automatically after any future EC2 instance restarts, run the following commands:

#Install database
sudo dnf install -y mariadb105-server
sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo mariadb --version
sudo service mariadb status
	Note: After running these commands, if the terminal doesn't show a prompt, press Q. 

<img width="1120" height="573" alt="image" src="https://github.com/user-attachments/assets/c73b8614-57eb-4e03-bdc4-8dd48339e768" />


To configure the EC2 instance so that you can use the VS Code IDE to edit web server files, run the following commands:

ln -s /var/www/ /home/ec2-user/environment
sudo chown ec2-user:ec2-user /var/www/html
Notice that the VS Code IDE file browser currently does not display the Apache web server default web directory.

The first command creates a symlink from the default VS Code IDE workspace to the /var/www directory that contains your web server files.

The second command changes ownership of the html subdirectory so that the ec2-user (which you are logged in as) can edit and create new files in it.

<img width="595" height="40" alt="image" src="https://github.com/user-attachments/assets/c4721d8e-f2f3-4d5f-a818-bb6c6cab5587" />

Next, you create a test webpage.

In the file browser, expand the CafeWebServer > www directory, and choose the html directory.

Choose Menu , choose File and choose New File .

In the text editor tab, paste the following line:

<html>Hello from the café web server!</html>
Choose File > Save, and save the file in the html directory as index.html.

Make the website accessible from the internet.

In this step, you need to verify and update the configurations that make the webpages (which are hosted on the web server) accessible from the internet.

Expand each tip for help solving the challenge.

Tip #1
In the Amazon EC2 console, locate the public IPv4 address of the EC2 instance. In a new browser tab, enter `http://<public-ip>:8000` and replace with the public IPv4 address, and open the page. Does the message that you entered into index.html file load in the browser?

Tip #2
To allow inbound HTTP traffic on TCP port 8000 from anywhere, add the new rule in the security group of the EC2 instance as needed.
<img width="1839" height="420" alt="image" src="https://github.com/user-attachments/assets/145deb0d-b352-4522-a344-68628f89786a" />
<img width="1085" height="232" alt="image" src="https://github.com/user-attachments/assets/a273c783-25fe-42bf-a953-77ccec4539c7" />


New business requirement: Installing a dynamic website application on the EC2 instance (challenge #2)
In the previous challenge, you configured the EC2 instance. You now know that PHP is installed and that the application environment has a running relational database. Also, the environment has a running web server that can be accessed from the internet. You now have the basic setup for hosting a dynamic website for the café.

In the second part of this lab, you take on the role of Sofía and install the café application on the EC2 instance.

Task 4: Installing the café application
To download and extract the web server application files, run the following commands:

cd ~/environment
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/03-lab-mod5-challenge-EC2/s3/setup.zip
unzip setup.zip
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/03-lab-mod5-challenge-EC2/s3/db.zip
unzip db.zip
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/03-lab-mod5-challenge-EC2/s3/cafe.zip
unzip cafe.zip -d /var/www/html/
cd /var/www/html/cafe/
wget https://docs.aws.amazon.com/aws-sdk-php/v3/download/aws.zip
wget https://docs.aws.amazon.com/aws-sdk-php/v3/download/aws.phar
unzip aws -d /var/www/html/cafe/
chmod -R +r /var/www/html/cafe/
Notice how the file browser now shows the .zip files that you downloaded.

You also extracted these archive files, which created the cafe, db, and setup directories in your work environment.

Next, you observe how the application is designed to work.

To open the www/html/cafe/index.php source code file in the VS Code IDE, select (double-click) the file.

Notice that this file has HTML code in it, but it also contains sections that are enclosed in  elements. These elements make calls to other systems and resources. For example, on line 18, you see that the PHP code references a file named getAppParameters.php.

Open the getAppParameters.php file in the code editor.

Notice on line 3 of this file that the AWSSDK is invoked.

In the subsequent sections, the web application creates a client that connects to Secrets Manager. The application then retrieves seven parameters from Secrets Manager. Those parameters have not been created in Secrets Manager yet, but you do that next.

To configure the application parameters, in the bash terminal, run the following commands:

cd
cd environment/setup/
./set-app-parameters.sh
The shell script that you just ran issues AWS Command Line Interface (AWS CLI) commands. These commands add the secrets that the application will use from Secrets Manager.

On the AWS Management Console, in the search box, enter and choose Secrets Manager to open the Secrets Manager console.

From the panel on the left, choose Secrets.

There are now seven parameters stored as secrets. 

Note: It might take a few minutes for these parameters to be stored as secrets. If you don't see the secrets, wait a few minutes, and choose Refresh.

The café application's PHP code references these values (for example, so that it can retrieve the connection information for the MySQL database).

Choose the /cafe/dbPassword parameter.

Choose Retrieve secret value, and copy this value to your clipboard. 
<img width="1908" height="720" alt="image" src="https://github.com/user-attachments/assets/80c76592-207c-46c4-9218-c396fe87779c" />


You use this value in a moment.

To configure the MySQL database to support the café application, in the VS Code IDE bash terminal, run the following commands:

cd ../db/
./set-root-password.sh
./create-db.sh
	Next, you observe the database tables that were created.
<img width="590" height="182" alt="image" src="https://github.com/user-attachments/assets/96df8a93-d014-4ff7-ba74-0c688b586578" />


To connect the terminal-based MySQL client to the database, run the following command:

mysql -u admin -p
When you are prompted for the database password, paste the dbPassword parameter value that you copied.

You should now see a mysql> prompt, which indicates that you are now connected to the MySQL database that runs on this EC2 instance.

The information displayed when you connect to the MariaDB database by using a MySQL client.

To observe the contents of the database (specifically, the tables that support the café web application), run the following commands:

show databases;
use cafe_db;
show tables;
select * from product;
exit;
<img width="1376" height="740" alt="image" src="https://github.com/user-attachments/assets/ce6454a6-258f-450d-8390-8c72e3988cae" />

To update the time zone configuration in PHP, in the bash terminal, run the following commands:

sudo sed -i "2i date.timezone = \"America/New_York\" " /etc/php.ini
sudo service httpd restart
The first command configures the time zone in the PHP software.
<img width="713" height="55" alt="image" src="https://github.com/user-attachments/assets/8f622fb1-96f4-4909-9fc2-bf33f70a7be0" />


The second command restarts the web server so that the web server notices the configuration update.

To test whether the café website is working and can be accessed from the internet, in a new browser tab, enter http://<public-ip>:8000/cafe and replace <public-ip> with the public IPv4 address of the EC2 instance.

Resolve the issue with the website.

In this step, you need to figure out how to make the café website function correctly.

Here's a list of what does work:

The test page at http://<public-ip>:8000/ loads, so you know that the web server works and is accessible from the internet.

You also know that the MySQL database is running and contains tables and data to support the application.

What could be the issue?

Expand each tip for help solving the challenge.

Tip #1
Tip #2
Tip #3
Tip #4
When you think you have fixed the issue, load the http://<public-ip>:8000/cafe page again. Does it load completely so that you can see the café menu items? If so, congratulations!
<img width="1911" height="402" alt="image" src="https://github.com/user-attachments/assets/e5f12a29-e13d-42c9-a622-1eb4dce3d467" />


Note: If you still can't solve the issue, you might find it helpful to run the grading script as documented in the Submitting your work section at the end of these lab instructions. The submission report that is generated can provide additional tips for parts of the lab that you didn't complete successfully. You can submit your work as many times as you like. Only the score that you achieve on the last submission will be retained.

Task 5: Testing the web application
In this task, you test placing an order.

In the browser tab where you have the http://<public-ip>:8000/cafe page open, choose Menu.

Submit an order for at least one of the menu items displayed.

Note: You might need to scroll down to find the Submit Order button.

Return to the menu page and place another order.

Go to the Order History page to see the order details for all the orders that you placed.

New business requirement: Creating development and production websites in different AWS Regions (challenge #3)
Everyone at the café is impressed with the new dynamic website that Sofía created. Customers are delighted that they can now place online orders and schedule dessert items for pickup. Customer satisfaction has increased because of reduced wait times.

However, another business requirement emerges, along with the praise. Martha and Frank would like to have two café websites:

One website that can be used as a development environment to mock up new features and web designs before they are released to customers

A separate website that hosts the production environment that customers use

Sofía discusses the new requirement with Mateo, an AWS systems administrator and engineer, when he comes into the café one morning for his coffee. He suggests that, ideally, the two environments would exist in different AWS Regions. Such a design would have the added benefit of providing more robust disaster recovery (DR) in the unlikely scenario when an AWS Region becomes temporarily unavailable.

Sofía is now very busy! As she accomplishes more impressive work, her skills become more in-demand.

Task 6: Creating an AMI and launching another EC2 instance
Because the café website already runs well on an existing EC2 instance, Sofía decides to duplicate it by creating an AMI from it. She then launches a new instance from the new AMI.

You continue to take on the role of Sofía for this task. Before you create an AMI out of this instance, you should create a new key pair, which might be important to have later in this lab.

To set a static internal hostname and create a new key pair on the EC2 instance, in the bash terminal, run the following commands, ensure that you are on prompt  environment$, if not , used cd .. 

sudo hostname cafeserver
ssh-keygen -t rsa -f ~/.ssh/id_rsa
For the two times that you are prompted for a passphrase, press Enter.
<img width="599" height="411" alt="image" src="https://github.com/user-attachments/assets/86cbf30c-b91f-4947-9e6d-b495b6e3a21b" />


To make the new key available to the SSH utilities, in the bash terminal, run the following command:

cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
In the AWS Management Console, browse to the Amazon EC2 console, and then select the instance.
<img width="630" height="31" alt="image" src="https://github.com/user-attachments/assets/4a6ee8c3-ec0a-4b6e-9e1c-7a86527c0e0a" />

Choose Actions > Images and templates > Create image.

Tip: Leave the Create Image dialog open in the browser tab while you answer some questions about AMIs.

Answering questions about AMIs
Your answers are evaluated when you choose the Submit button at the end of the lab.

Return to the browser tab that has the questions for this lab. You accessed this tab earlier.

If you need to find the page again, follow these steps:

At the top of these instructions, choose AWS Details.

Choose the Access the multiple choice questions link.

On the page, submit answers to the following questions:

Question 5: When you create an AMI from an instance, will the instance be rebooted?

Question 6: In what ways can you modify the root volume properties when you create an AMI from an instance?

Question 7: Can you add more volumes to an AMI that you create from an instance that only has one volume?

Next, you configure the new AMI.

On the Amazon EC2 console, in the Create Image page, for Image name, enter CafeServer.
<img width="1916" height="713" alt="image" src="https://github.com/user-attachments/assets/7596537e-03f4-4440-9cba-07b88bebff1b" />


Choose Create Image.

From the navigation menu, choose AMIs, and wait until the image status becomes Available. 

On the navigation menu, you might need to expand Images to find AMIs.
<img width="1919" height="840" alt="image" src="https://github.com/user-attachments/assets/235c2154-1bb0-49ed-8fdb-acccb0f05cae" />


The process typically takes about 2 minutes. 

Create an AMI in another AWS Region.

In this step, your objective is to create a new EC2 instance from the AMI that you just captured. However, you must create the new instance in the Oregon (us-west-2) AWS Region.

Expand each tip for help solving the challenge.

Tip #1
 

Tip #2
Tip #3
<img width="1919" height="782" alt="image" src="https://github.com/user-attachments/assets/623f7d37-5ac3-43e8-8c5f-0eb66db1faf9" />

Next, you create the new café instance from your AMI. 

To create the new café instance from your AMI, make sure you are in the Oregon Region, and configure the following options:

For Name and tags, choose Add additional tags, and configure the following options:

For Key, enter Name.

For Value, enter ProdCafeServer.
<img width="1919" height="822" alt="image" src="https://github.com/user-attachments/assets/47fb1136-8713-4ab2-9647-4bd9fb100e83" />


For Instance type, choose t2.small.
<img width="1152" height="246" alt="image" src="https://github.com/user-attachments/assets/8b1438f4-0c39-4247-9fb0-8e3e46a81f80" />


For Key pair (login), choose Proceed without a key pair. The key pair that you created earlier in this lab should work to connect to it if necessary.

For Network settings, choose Edit, and configure the following options:

For VPC - required, choose Lab VPC Region 2.

For Subnet, choose Public Subnet.

For Security Security group name, enter cafeSG, and configure the following options:
<img width="1024" height="545" alt="image" src="https://github.com/user-attachments/assets/0ae9e1e2-ea54-4065-8654-96af95796056" />


Set TCP port 22 so that it is open to anywhere.

Set TCP port 8000 so that it is also open to anywhere.
<img width="974" height="614" alt="image" src="https://github.com/user-attachments/assets/5d6e16d4-85dc-4499-96db-6051c03844c1" />



In the Advanced details section, for IAM instance profile, choose CafeRole.
<img width="999" height="206" alt="image" src="https://github.com/user-attachments/assets/c7467f64-2bd0-4beb-935c-1f69c3fd1e35" />


Choose Launch instance.

Wait for the new instance to have a Public IPv4 DNS value assigned to it even if the status of the instance is still not Available.

Copy the Public IPv4 DNS value. You use it soon.

Next, you create the needed Secrets Manager secrets in the new AWS Region.

Return to the VS Code IDE in the N. Virginia (us-east-1) Region, and open the CafeWebServer/setup/set-app-parameters.sh file in the text editor.

Edit region as follows, line 15 (approx.) of the file to match this setting:

region="us-west-2"
Edit line 21 (approx.) to match this setting (where <public-dns-of-ProdCafeServer-instance> is the DNS of the ProdCafeServer instance):

publicDNS="<public-dns-of-ProdCafeServer-instance>"
   Note: The line should still contain the quotation marks, but it should not contain the brackets (< >).
   <img width="1450" height="668" alt="image" src="https://github.com/user-attachments/assets/5558de71-05b6-465f-b442-e0e80dcce861" />


  This example shows what line 15 should look like and how line 21 should be formatted. However, the value of your public DNS will be different.

The section of the application parameters setup file where the Region and DNS are updated.

Choose File > Save.

To run this script, run the following command IDE terminal.

cd ~/environment/setup/
./set-app-parameters.sh
 

In the bash terminal below the text editor, you should see output that's formatted in JSON. This output indicates that the parameters script ran successfully.

If the script encountered an issue, review this information for troubleshooting tips.
Note: By changing the AWS Region details and running this script again, you create the same parameters that you created earlier in the us-east-1 Region of Secrets Manager. However, this time, you created these parameters in the Oregon Region.

Task 7: Verifying the new café instance  
Return to the EC2 Console in the Oregon Region, and verify that the new ProdCafeServer instance is running.

Copy the Public IPv4 address, and load it in a web browser.

The Hello from the cafe web server! message should display.

Load the http://<public-ip>:8000/cafe/ URL in a browser tab.

The entire café website should display.

Load the Menu page.

The full Menu page should load, and the order-placing functionality should work.

Place an order to verify that the website is working as intended.

If you encountered issues loading the Menu page, try these troubleshooting tips.

The grading script can provide additional tips for parts of the lab that you didn't complete successfully. You can submit your work as many times as you like. Only the score that you achieve on the last submission will be retained.

If you want to connect to the new EC2 instance in the Oregon (us-west-2) Region to do some troubleshooting, run the following command from the VS Code IDE IDE in us-east-1:

ssh -i ~/.ssh/id_rsa ec2-user@<public-ip-of-ProdCafeServer>
Note that <public-ip-of-ProdCafeServer> is the public IP address of the ProdCafeServer instance.

Update from the café
Sofía is now a hero at the cafe. She created a dynamic website, and she also created a duplicate version of the same website that runs in a second AWS Region.

Sofía decides to designate the first EC2 instance that she created—the one in the us-east-1 Region—as the development instance. The second instance she created—the one in the Oregon (the us-west-2) Region—is the production instance.

This way, Sofía and any other application developers can test application enhancements on the development site without affecting the production site. Then, when the developers decide that the enhancements look good and they have fully tested them, they can migrate the code to the production site.

Sofía explained to Frank and Martha what she had done. They were pleased to know that the website can now take online orders. They were also glad to hear that they can now test new enhancements to the website without immediately exposing those changes to customers.
