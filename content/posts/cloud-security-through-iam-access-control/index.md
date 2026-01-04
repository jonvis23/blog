
---
title: "Cloud Security through IAM Access control"
author: "Elvis Ouya"
date: 2025-10-23T12:36:41.827Z
---



As organisations move to the cloud, securing resources becomes a non-negotiable. In this writeup, I walk through how I implemented AWS IAM to control access between dev & prod environments, ensuring developers only interact with development resources, while administrators retain exclusive control over production.

This project simulates real-world IAM policy design, principle of least-priviledge, and account hardening using AWS best practices.

![“You can’t bolt security onto the cloud. You have to build it in.”](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*T8SMhgh7N-VD4fPBzmxLvQ.jpeg)

Project Scenario
----------------

Imagine a startup with two Amazon EC2 instances:

*   A **Development server** for building and testing features
*   A **Production server** running live customer applications

As the cloud security administrator, my goal is to **implement strong IAM controls** that:
✅ Prevent developers from accidentally modifying production
✅ Allow administrators to manage both environments securely
✅ Make the AWS account human-readable by setting an alias

![IAM Access Control Diagram](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*WUXEq_vmm4Uks7SRFR6UHQ.png)

✅ Stage 1: Setting an AWS Account Alias
---------------------------------------

Before anything else, I improved account observability by replacing a numeric account ID with a human-friendly alias.

**Why this matters:** In real teams, multiple AWS accounts exist (staging, production, sandbox). An alias prevents accidental deployments to the wrong account.

**Steps:**

1.  Navigate to **IAM → Dashboard**
2.  Under Account Alias, click **Create**
3.  Enter alias: `name-cloud-account`

![Aliased my account on the IAM Dashboard](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*MNdOhhDwPi9niTM4qV52pA.png)

### Step 2: Launch and Tag Two EC2 Instances

We will launch two separate Amazon Linux instances. Tagging is crucial as our IAM policies will use these tags to grant permissions.

1.  Navigate to the **EC2 Dashboard**.
2.  Click **Launch instance**.
3.  **Launch the Development Instance:**

*   **Name:** `dev-instance`
*   **Application and OS Images:** Select `Amazon Linux 2023 AMI` (or another as preferred).
*   **Instance type:** `t2.micro` (eligible for the Free Tier).
*   **Key pair (login):** Proceed without a key pair, as we will manage these instances via IAM policies and the console, not SSH.
*   **Network settings:** Keep the default VPC and subnet settings.
*   Under **Advanced details**, expand the section and scroll to the bottom. Under **Tags**, click **Add tag**. [**Key:** `Environment`**Value:** `Development` ]
*   Click **Launch instance**.

**4. Launch the Production Instance:**

*   Repeat the process above.
*   **Name:** `prod-instance`
*   Use the same AMI and instance type (`t2.micro`).
*   Proceed without a key pair.
*   Under **Advanced details** -> **Tags**, click **Add tag. [Key:** `Environment` **Value:**`Production` ]
*   Click **Launch instance**.

![You should now have two running EC2 instances: dev-instance and prod-instance, each with a distinct Environment tag.](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*vywcqT8TdLntlFkgE-b57g.png)

**Part 1 Highlights 💡**

This initial setup highlights two critical concepts. First, creating an **account alias** is a security best practice that simplifies the sign-in process for users by removing the need to know the numerical Account ID. Second, this section shows that **tagging resources** is not just for organization or billing; it’s a foundational element of a robust security strategy. These tags act as metadata that our security policies will use to dynamically grant or deny permissions.

**Part 2: Create IAM User Groups**

User groups simplify permission management. Instead of assigning policies to individual users, you assign them to a group.

1.  Navigate back to the **IAM Dashboard**.
2.  In the left navigation pane, click on **User groups**.
3.  Click **Create group**.
4.  **Create the Developers Group:**

*   **User group name:** `Developers`
*   Leave the “Users” and “Permissions policies” sections empty for now.
*   Click **Create group**.

**5. Create the Administrators Group:**

*   Click **Create group** again.
*   **User group name:** `Administrators`
*   Click **Create group**.

![We have both Adminstrators & Developers group](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*JgkeAXk3lVqAPGBazRDtGQ.png)

Part 2 Highlights **💡**

> The key insight here is the importance of scalability and manageable permissions. Attaching policies directly to individual users is inefficient and becomes impossible to manage in a large organization. By creating **user groups** based on job functions (`Developers`, `Administrators`), we are implementing a form of **Role-Based Access Control (RBAC)**. This ensures that permissions are consistent for all users in the same role and simplifies administration significantly.

**Part 3: Create and Attach IAM Policies**
------------------------------------------

This is the core of the project. We’ll create custom policies that grant specific, tag-based permissions.

**Step 1: Create the Developer Policy**

This policy will allow users to view all instances but only start, stop, or reboot the one tagged for development.

1.  In the IAM Dashboard, click **Policies** in the left navigation pane.
2.  Click **Create policy**.
3.  Select the **JSON** tab.
4.  Paste the following policy document. This policy has two statements:

*   The first allows listing all instances (`_ec2:DescribeInstances_`).
*   The second allows start/stop/reboot actions _only_ on resources that have the tag `**_Environment_**`with the value `**_Development_**`

```
{
    "Version": "2012-10-17",
    "Statement": [        {
            "Effect": "Allow",
            "Action": "ec2:DescribeInstances",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [                "ec2:StartInstances",
                "ec2:StopInstances",
                "ec2:RebootInstances"
            ],
            "Resource": "arn:aws:ec2:*:*:instance/*",
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/Environment": "Development"
                }
            }
        }
    ]
}
```

5. Click Next: Tags, then Next: Review where you’ll name & describe the policy.

6. Click **Create policy**.

![Creating Development policies](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*5WhITWCf30Cc_a1TfjZ09A.png)

### Step 2: Create the Administrator Policy

This policy will grant full control over the production instance.

1.  Go back to **Policies** and click **Create policy**.
2.  Select the **JSON** tab and paste the following:

```
{
    "Version": "2012-10-17",
    "Statement": [        {
            "Effect": "Allow",
            "Action": "ec2:*",
            "Resource": "arn:aws:ec2:*:*:instance/*",
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/Environment": "Production"
                }
            }
        }
    ]
}

```

3. Click Next: Tags, then Next: Review where you’ll name & describe the policy.

4. Click **Create policy**.

![Creating administator polices](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*YwR65eBhoUZuqi1BT5Rssg.png)

### Step 3: Attach Policies to Groups

1.  Navigate to **User groups**, then Click on the **Developers** group.
2.  Go to the **Permissions** tab and click **Add permissions** -> **Attach policies**.
3.  Search for and select `DevInstanceAccessPolicy`.
4.  Click **Add permissions**.
5.  Go back to **User groups** and click on the **Administrators** group.
6.  In the **Permissions** tab, click **Add permissions** -> **Attach policies**.
7.  Search for and select `ProdInstanceAccessPolicy`. Also search for and select the AWS managed policy `AdministratorAccess` to give them full admin rights as is common.
8.  Click **Add permissions**.

> **_💡_**Part 3 Highlights
> 
> _This section is a practical application of the_ **_Principle of Least Privilege_**_. The developer policy is a perfect example: it allows a broad, read-only action (_`_DescribeInstances_`_) on all resources (_`_*_`_) but restricts sensitive actions (_`_Start_`_,_ `_Stop_`_) to only the resources that meet a specific_ `_Condition_`_—in this case, having the correct tag. This use of_ **_condition clauses_** _is what makes IAM so powerful. It enables a dynamic,_ **_Attribute-Based Access Control (ABAC)_** _model where permissions are not tied to specific resources but to their attributes (tags)._

**Part 4: Create and Assign IAM Users**

Now, we create the user accounts and add them to their respective groups to inherit permissions.

1.  In the IAM Dashboard, click **Users** in the left navigation pane.
2.  Click **Create user**.
3.  **Create the Developer User:**

*   **User name:** `DevUser`
*   Select **Provide user access to the AWS Management Console**.
*   Choose **I want to create an IAM user**.
*   Under **Console password:** Select **Custom password** and create a secure password.
*   Uncheck **User must create a new password at next sign-in**.
*   Click **Next**.
*   On the **Set permissions** page, select **Add user to group**.
*   Check the box for the **Developers** group.
*   Click **Next**, then **Create user**.

**4. Create the Administrator User:**

*   Repeat the process: Click **Create user**.
*   **User name:** `AdminUser`
*   Select console access, create a custom password, and uncheck the password reset option.
*   Click **Next**.
*   Select **Add user to group** and check the box for the **Administrators** group.
*   Click **Next**, then **Create user**.

![Snapshot for creating AdminUser profile](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*FvmIBWnhwLmV7Il0Et0oXw.png)

Part 4Highlights **💡**

> Here, we see the clear **separation of identity (the user) and authorization (the policies)**. The user object (`DevUser`) has no permissions attached to it directly. Instead, it inherits all its permissions from the `Developers` group. This decoupling is crucial for efficient management. If a developer needs administrative access, you don't edit their user policies; you simply move them to the `Administrators` group. This makes managing user permissions throughout their lifecycle (onboarding, role changes, offboarding) simple and less prone to error.

Part 5: Test Your IAM Configuration
-----------------------------------

The final step is to verify that the permissions work as expected.

1.  **Sign out** of your current root/admin session.
2.  Use your account alias URL to sign in: `your-alias.signin.aws.amazon.com/console`.
3.  **Log in as** `**DevUser**` with the password you created.
4.  Navigate to the **EC2 Dashboard**.
5.  You should see both `dev-instance` and `prod-instance`.
6.  **Test successful access:**

*   Select `dev-instance`.
*   Click **Instance state**. The options to **Stop instance** and **Reboot instance** should be enabled.

**7. Test denied access:**

*   Select `prod-instance`.
*   Click **Instance state**. The options should be greyed out, and if you try to perform an action, you will get an access denied error.

![Denied error for Prod instance](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*zPHkcPZc4xzV0_cNgg4LMw.png)

Test Production Policies

1.  **Sign out** and **log in as** `**AdminUser**`.
2.  Navigate to the **EC2 Dashboard**.
3.  Select `prod-instance` and verify you have full control.
4.  Select `dev-instance`. Because the `Administrators` group also has the `AdministratorAccess` policy, this user can manage the dev instance as well.

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*c92uN-OXnYDpjWamYzZSUQ.png)

Conclusion
----------

Congratulations! You have successfully configured a secure AWS environment using IAM. You created user groups with specific, policy-based permissions that grant access to resources based on tags. This project demonstrates the **principle of least privilege**, a fundamental security concept where users are only given the permissions essential to perform their duties. This greatly reduces the risk of accidental changes or malicious activity in your production environment.

What I Took Away
----------------

Cloud security is not just about denying access 😅 ,it’s about enabling the _right_ access, to the _right_ people, at the _right_ time. This exercise demonstrated the power of Identity Access Management as a centralized security control mechanism in AWS.