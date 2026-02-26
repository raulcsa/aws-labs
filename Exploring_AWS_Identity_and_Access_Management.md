# Task 1: Explore the users and groups, and inspect policies

In this task, you explore the users and groups that were created for you in IAM.

First, note the Region that you are in; for example, N. Virginia. The Region is displayed in the upper-right corner of the console page. You might need this information later in the lab.

1. Choose the **Services** menu, locate the **Security, Identity, & Compliance** services, and choose **IAM**.
2. In the navigation pane on the left, choose **Users**.

<img width="1679" height="831" alt="image" src="https://github.com/user-attachments/assets/6b7fd4b1-3e30-419d-bcb0-566031ea0b56" />

The following IAM users were created for you:
* `user-1`
* `user-2`
* `user-3`

3. Choose the name of `user-1`.

This brings you to a summary page for `user-1`. The **Permissions** tab is displayed. Notice that `user-1` does not have any permissions.

<img width="1675" height="817" alt="image" src="https://github.com/user-attachments/assets/b55e94aa-cc1a-4c05-9924-76f08a1567bc" />

4. Choose the **Groups** tab.

Notice that `user-1` also is not a member of any groups.

<img width="1397" height="605" alt="image" src="https://github.com/user-attachments/assets/e6b8aef0-3807-4431-b2d8-e0c579144e53" />

5. Choose the **Security credentials** tab.

Notice that `user-1` is assigned a Console password. This allows the user to access the AWS Management Console.

<img width="1390" height="753" alt="image" src="https://github.com/user-attachments/assets/926ce3fe-a9a4-4ad2-a33c-a07639db523f" />

6. In the navigation pane on the left, choose **User groups**.

The following groups were created for you:
* `EC2-Admin`
* `EC2-Support`
* `S3-Support`

<img width="1688" height="781" alt="image" src="https://github.com/user-attachments/assets/3077e53e-d201-4695-bedc-5c2d0f79f58a" />

7. Choose the name of the **EC2-Support** group.

This brings you to the summary page for the `EC2-Support` group.

8. Choose the **Permissions** tab.

This group has a managed policy called `AmazonEC2ReadOnlyAccess` associated with it. Managed policies are prebuilt policies (built either by AWS or by your administrators) that can be attached to IAM users and groups. When the policy is updated, the changes to the policy are immediately applied against all users and groups that are attached to the policy.

<img width="1396" height="530" alt="image" src="https://github.com/user-attachments/assets/6b5ac9f4-7931-4bb9-9ef7-d81001dcdb4f" />

9. Below **Policy Name**, choose the link for the `AmazonEC2ReadOnlyAccess` policy.
10. Choose the **JSON** tab.

A policy defines which actions are allowed or denied for specific AWS resources. This policy is granting permission to List and Describe (view) information about Amazon Elastic Compute Cloud (Amazon EC2), Elastic Load Balancing, Amazon CloudWatch, and Amazon EC2 Auto Scaling. This ability to view resources, but not modify them, is ideal for assigning to a support role.

Statements in an IAM policy have the following basic structure:
* **Effect:** says whether to Allow or Deny the permissions.
* **Action:** specifies the API calls that can be made against an AWS service (for example, `cloudwatch:ListMetrics`).
* **Resource:** defines the scope of entities covered by the policy rule (for example, a specific Amazon Simple Storage Service [Amazon S3] bucket or Amazon EC2 instance; an asterisk [ `*` ] means any resource).

11. In the navigation pane on the left, choose **User groups**.
12. Choose the name of the **S3-Support** group.
13. Choose the **Permissions** tab.

The `S3-Support` group has the `AmazonS3ReadOnlyAccess` policy attached.

14. Below **Policy Name**, choose the link for the `AmazonS3ReadOnlyAccess` policy.
15. Choose the **JSON** tab.

This policy has permissions to Get and List for all resources in Amazon S3.

<img width="1359" height="641" alt="image" src="https://github.com/user-attachments/assets/6725c188-678f-430a-a9c6-140397846ab3" />

16. In the navigation pane on the left, choose **User groups**.
17. Choose the name of the **EC2-Admin** group.
18. Choose the **Permissions** tab.

This group is different from the other two. Instead of a managed policy, the group has an inline policy, which is a policy assigned to just one user or group. Inline policies are typically used to apply permissions for specific situations.

19. Below **Policy Name**, choose the name of the `EC2-Admin-Policy` policy.
20. Choose the **JSON** tab.

This policy grants permission to Describe information about Amazon EC2 instances and the ability to Start and Stop instances.

21. At the bottom of the screen, choose **Cancel** to close the policy, and then choose **Continue**.

---

## Business scenario

For the remainder of this lab, you work with these users and groups to enable permissions that support the following business scenario.

Your company is growing its use of AWS services and is using many Amazon EC2 instances and Amazon S3 buckets. You want to give access to new staff based on their job function, as indicated in the following table:

| User | In Group | Permissions |
| :--- | :--- | :--- |
| `user-1` | `S3-Support` | Read-only access to Amazon S3 |
| `user-2` | `EC2-Support` | Read-only access to Amazon EC2 |
| `user-3` | `EC2-Admin` | View, Start, and Stop Amazon EC2 instances |

---

# Task 2: Add users to groups

You recently hired `user-1` into a role where they will provide support for Amazon S3. In this task, you add them to the `S3-Support` group so that they inherit the necessary permissions through the attached `AmazonS3ReadOnlyAccess` policy.

> **Note:** Ignore any "not authorized" errors that appear during this task. They are caused by your lab account having limited permissions and don't impact your ability to complete the lab.

### Task 2.1: Add user-1 to the S3-Support group

1. In the left navigation pane, choose **User groups**.
2. Choose the name of the **S3-Support** group.
3. On the **Users** tab, choose **Add users**.
4. Select `user-1`, and choose **Add users**.
5. On the **Users** tab, notice that `user-1` was added to the group.

<img width="1395" height="629" alt="image" src="https://github.com/user-attachments/assets/b99bd4b8-28d5-4115-ab02-28fe63d1db7c" />

### Task 2.2: Add user-2 to the EC2-Support group

You hired `user-2` into a role where they will provide support for Amazon EC2. You will add them to the `EC2-Support` group so that they inherit the necessary permissions through the attached `AmazonEC2ReadOnlyAccess` policy.

1. Use what you learned from the previous steps to add `user-2` to the **EC2-Support** group.
2. Verify that `user-2` is now part of the **EC2-Support** group.

<img width="1397" height="587" alt="image" src="https://github.com/user-attachments/assets/ca507eef-58d7-4eef-a58a-ec2206f8734b" />

### Task 2.3: Add user-3 to the EC2-Admin group

You hired `user-3` as your Amazon EC2 administrator to manage your EC2 instances. You will add them to the `EC2-Admin` group so that they inherit the necessary permissions through the attached `EC2-Admin-Policy`.

1. Use what you learned from the previous steps to add `user-3` to the **EC2-Admin** group.
2. Verify that `user-3` is now part of the **EC2-Admin** group.

<img width="1399" height="571" alt="image" src="https://github.com/user-attachments/assets/74fc81af-1020-4184-9769-23f18d993529" />

3. In the navigation pane on the left, choose **User groups**.

Each group should have a `1` in the **Users** column. This indicates the number of users in each group.

If you do not have a `1` for the **Users** column for a group, revisit the previous steps to ensure that each user is assigned to a group, as shown in the table in the Business scenario section.

<img width="1400" height="401" alt="image" src="https://github.com/user-attachments/assets/060c8c1c-6445-452f-a89c-573ca95fcbd9" />

---

# Task 3: Sign in and test user permissions

In this task, you test the permissions inherited by IAM users in the console.

### Task 3.1: Get the console sign-in URL

1. In the navigation pane on the left, choose **Dashboard**.
2. Notice the **Sign-in URL for IAM users in this account** section at the top of the page. The sign-in URL looks similar to the following: `https://123456789012.signin.aws.amazon.com/console`

This link can be used to sign in to the AWS account that you are currently using.

3. Copy the sign-in link to a text editor.

<img width="1398" height="741" alt="image" src="https://github.com/user-attachments/assets/e3012c98-ea26-4a59-8f8e-7364a0cd1915" />

### Task 3.2: Test user-1 permissions

1. Open a private or incognito window in your browser.
2. Paste the sign-in link into the private browser, and press **ENTER**.

You will now sign-in as `user-1`, who was hired as your Amazon S3 storage support staff.

3. Sign in with the following credentials:
   * **IAM user name:** `user-1`
   * **Password:** `Lab-Password1`

<img width="1679" height="918" alt="image" src="https://github.com/user-attachments/assets/eaa2cdd6-b830-4d48-9b3d-6220753d3c1d" />

4. Choose the **Services** menu, and choose **S3**. You can also use the search bar to find and choose S3.
5. Choose the name of one of your buckets, and browse the contents.

Because this user is part of the `S3-Support` group in IAM, they have permissions to view a list of Amazon S3 buckets and their contents.

<img width="1679" height="757" alt="image" src="https://github.com/user-attachments/assets/8925447e-93c7-4d19-88f6-640e9abee7e5" />

Now, test whether the user has access to Amazon EC2.

6. Choose the **Services** menu, and choose **EC2**. You can also use the search bar to find and choose EC2.
7. In the left navigation pane, choose **Instances**.

You cannot see any instances. Instead, an error message says you are not authorized to perform this operation. This user has not been assigned any permissions to use Amazon EC2.

You will now sign in as `user-2`, who was hired as your Amazon EC2 support person.

<img width="1679" height="717" alt="image" src="https://github.com/user-attachments/assets/006f8414-8279-461c-92a3-70e078c8f314" />

First, sign out `user-1` from the console:
1. In the upper-right corner of the page, choose `user-1`.
2. Choose **Sign Out**.

### Task 3.3: Test user-2 permissions

1. Paste the sign-in link into the private browser again, and press **ENTER**.
2. Sign in with the following credentials:
   * **IAM user name:** `user-2`
   * **Password:** `Lab-Password2`
3. Choose the **Services** menu, and choose **EC2**. You can also use the search bar to find and choose EC2.
4. In the navigation pane on the left, choose **Instances**.

You are now able to see an EC2 instance. However, you cannot make any changes to Amazon EC2 resources because you have read-only permissions.

> If you cannot see an EC2 instance, then your Region might be incorrect. In the upper-right corner of the page, choose the Region name, and then choose the Region that you were in at the beginning of the lab (for example, N. Virginia).

<img width="1679" height="899" alt="image" src="https://github.com/user-attachments/assets/ab1334f4-b893-4a2f-8279-68c2586e1168" />

5. Select the EC2 instance.
6. Choose the **Instance state** menu, and then choose **Stop instance**.
7. To confirm that you want to stop the instance, choose **Stop**.

An error message appears and says that *You are not authorized to perform this operation*. This demonstrates that the policy only allows you to view information without making changes.

<img width="1677" height="999" alt="image" src="https://github.com/user-attachments/assets/11828e91-1dd3-41a5-bb14-f9583c93cdd8" />

Next, check whether `user-2` can access Amazon S3.

8. Choose the **Services** menu, and choose **S3**. You can also use the search bar to find and choose S3.

An error message says *You don't have permissions to list buckets* because `user-2` does not have permissions to use Amazon S3.

<img width="1679" height="814" alt="image" src="https://github.com/user-attachments/assets/30cca9b4-a8ca-4472-b5bc-141a066c980d" />

You will now sign-in as `user-3`, who was hired as your Amazon EC2 administrator.

First, sign out `user-2` from the console:
1. In the upper-right corner of the page, choose `user-2`.
2. Choose **Sign Out**.

### Task 3.4: Test user-3 permissions

1. Paste the sign-in link into the private browser again, and press **ENTER**.
2. Sign in with the following credentials:
   * **IAM user name:** `user-3`
   * **Password:** `Lab-Password3`
3. Choose the **Services** menu, and choose **EC2**.
4. In the navigation pane on the left, choose **Instances**.

An EC2 instance is listed. As an Amazon EC2 Administrator, this user should have permissions to Stop the EC2 instance.

> If you cannot see an EC2 instance, then your Region might be incorrect. In the upper-right corner of the page, choose the Region name, and then choose the Region that you were in at the beginning of the lab (for example, N. Virginia).

5. Select the EC2 instance.
6. Choose the **Instance state** menu, and then choose **Stop instance**.
7. To confirm that you want to stop the instance, choose **Stop**.

<img width="1679" height="921" alt="image" src="https://github.com/user-attachments/assets/afd16610-66e6-4625-836d-d858f6f22047" />

This time, the action is successful because `user-3` has permissions to stop EC2 instances. The Instance state changes to *Stopping* and starts to shut down.

8. Close your private browser window.
