---
layout: post
title: "AWS CLI User Setup: A Beginner's Guide"
date: 2024-10-21 10:00 +0000
categories: [ AWS ]
tags: [ AWS, AWS CLI ]
---

AWS CLI is a powerful command-line tool that allows you to manage AWS services from your terminal. After installing it
on your machine, you will also need an AWS user with the proper permissions to interact with AWS resources

In this article, we'll walk through how to set up a user for AWS CLI, which will be used in subsequent articles.

## Create a user

Log in to your AWS Management Console. In the search bar at the top, type **IAM** and select it from the search results.
In the IAM dashboard, select **Users** from the left-hand menu. On **Users** page click **Create User**.

![Create user](/assets/2024-10-21/create_user.png)

Let's use `aws-cli-user` as the username, and click **Next**

![Set username](/assets/2024-10-21/user_name.png)

On the next screen, choose Attach policies directly. Search for the policy named `AmazonEC2FullAccess`, select it and
click **Next**.

![User permissions](/assets/2024-10-21/user_permissions.png)

**Note!** Here we give the user full access on EC2 for the sake of simplicity. In a real-world scenario, it's
recommended to assign more granular permissions based on the user's specific tasks.

On the review page, confirm the details. If everything looks correct, click **Create user**. The new user will now
appear on **Users** page.

![New user](/assets/2024-10-21/new_user.png)

## Create an access key

An access key is a set of credentials that allows the AWS CLI to authenticate on behalf of your user. Let's create it!
Open the user screen by clicking on the username, and then click **Create access key**

![Create access key](/assets/2024-10-21/create_access_key.png)

On **Access key best practices & alternatives** page select **Command Line Interface (CLI)** as the intended use, and
click **Next**.

![Access key purpose](/assets/2024-10-21/access_key_purpose.png)

Then click **Create access key**.

![Access key is ready](/assets/2024-10-21/access_key_ready.png)

Note down the access key, secret access key, as well as the region where you want to create an instance.
**Important**: Treat these credentials securely. Do not share or expose them publicly.

## Configure AWS CLI

Now that you have the access keys, you can configure the AWS CLI to use this new user. Run the CLI configuration
command.

```shell
aws configure
```

When asked, enter the access key, secret key and region. The output format can be json.

## Verify the setup

To confirm that the AWS CLI is working and that your user has the necessary permissions, run the following command:

```shell
aws ec2 describe-instances
```

You should see instance data in json format. If this is the case, and no errors in the response, then AWS CLI is ready
for work! Well done!
