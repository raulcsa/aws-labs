# Task 1: Creating an Amazon RDS database

In this task, you will create a MySQL database in your virtual private cloud (VPC). MySQL is a popular open source relational database management system (RDBMS), so there are no software licensing fees.

* At the top of the AWS Management Console, in the search box, enter **RDS** and choose **Aurora and RDS**.
* Under **Create a database**, choose **Create a database**.

<img width="1648" height="611" alt="image" src="https://github.com/user-attachments/assets/988ab333-1119-4f20-bae5-a1fd921b8747" />

* Under **Engine options**, select **MySQL**.

<img width="1647" height="424" alt="image" src="https://github.com/user-attachments/assets/ec16f536-cc3c-4362-8875-ad780aa77f26" />

> The options include several use cases, ranging from enterprise-class databases to Dev/Test systems. In the options, you might notice Amazon Aurora. Aurora is a MySQL-compatible system that was re-architected for the cloud. If your company uses large-scale MySQL or PostgreSQL databases, Aurora can provide enhanced performance.

### Set the templates and availability and durability options:

* Under the **Templates** section, select **Free tier**.

<img width="1615" height="356" alt="image" src="https://github.com/user-attachments/assets/a06fe9c3-60f5-4750-a345-871a08b0eb13" />

> **Note:** Under the **Availability and durability** section, **Single-AZ DB instance** is automatically chosen.

<img width="1623" height="739" alt="image" src="https://github.com/user-attachments/assets/389dfa24-2346-47e9-bd2c-34bb3ce436ed" />

### Under the Settings section, configure these options:

* **DB instance identifier:** `inventory-db`
* **Master username:** `admin`

Under **Credentials management**, choose **Self managed** and configure as follows:
* **Master password:** `lab-password`
* **Confirm master password:** `lab-password`

<img width="1651" height="686" alt="image" src="https://github.com/user-attachments/assets/4185a11b-80b0-4704-bbb3-1f422d29852b" />

### Under the Instance configuration section, configure these options:

* Select **Burstable classes (includes t classes)**.
* Select `db.t3.micro`.

### In the Storage section next:

* For **Storage type** choose **General Purpose SSD (gp2)** from the dropdown menu.
* For **Allocated storage** enter `20`.
* Expand **Storage autoscaling**.
* Clear or Deselect **Enable storage autoscaling**.

<img width="1111" height="727" alt="image" src="https://github.com/user-attachments/assets/d2a0c52e-97af-43cf-bd5f-117fcec93c01" />

### Under the Connectivity section, configure these options: 

* **Virtual Private Cloud (VPC):** `Lab VPC`
* **DB subnet group:** Keep the default selection.

> **Note:** The subnet group created as part of the lab setup has two private subnets, one per Availability Zone (AZ). This is required by the RDS service, in case the user changes the configuration to Multi-AZ in future.

* **VPC security group (firewall):** * Choose **Choose existing**.
  * Under **Existing VPC security groups**, from the dropdown, choose **DB-SG**.
  * Remove the default security group.

<img width="1478" height="731" alt="image" src="https://github.com/user-attachments/assets/48ae81c7-c0c7-4cff-9d06-c95d7e21500a" />

* Observe that under the **Monitoring** section, **Database Insights - Standard** is already chosen.

### Expand the Additional configuration panel, then configure these settings:

* **Initial database name:** `inventory`

> **Note:** This is the logical name of the database that will be used by the application. Feel free to review the many other options displayed on the page, but leave them set to their default values. Options include automatic backups, the ability to export log files, and automatic version upgrades. The ability to activate these features through check boxes demonstrates the power of using a fully managed database solution instead of installing, backing up, and maintaining the database yourself.

<img width="1469" height="751" alt="image" src="https://github.com/user-attachments/assets/46638c0e-85a1-4ac2-8071-a75ea1c32b13" />

* Choose **Create database** (at the bottom of the page).

You should receive a message indicating that your database is being created. Before you continue to the next task, the database instance status must be **Available**. This process might take several minutes.

> **Note:** If you receive a prompt *Suggested add-ons for inventory-db*, choose **Close**.

<img width="1679" height="330" alt="image" src="https://github.com/user-attachments/assets/ce7e9989-075a-428f-bfd5-ee3a12793dc2" />

---

# Task 2: Configuring web application communication with a database instance

This lab automatically deployed an Amazon Elastic Compute Cloud (Amazon EC2) instance with a running web application. You must use the IP address of the instance to connect to the application.

1. At the top of these instructions, from the **AWS Details** section, copy the value for `AppServerPublicIP`.
2. Open a new web browser tab, paste the IP address you copied into the address bar, and then press **ENTER**.
3. The web application should appear. It does not display much information because the application is not yet connected to the database.
4. Choose **Settings**.
<img width="1670" height="490" alt="image" src="https://github.com/user-attachments/assets/d90dcfac-fe0c-4fde-8fb3-8fc762cbfaf5" />


You can now configure the application to use the RDS DB instance you created earlier. You will first retrieve the Database Endpoint so that the application knows how to connect to a database.

5. Return to the **AWS Management Console**, but do not close the application tab. (You will return to it soon).
6. From the **Services** menu, choose **RDS** to open the RDS console.
7. In the left navigation pane, choose **Databases**.
8. Choose `inventory-db`.
9. Go to the **Connectivity & Security** section and copy the **Endpoint** to your clipboard.
   * *It should look similar to this example: `inventory-db.crwxbgqad61a.rds.amazonaws.com`*
<img width="1406" height="733" alt="image" src="https://github.com/user-attachments/assets/70260f4c-a059-4392-8e5f-224e062d6e18" />

10. Return to the browser tab with the Inventory application, and enter these values:
    * **Endpoint:** Paste the endpoint you copied earlier
    * **Database:** `inventory`
    * **Username:** `admin`
    * **Password:** `lab-password`
11. Choose **Save**.
<img width="1679" height="486" alt="image" src="https://github.com/user-attachments/assets/1e12498c-c70c-4922-a555-a1d54e8fbf47" />

<img width="1617" height="447" alt="image" src="https://github.com/user-attachments/assets/475a090a-aac3-4819-b994-df37b6b7a70c" />

The application stores this information into AWS Secrets Manager as a Secret and uses it to connect to the database, load some initial data, and display instance information. Secrets Manager helps you improve your security posture, because you no longer need hard-coded credentials in application source code. You replace hard-coded credentials with a runtime call to the Secrets Manager service to retrieve credentials dynamically when you need them.

### Testing the Application

* Add inventory, edit, and delete inventory information by using the web application.
<img width="1679" height="537" alt="image" src="https://github.com/user-attachments/assets/51c044f0-809a-46a4-b5df-d2964d67f799" />


The inventory information is stored in the Amazon RDS MySQL database that you created earlier in the lab. This means that any failure in the application server will not lose any data. It also means that multiple application servers can access the same data.

* Insert new records into the table. Ensure that the table has 5 or more inventory records before submitting your work.

🎉 **You have now successfully launched the application and connected it to the database!**

> **Optional:** You can access the saved secrets values in the Secrets Manager console.
<img width="1679" height="415" alt="image" src="https://github.com/user-attachments/assets/34550090-cdb4-45f3-8722-7f36974854b1" />
