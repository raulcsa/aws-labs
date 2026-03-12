# Encrypting Data at Rest by Using AWS Encryption Options

Scenario
In this lab, you start with the following architectural components: 

An S3 bucket to store images 

An EC2 instance 

An AWS KMS key to be used for encryption in the lab

The following diagram shows these components.

<img width="459" height="401" alt="image" src="https://github.com/user-attachments/assets/f8546f34-7e70-432d-89bf-ee5c007f5154" />

## Task 1: Reviewing default encryption for objects in an S3 bucket
In this task, you upload an image file to an S3 bucket and review the default encryption provided by Amazon S3. 

Download the file named clock.png to your computer.

  Next, you locate the S3 bucket and analyze its encryption settings.

At the top of AWS Management Console, in the search box, search for and choose S3 to open the Amazon S3 console.

In the navigation pane, choose Buckets.

Choose the link for the bucket name that contains imagebucket.

Choose the Properties tab.

  In the Default encryption section, notice the setting Server-side encryption is automatically applied to new objects stored in this bucket.

   Note: Amazon S3 now applies server-side encryption with Amazon S3 managed keys (SSE-S3) as the base level of encryption for every bucket in Amazon S3.

<img width="1381" height="355" alt="image" src="https://github.com/user-attachments/assets/8c1c882b-19e8-4072-b6c4-503aaf034420" />

  Next, you upload a file to the bucket.

At the top of the page, choose the Objects tab.

Choose Upload.

Choose Add files.

Browse to and select the clock.png file that you downloaded to your computer.

Expand the Permissions option.

Choose Grant public-read access.

<img width="1653" height="750" alt="image" src="https://github.com/user-attachments/assets/cf9c3ab7-3218-4030-8ddb-76f149f4e139" />

<img width="1349" height="431" alt="image" src="https://github.com/user-attachments/assets/938c10db-3a2e-488a-bcc4-61dcf6fba31b" />

  The following warning message appears: Granting public-read access is not recommended.

To acknowledge the warning, select I understand the risk of granting public-read access to the specified objects.

At the bottom of the page, choose Upload.

After the upload completes, in the upper-right corner, choose Close.

  The clock.png file is now listed as an object in the bucket.

To open the file properties, choose the clock.png file.

In the Server-side encryption settings section, notice the following settings: 

This encryption setting is enabled, and the following message is displayed: Server-side encryption protects data at rest.

<img width="1311" height="129" alt="image" src="https://github.com/user-attachments/assets/70e24169-80e0-4e8a-8568-395a95d5a7d7" />

For Encryption type, the following message is displayed: Server-side encryption with Amazon S3 managed keys (SSE-S3).

To open the image, in the Object overview section, choose the Object URL. 

  You can use this method to access an Amazon S3 object from the public internet.

  The clock image opens in a browser tab. 

   Note: The object is encrypted, but while it displays, it has to be decrypted. The service handles this decryption transparently.

In this task, you reviewed the encryption settings on the bucket. You then uploaded an object to the bucket and accessed the object by using a public link for the object. Amazon S3 transparently decrypted the object before displaying it in the browser.

<img width="1260" height="818" alt="image" src="https://github.com/user-attachments/assets/0be8e241-df9c-4b83-b62f-9276df8d373f" />

## Task 2: Creating an AWS KMS key
In this task, you create a customer managed AWS KMS key. Later in the lab, you use the AWS KMS key that you create to generate, encrypt, and decrypt data keys. The data keys will be shared with Amazon EC2. The data keys are used to encrypt the actual data stored on EBS volumes.

In the AWS Management Console, in the search box, search for KMS and choose Key Management Service to open the AWS KMS console.

In the navigation pane, choose Customer managed keys.

   Tip: To open the navigation pane, choose the  menu icon in the upper-left corner. 

Choose Create key.

For Key type, choose Symmetric.

   Note: Symmetric keys never leave AWS KMS unencrypted. The key that you create will be a 256-bit secret key.

<img width="1507" height="564" alt="image" src="https://github.com/user-attachments/assets/b1e9c7df-4374-4113-a47a-6e03b2d7b0ec" />

Choose Next.  

For Alias, enter MyKMSKey.

<img width="1575" height="696" alt="image" src="https://github.com/user-attachments/assets/8ff73f49-254b-4b05-a8d5-6a332b75ebf2" />

Choose Next. 

On the Define key administrative permissions page, for Key administrators, search for and select the voclabs role.

<img width="1625" height="548" alt="image" src="https://github.com/user-attachments/assets/8fb411a3-eff3-432d-8207-70d5d99cfe62" />

   Note: Key administrators are users or roles that manage access to the encryption key. You are currently signed in to the console with the voclabs role. This user role is displayed in the upper-right corner of the console to the left of the AWS Region. 

Choose Next.

On the Define key usage permissions page, for Key users, search for and select the voclabs role again.

   Note: Key users are the users or roles that can use the key to encrypt and decrypt data. 

<img width="1604" height="669" alt="image" src="https://github.com/user-attachments/assets/1565ca92-494b-4588-858b-2d92133ad385" />
```
{
  "Id": "key-consolepolicy-3",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Enable IAM User Permissions",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::637423566699:root"
      },
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Sid": "Allow access for Key Administrators",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::637423566699:role/voclabs"
      },
      "Action": [
        "kms:Create*",
        "kms:Describe*",
        "kms:Enable*",
        "kms:List*",
        "kms:Put*",
        "kms:Update*",
        "kms:Revoke*",
        "kms:Disable*",
        "kms:Get*",
        "kms:Delete*",
        "kms:TagResource",
        "kms:UntagResource",
        "kms:ScheduleKeyDeletion",
        "kms:CancelKeyDeletion",
        "kms:RotateKeyOnDemand"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow use of the key",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::637423566699:role/voclabs"
      },
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
      ],
      "Resource": "*"
    },
    {
      "Sid": "Allow attachment of persistent resources",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::637423566699:role/voclabs"
      },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants",
        "kms:RevokeGrant"
      ],
      "Resource": "*",
      "Condition": {
        "Bool": {
          "kms:GrantIsForAWSResource": "true"
        }
      }
    }
  ]
}
```
Choose Next.

At the bottom of the Review page, choose Finish.

  Your key displays in the list of customer managed keys.

   Note: If you attempted this lab within the past 7 days, you might see an older key or older keys in this list with the status Pending deletion. The minimum amount of time that must transpire between when you request that a key be deleted and when it's actually deleted is 7 days. 

In this task, you created an AWS KMS key, which you use in the next task.

<img width="1679" height="297" alt="image" src="https://github.com/user-attachments/assets/e49c32dc-c3bc-47aa-92e8-a809b9b267e5" />

## Task 3: Creating and attaching encrypted data volume on an EC2 instance
In this task, you create an encrypted EBS volume by using the KMS key that you created in the previous task and attach it to your EC2 instance. When you attach the encrypted volume, the EC2 instance retrieves the data key from AWS KMS and uses it to decrypt the data on the EBS volume. In later tasks, you examine the CloudTrail event history to observe the calls made to the AWS KMS service.

First, you observe the root volume.

At the top of AWS Management Console, in the search box, search for and choose EC2 to open the Amazon EC2 console.

In the navigation pane, choose Instances.

  The LabInstance is listed. Note the Availability Zone for this EC2 instance. You need this information in a later step.

Choose the Instance ID link for the LabInstance.

Choose the Storage tab.

  In the Block devices section, notice that the volume that is attached indicates it is not encrypted. This is the root volume, which contains the guest operating system installation. It was created when the the EC2 instance was created.

<img width="1285" height="658" alt="image" src="https://github.com/user-attachments/assets/80e25136-3cf3-4218-8b01-f8f0a7ce0b1a" />

Next, you create the new encrypted volume.

From the navigation pane, choose Volumes.

Choose Create volume.

On the Create volume page, configure the following settings:

For Volume type, keep the default.

For Size (GiB), enter 1.

For Availability Zone, choose the Availability Zone where the EC2 instance exists. You checked for this information in a previous step.

For Encryption, select  Encrypt this volume.

For KMS key, choose MyKMSKey. Notice the details about the key that are displayed.

Choose Create volume.

<img width="1389" height="800" alt="image" src="https://github.com/user-attachments/assets/edc1705c-741f-4a63-a07d-b472a84e49b1" />

  The volume is created and will have a Volume state of Available soon.

  Next, you attach the new volume.

Choose the link for the volume that you created.

Choose Actions, and then choose Attach volume.

On the Attach volume page, for Instance, choose your EC2 instance from the dropdown list.

  A Device name is allocated and displayed similar to /dev/sdf.

Choose Attach volume.

<img width="1545" height="526" alt="image" src="https://github.com/user-attachments/assets/b1b409e6-81d5-450b-bfd2-f0c2be72bc3a" />

  The volume is attached to the instance.

Open a new browser tab, navigate to the the Amazon EC2 console, and choose Instances.

Select  LabInstance.

Choose the Storage tab.

  Two volumes are attached to the instance:

The root volume (8 GiB) is not encrypted.

The data volume (1 GiB) that you attached is encrypted and has a KMS key ID.

   Note: You might need to refresh the page to see the latest information for the attached volume.

   Note: Observe the second diagram to understand what happens when you attach an encrypted EBS volume to the instance.

In this task, you created a new encrypted data volume and attached it to the EC2 instance so that the data on it is now more secure.

<img width="1576" height="789" alt="image" src="https://github.com/user-attachments/assets/a7bc4264-f965-4f7f-be99-7bca2ec9a009" />

## Task 4: Disabling the encryption key and observing the effects
In this task, you temporarily disable the AWS KMS key that you previously used to encrypt the EBS volume. You then observe the effects that disabling the key has on accessing encrypted data.

First, you disable the AWS KMS key that you used to encrypt the EBS volume.

Navigate to the AWS KMS console.

In the navigation pane, choose Customer managed keys.

Select  MyKMSKey, and then choose Key actions > Disable.

<img width="502" height="382" alt="image" src="https://github.com/user-attachments/assets/6de80540-5d1f-46d8-a7da-1cedd7398651" />

In the Disable key window that appears, to confirm the action, select  Confirm that you want to disable this key, and then choose Disable key.

  Next, you detach the data volume from EC2 instance. It is necessary for this test to detach the instance. The encrypted data key is stored in memory and used to encrypt and decrypt all data going to and from the attached EBS volume. Amazon EBS retains the encrypted data key for later use in case the data key in memory is no longer available.

Navigate to the Amazon EC2 console.

In the navigation pane, choose Volumes.

Choose the link for the volume that you created.

Choose Actions > Detach volume.

<img width="1575" height="639" alt="image" src="https://github.com/user-attachments/assets/8d458192-6d98-45d1-9d8d-e12430584c64" />

In the Detach window that appears, choose Detach.

Next to Actions, choose the Refresh button to refresh the page.

Choose Actions > Attach volume.

For Instance, choose your instance from the dropdown list.

Choose Attach volume.

  A message similar to the following appears: Volume vol-072d7cf3173a13151 cannot be attached. The encrypted volume was unable to access the KMS key.

  The operation fails as expected because the AWS KMS key used to encrypt the data volume is now disabled and can no longer be used to decrypt the data.

<img width="1626" height="618" alt="image" src="https://github.com/user-attachments/assets/0f43db4c-4939-455e-8df9-18810502d386" />

  Next, you analyze the CloudTrail event history.

In the AWS Management Console, in the search box, search for and choose CloudTrail to open the CloudTrail console.

In the navigation pane, choose Event history.

   Tip: To open the navigation pane, choose the  menu icon in the upper-left corner.

   Note: CloudTrail provides an audit log of API calls that are made in the AWS account. The event history provides access to events from the last 90 days of account activity.

  The recent events are displayed.

Choose the link for the DisableKey event name.

  In the Event record section, review the details of the event. You should see information similar to the following record:

  DisableKey event record. Highlighted information includes principalId, eventName, keyId, and EBS object name.

  In the Details section, examine the event details. The Event time show that you disabled the AWS KMS key a few minutes ago.

<img width="1668" height="797" alt="image" src="https://github.com/user-attachments/assets/c6e44064-92b8-44d7-89cc-912b2c077847" />

  Next, you examine the AttachVolume event, which occurred just after the DisableKey event.

In the navigation pane, choose Event history.

Choose the link for the AttachVolume event name.

  In the Event record section, review the details of the event. You should see information similar to the following record:

  AttachVolume event record. Highlighted information includes principalId, eventName, keyId, and EBS object name.

  The details show that the request to attach the volume to the instance was unsuccessful. 

  This happened because the attach volume process noticed that the data volume was encrypted with the MyKMSKey, so Amazon EC2 contacted AWS KMS to provide the plaintext data key so it could decrypt the volume. AWS KMS denied the request because the AWS KMS key that was used to encrypt the data key that the EBS volume was encrypted with was disabled.

<img width="1659" height="787" alt="image" src="https://github.com/user-attachments/assets/67e67f45-77d8-4802-947d-0fa7785d9f8b" />

  Next, you re-enable the AWS KMS key and re-attach the EBS volume.

To re-enable the MyKMSKey, return to the AWS KMS console.

Select  MyKMSKey, and then choose Key actions > Enable.

To attach the volume again, return to the Amazon EC2 console.

In the navigation pane, choose Volumes.

Select your volume, and then choose Actions > Attach volume.

On the Attach volume page, for Instance, choose your instance, and then choose Attach volume.

  The volume is attached successfully.

<img width="1675" height="604" alt="image" src="https://github.com/user-attachments/assets/e68475f3-44c5-4ed5-8acc-a96ecda32a39" />

## Task 5: Analyzing AWS KMS activity by using CloudTrail
In this task, you access the CloudTrail event history to find events that are related to your encryption operations. The CloudTrail audit log functionality provides an important security feature, and it's a good idea to monitor how AWS KMS keys are used in your account.

First, you access the CloudTrail event history.

Navigate to the CloudTrail console. 

In the navigation pane, choose Event history.

  In the Event history list, notice the column named Event source.

  Each time an API call to an AWS service occurs within the Region that you have selected, if that service reports such events to CloudTrail, then the event and the AWS service that reported the event are listed.

  Next, you filter the event history to display only events that the AWS KMS service reported. 

Under Lookup attributes, from the dropdown list that currently displays Read-only, choose Event source.

In the search box that currently reads Enter an event source, enter kms, and then choose kms.amazonaws.com.

  Next, you analyze a few key CloudTrail events.

<img width="1425" height="781" alt="image" src="https://github.com/user-attachments/assets/d7f307b4-0f43-444c-bedb-308083f366f1" />

Choose the link for the CreateGrant event name.

  In the Event record section, review the details of the event. The details are similar to the following image. In this image, the essential details of the event record are highlighted.

  CreateGrant event record. Highlighted information includes principalId, eventName, keyId, and EBS object name.

  Analysis: This event allows AWS principals to use AWS KMS keys in cryptographic operations. The EC2 instance sends a CreateGrant request so that it can decrypt the data key.

Return to the Event history list, and choose the link for the Decrypt event name. If there are multiple Decrypt events, pick any one of them.

   Note: If you do not see the Decrypt event, wait for a few minutes.

  In the Event record section, review the details of the event. The details are similar to the following image. In this image, the essential details of the event record are highlighted.

  Decrypt event record. Highlighted information includes principalId, eventName, keyId, and EBS object name.

  Analysis: Decrypt follows a successful CreateGrant event. The record shows the details of the AWS KMS key named MyKMSKey that the EC2 instance uses to decrypt the encrypted key, which is eventually used to decrypt data on the EBS volume.

Return to the Event history list, choose the links for the following events, and observe the event details.

For the GenerateDataKeyWithoutPlaintext, an event occurred when a request to generate a key to encrypt the EBS volume data was sent to AWS KMS.

You might also look at events such as RetireGrant.

CloudTrail logs all AWS KMS API activity. By evaluating these log entries, you might be able to determine the past usage of a particular AWS KMS key. If you want to be able to analyze events that occurred more than 90 days ago, you can create a CloudTrail trail.

## Task 6: Reviewing key rotation
You might be required to rotate your KMS keys due to business or contract rules or government regulations. AWS KMS supports automatic key rotation only for symmetric encryption KMS keys with key material that AWS KMS creates. Automatic rotation is optional for customer managed KMS keys.

In this task, you review the feature to enable automatic rotation for the key you created in this lab. However, you cannot see it in effect as the rotation would be effective after a year, once enabled.

In the AWS Management Console, choose Key Management Service to open the AWS KMS console.

From the navigation pane on the left, choose Customer managed keys  .

From the list, choose MyKMSKey, the key you created for this lab.

Choose Key rotation.

On the Key rotation menu, select Automatically rotate this KMS key every year.

Choose Save.

A message is displayed at the top indicating Key rotation updated.

Your key is now scheduled for rotation.

<img width="1679" height="391" alt="image" src="https://github.com/user-attachments/assets/8ea017c3-5f36-41ae-8756-8dc5fbae81fa" />
