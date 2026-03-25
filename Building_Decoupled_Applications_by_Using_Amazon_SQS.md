Task 1: Installing the image processing application
In this task, you download the required files and install the image processing application on the IDE. Then you configure a security group and the S3 bucket permissions required for the communication between the application and AWS services.

Task 1.1: Logging in to the Lab IDE
In this first task, you will connect to VS Code IDE and configure the environment to support the development that you will work on during the rest of the lab.

At the top of these instructions, choose i AWS Details   

Copy values from the table for the following and paste it into an editor of your choice for use later.

LabIDEURL

LabIDEPassword

EC2IP

Phase1bucket

Phase2bucket

In a new browser tab, paste the value for LabIDEURL to open the IDE.

On the prompt window Welcome to code-server: 

Enter the value for LabIDEPassword you copied to the editor earlier

Choose Submit to open the VS Code IDE similar to below.

Note: User Interface is displayed as shown below.

The IDE includes the following:

A bash terminal in the bottom-right panel.

A file browser in the left panel that shows files in the /home/ec2-user/environment directory on the instance.

A file editor in the upper-right panel. If you select a file in the file browser, such as the README.md file, it displays in the editor.

Note: You IDE is now ready for use.

Task 1.2: Downloading files and installing the application
In the Lab IDE,  the bash terminal window at the bottom. (You can resize the window by dragging the terminal window up.)

At the ~/environment prompt, to download the application files and install the web server, run the following commands:

wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/17-lab-mod13-guided-SQS/code.zip
unzip code.zip 
cd phase_1/web_server_1/
npm install   

Note: You may need to Allow paste into the bash terminal for the first time. 

In the same window, to open another terminal, choose + > New Terminal.

In the new terminal, run the following commands to install the application server:

cd phase_1/app_server_1/
npm install

You have now installed the NodeJs module and the application, which has a web server and an application server. Next, you configure other services.

<img width="1025" height="327" alt="image" src="https://github.com/user-attachments/assets/56756c1b-3b7b-429d-ba98-f6953b283fed" />

Note: Keep the IDE open.

Task 1.3: Configuring the S3 bucket permissions
In this lab, the S3 bucket that you use for storing images is private by default. In this task, you adjust the permissions so that you can access the bucket from your local computer.

On the AWS Management Console, in the search bar at the top, enter and choose S3 to open the Amazon S3 console.

In the navigation pane, choose Buckets.

Choose the link for the bucket with phase1bucket in the name.

Choose the Permissions tab.

For Block public access (bucket settings), choose Edit.

Clear Block all public access.

Choose Save changes.

<img width="1679" height="563" alt="image" src="https://github.com/user-attachments/assets/f13b8cbc-00a1-4832-9793-a7256a4dfabe" />

In the Edit Block public access (bucket settings) window, enter confirm, and then choose Confirm.

For Bucket policy, choose Edit.

In the Policy field, enter the following code. 

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicRead",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:*",
            "Resource": "[bucket ARN]/*",
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": "[ServerIP]/32"
                }
            }
        }
    ]
}
In the code that you entered, replace the following values:

Replace [bucket ARN] with the Bucket ARN on the same window. 

Replace [ServerIP] with the public IP address of your local computer. You can use websites such as WhatsMyIP to find this information. 

Ensure that the policy editor does not flagged any errors, and then choose Save changes.

<img width="978" height="583" alt="image" src="https://github.com/user-attachments/assets/70f4ef06-1be9-4240-8c7a-9bf632fead3d" />

You have now configured a security group and the S3 bucket permissions required for the image processing application. Next, you configure the application parameters and start the application.

Task 1.4: Configuring and starting the application
In this task, you configure three separate configuration files for each application tier: browser, web application, and application server.

In the Lab IDE that you kept open, from the explorer on the left, expand the folder titled phase_1.

First, you make changes for the browser tier. 

Open the following file for editing in the IDE: web_server_1/static/js/config.js

In the file, replace the following values with the variables that you copied into a text editor earlier.

Note: Replace the values and the brackets []. You can use the Find/Replace capability of the IDE (press Ctrl+f in the Lab IDE).

Parameter Name	Value
CONFIG.FULL_URL_STR	http://[EC2IP]:8008/upload
CONFIG.FULL_IMAGE_PATH_STR	https://[Phase1bucket].s3.amazonaws.com
CONFIG.FULL_IMAGE_URL_STR	http://[EC2IP]:8008/get-image-urls
After replacing the values, choose File > Save.

<img width="1066" height="408" alt="image" src="https://github.com/user-attachments/assets/00b2d7f5-8f57-432f-a095-a278dc1a3565" />

To close the file, on the tab with the file, choose X.

Next, you make configuration changes for the web tier. 

Open the following file for editing in the IDE: web_server_1/libs/config.js

In the file, replace the following values with the variables that you copied into a text editor file earlier.

Note: Replace the values and the brackets []. You can use the Find/Replace capability of the IDE.

Parameter Name	Value
CONFIG.PROCESSING_URL_STR	http://[EC2IP]::8009/process
CONFIG.S3.IMAGE_BUCKET_NAME_STR	[Phase1bucket]
After replacing the values, choose File > Save.

<img width="952" height="545" alt="image" src="https://github.com/user-attachments/assets/65e0175f-c594-427f-84b9-b4f3d5a837fa" />

To close the file, on the tab with the file, choose X.

Finally, you make configuration changes for the application server tier. 

Open the following file for editing in the IDE: app_server_1/libs/config.js

In the file, replace each parameter name and the brackets with the corresponding value.

Parameter Name	Value
CONFIG.S3.IMAGE_BUCKET_NAME_STR	[Phase1bucket]
After replacing the value, choose File > Save.

To close the file, on the tab with the file, choose X.

Note: Be careful while assigning or replacing these values because they affect the running of the application.

Now that you have updated the application configuration, you are ready to start the servers.

<img width="1050" height="475" alt="image" src="https://github.com/user-attachments/assets/ef2924bd-0b62-4ff0-978b-0678c9adccdc" />

In the Lab IDE window, on the first terminal, run the pwd command to ensure that the current working directory is /phase_1/web_server_1/.

To start the web server, run the following command:

npm start
   Note: A message is displayed that says the App is listening on port 8008.

<img width="929" height="205" alt="image" src="https://github.com/user-attachments/assets/e1455e9c-1ddd-439b-9017-ac5299ed328e" />

In the Lab IDE terminal, on the other window, run the pwd command to ensure that the current working directory is cd /phase_1/app_server_1/.

To start the application server, run the following command:

npm start
  Note: A message is displayed that says the App is listening on port 8009.

<img width="845" height="234" alt="image" src="https://github.com/user-attachments/assets/c449249d-5ec1-4170-8711-4e68c07ab3c2" />

Leave this window running. Your application is now ready to be tested.

Task 2: Testing the application
To test the application, in the following link, replace [EC2IP] with the phase 1 public IPv4 address that you pasted into a text editor earlier: http://[EC2IP]:8008. Paste your revised link into a browser tab.

<img width="1615" height="740" alt="image" src="https://github.com/user-attachments/assets/585f95fc-80c8-4e8e-88f8-43e7969a4279" />

The Image Tinter application is displayed in the browser.

To check for images that are processing, choose Start checking for images.

Next, you upload an image.

While processing is happening in the background, choose Choose File, navigate to any file that you want to upload, select the file, and choose Submit. 

The processed image is displayed as it becomes available.

Once you are done, choose Stop checking for images.

To delete a processed image, choose the Bin icon on the image.

Congratulations! You have successfully completed phase 1 of the application building process.

<img width="1396" height="688" alt="image" src="https://github.com/user-attachments/assets/f1af5e41-f3dd-4370-9ee3-7ab7bc5fcba8" />

Analysis of phase 1
Review the phase 1 architecture diagram at the beginning of this lab. Once you upload images, the web server is communicating directly and regularly with the application server to process and display the images. This architecture uses tight coupling, which can create problems. In a tightly coupled architecture, if one component of the system breaks, the entire application breaks, and the application as a whole is unusable until you fix the broken component. 

One of the solutions for such problems could be using decoupling to separate components.

In the next phase, you create a decoupled architecture by introducing AWS services such as Amazon SQS and Amazon SNS.

You can close the application and Lab IDE that you opened for phase 1. You use a new Lab IDE for phase 2.

Phase 2: Building the application with a decoupled architecture
In this phase, you create a new version of your application based on a decoupled architecture, which improves the availability, scaling, and performance of the application in a high-volume scenario. You achieve this by using a combination of AWS services, such as Amazon SQS and Amazon SNS. Refer to the phase 2 architecture diagram for more information. 

First, you install the application in a new Lab IDE and then configure the Amazon SQS and Amazon SNS services and Amazon S3 notification. Finally, you configure the application parameters required for running the application in a new architecture.

Task 3: Installing the application for Phase2
In this task, you download the required files and install the image processing application in the Lab IDE. Then you configure a security group to facilitate communication between the application running on the Lab IDE and the user interface (browser).

Task 3.1: Downloading files and installing the application
In the Lab IDE terminal window, at the voclabs:~/environment prompt, to install the web server, run the following commands:

wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/17-lab-mod13-guided-SQS/code.zip
unzip code.zip 
cd phase_2/web_server_2/
npm install

In the same window, to open another terminal, choose + > New Terminal.

In the new terminal, run the following commands to install the 

cd phase_2/app_server_2/
npm install

You have now installed the NodeJs module and the application, which has a web server and an application server. Next, you configure other services.

Keep the Lab IDE open.

Task 4: Configuring Amazon SQS
On the AWS Management Console, in the search box, enter and choose SQS to open the Amazon SQS console.

Choose Create queue.

On the Create queue page, for Name, enter ImageApp.

Note: Leave the other properties unchanged.

Choose Create queue.

<img width="1459" height="603" alt="image" src="https://github.com/user-attachments/assets/5ac1d1ea-544f-4cda-b135-71e2fbb1710b" />

On the ImageApp page, in the Details panel, copy the URL into a text editor for later use.

Task 5: Configuring Amazon SNS
On the AWS Management Console, in the search box, enter and choose SNS to open the Amazon SNS console.

In the Create topic section, in the Topic name box, enter uploadnotification.

Choose Next step.

On the Create topic page, expand Access policy - optional, and then choose Advanced.

You now configure the policy to provide access to the phase 2 S3 bucket to send a notification for every upload.

In the JSON editor, delete the existing policy.

To add a new policy, copy and paste the following code into the JSON editor.

{
    "Version": "2012-10-17",
    "Id": "S3UploadNotification",
    "Statement": [
        {
            "Sid": "S3 SNS topic policy",
            "Effect": "Allow",
            "Principal": {
                "Service": "s3.amazonaws.com"
            },
            "Action": [
                "SNS:Publish"
            ],
            "Resource": "[SNS-topic-ARN]",
            "Condition": {
                "ArnLike": {
                    "aws:SourceArn": "arn:aws:s3:*:*:[Phase2bucket]"
                },
                "StringEquals": {
                    "aws:SourceAccount": "[Lab-account]"
                }
            }
        }
    ]
}                  
In this code, replace the following values:

Replace [Phase2bucket] with value for the Phase2bucket you copied into the text editor at the beginning of the lab.

Replace [Lab-account] with the 12-digit Account ID from the upper-right corner of your AWS console.

<img width="1148" height="410" alt="image" src="https://github.com/user-attachments/assets/e6a2bbe0-ebea-44be-a730-91c8c9dec07e" />

Choose Create topic.

From the Details pane, copy the ARN for the topic, and choose Edit.

Expand Access policy - optional.

In the JSON editor, replace [SNS-topic-ARN] with the ARN that you just copied.

Choose Save changes.

<img width="894" height="410" alt="image" src="https://github.com/user-attachments/assets/1717562f-aaaa-4952-96ee-058f072c26fc" />

Now that you have provided the necessary permissions for the S3 bucket to send an SNS notification, you configure the phase 2 S3 bucket event notification. 

Task 6: Configuring Amazon S3 permissions and event notifications
In this task, you configure the S3 bucket to send a notification to the SNS topic that you created as soon as your application uploads an image.

On the AWS Management Console, in the search box, enter and choose S3 to open the Amazon S3 console.

Choose the bucket with phase2bucket in the name.

Choose the Permissions tab.

For Block public access (bucket settings), choose Edit.

Clear Block all public access.

Choose Save changes.

In the Edit Block public access (bucket settings) window, enter confirm, and then choose Confirm.

For Bucket policy, choose Edit.

In the Policy window, copy and paste the following code:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicRead",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:*",
            "Resource": "[bucket ARN]/*",
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": "[ServerIP]/32"
                }
            }
        }
    ]
}
In this code, replace the following values: 

Replace [bucket ARN] with the Bucket ARN on the same window.

Replace [ServerIP] with the public IP address of your local computer. You can use websites such as WhatsMyIP to find this information. 

Choose Save changes.

<img width="1445" height="812" alt="image" src="https://github.com/user-attachments/assets/2d76bc8c-0a47-4cd7-9a67-ec1e0f5e56ff" />

Next, you choose the properties for the bucket.

Choose the Properties tab.

In the Event notifications section, choose Create event notification, and configure the following options:

Event name: Enter SendtoSns.

Object creation: Select All object create events.

<img width="1679" height="827" alt="image" src="https://github.com/user-attachments/assets/1e817187-4f0d-4df4-aa73-d793d8a14486" />

Destination: Choose SNS topic.

SNS topic: From the dropdown list, choose the uploadnotification topic that you created earlier.

Choose Save changes.

<img width="1665" height="611" alt="image" src="https://github.com/user-attachments/assets/5f7409ad-98d5-4110-9f6e-1c3825cd35c4" />

Task 7: Creating Amazon SNS subscriptions
Now that you have created an Amazon S3 event to send a notification to Amazon SNS, you create a subscription for the SNS topic to send a message to the queue. You also configure email subscriptions for the user.

Task 7.1: Configuring an Amazon SQS subscription
On the AWS Management Console, in the search box, enter and choose SQS to open the Amazon SQS console.

Choose the ImageApp queue that you created.

For SNS Subscriptions, choose Subscribe to Amazon SNS topic.

On the Subscribe to Amazon SNS topic page, from the dropdown list, choose the uploadnotification SNS topic.

Choose Save.

<img width="1656" height="445" alt="image" src="https://github.com/user-attachments/assets/2d87d46a-33b7-44c6-83ff-43ec1a6f9ecb" />

Task 7.2: Configuring an Amazon SNS email subscription
On the AWS Management Console, in the search box, enter and choose SNS to open the Amazon SNS console.

In the navigation pane, choose Topics, and then choose uploadnotification.

From Subscriptions, choose Create subscription.

On the Create subscription page, configure the following options:

Protocol: Choose Email.

Endpoint: Enter an email address that you can access now.

Choose Create subscription.

An email is sent to your email address for confirmation. 

Go to your email account, and open the email message with the subject AWS Notification - Subscription Confirmation.

<img width="1658" height="797" alt="image" src="https://github.com/user-attachments/assets/16bb216e-ca3f-44bc-a091-81aa8e60b1a4" />

In the email, choose the Confirm subscription link.

You have now configured all the required services for the image processing application. Next, you configure the application parameters and start the application.

<img width="1132" height="626" alt="image" src="https://github.com/user-attachments/assets/296df2b0-c877-4a60-b81a-e6fa02921a3f" />

Task 8: Configuring parameters and starting the application
In this task, you configure three separate configuration files for each application tier: browser, web application, and application server.

In the Lab IDE that you kept open, from the explorer on the left, expand the folder titled phase_2.

  First, you make changes for the browser tier. 

Open the following file for editing in the IDE: web_server_2/static/js/config.js

Assign or replace the following values with the variables that you copied into a text editor earlier.

Note: Replace the values and the brackets []. You can use the Find/Replace capability of the IDE (press Ctrl+f in the Lab IDE).

Parameter Name	Value
CONFIG.FULL_IMAGE_URL_STR	http://[EC2IP]:8007/get-image-urls
CONFIG.FULL_IMAGE_PATH_STR	https://[Phase2bucket].s3.amazonaws.com
CONFIG.ARN_TRUNC_URL_STR	[Phase2bucket]
CONFIG.APP_SERVER_URL_STR	http://[EC2IP]:8010
CONFIG.WEB_SERVER_URL_STR	http://[EC2IP]:8007
After replacing the values, choose File > Save.

To close the file, on the tab with the file, choose X.

Next, you make configuration changes for the web tier.  

Open the following file for editing in the IDE: web_server_2/libs/config.js

In the file, replace each parameter name and the brackets with the corresponding value.

Parameter Name	Value
CONFIG.S3.IMAGE_BUCKET_NAME_STR	[Phase2bucket]
After replacing the value, choose File > Save.

To close the file, on the tab with the file, choose X.

Finally, you make configuration changes for application server tier. 

Open the following file for editing in the IDE: app_server_2/libs/config.js

In the file, replace each parameter name and the brackets with the corresponding value.

Parameter Name	Value
CONFIG.S3.IMAGE_BUCKET_NAME_STR	[Phase2bucket]
CONFIG.SQS.HTTPS_URL_STR	[Phase2 SQS URL]
Note: The Phase2 SQS URL is the URL for the ImageApp.

After replacing the values, choose File > Save.

To close the file, on the tab with the file, choose X.

Note: Be careful while assigning or replacing these values because they affect the running of application.

Now that you have updated the application configuration, you are ready to start the servers.

In the Lab IDE window, on the first terminal, run the pwd command to ensure that the current working directory is /phase_2/web_server_2/.

To start the web server, run the following command:

npm start
   Note: A message is displayed that says the App is listening on port 8007.

In the Lab IDE terminal, on the other window, run the pwd command to ensure that the current working directory is cd /phase_2/app_server_2/.

To start the application server, run the following command:

npm start
  Note: A message is displayed that says the App is listening on port 8010.

Your application is now ready to be tested.

Task 9: Testing the application
 

To test the application, in the following link, replace [EC2IP] with the phase 2 public IPv4 address that you pasted into a text editor earlier: http://[EC2IP]:8007. Paste your revised link into a browser tab.

The Image Tinter (Improved) application is displayed on the browser.

Choose Images Lookup.

Images that are uploaded to the S3 bucket and ready to be processed are displayed. (Notice that processing has not yet started.) Images are still in their original appearance and display the current processing status.

To upload additional images while processing is happening in the background, choose Choose File, navigate to any file that you want to upload, select the file, and choose Submit. 

Notice the email notification that you receive when an image is uploaded.

To poll the queue for processing images, choose Poll SQS.

The processed images are displayed to the user as processing occurs.

Notice the email notification that you receive when a processed image is uploaded.

To stop polling the queue, choose Stop Polling SQS.

When you are done, choose Stop Images Lookup.

To delete a processed image, choose the Bin icon on the image.

Congratulations! You have successfully completed phase 2 of the application building process.
