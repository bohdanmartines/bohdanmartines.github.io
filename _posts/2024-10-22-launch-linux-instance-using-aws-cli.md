---
layout: post
title: How to Easily Launch a Linux EC2 Instance Using AWS CLI
date: 2024-10-22 10:00 +0000
categories: [ AWS ]
tags: [ AWS, AWS CLI, EC2 ]
image: /assets/2024-10-22/thumbnail.png
---

Hey there!

In [the previous article](/posts/launch-linux-instance-using-aws-cli), we explored how to manually launch a Linux EC2
instance using the AWS Management Console. Today, we'll take it a step further by learning how to do the same using the
**AWS CLI** (Command Line Interface), which provides a more efficient and scriptable way to interact with AWS services.

## Prerequisites

To follow this guide, you will need to have an AWS account, and AWS CLI installed on your local machine.
You can find AWS CLI installation
instructions [here](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).

Additionally, you'll need an **AWS IAM** user with the appropriate permissions to interact with AWS services via the
CLI.
For guidance on creating an IAM user and configuring the AWS CLI, refer to [this post](/posts/aws-cli-user-setup/).

## Launch an EC2 instance

To launch an instance we will need to prepare 3 things: a key pair, a security group, and the name of the OS image to
use.

### Create a key pair

A **key pair** is required to connect to the EC2 instance via SSH. Run the following command to create one:

```shell
aws ec2 create-key-pair --key-name aws-cli-demo-key --query "KeyMaterial" --output text > aws-cli-demo-key.pem
```

This command creates a key pair named `aws-cli-demo-key` and saves the private key to a file called
`aws-cli-demo-key.pem`.

### Configure a security group

A **security group** controls which traffic can reach your instance. Start by creating a security group with this
command:

```shell
aws ec2 create-security-group --group-name "AWS CLI demo security group" --description "Security group for AWS CLI demo"
```

The response will contain the security group ID, in my case it is `sg-05b4dc0764daba57f`. We will need it on the next
step to add inbound rules. Let's do it.

```shell
aws ec2 authorize-security-group-ingress --group-id sg-05b4dc0764daba57f --protocol tcp --port 22 --cidr 0.0.0.0/0
```

This command allows SSH (TCP on port 22) from any IP address. **In production**, it's good practice to restrict access
by specifying a trusted IP range instead of 0.0.0.0/0.

### Find the AMI (Amazon Machine Image)

The **AMI** defines the OS and software pre-installed on your instance. For this guide, we'll use **Amazon Linux 2023**.
To find
the latest AMI for Amazon Linux 2023, run:

```shell
aws ec2 describe-images --owners amazon --filters "Name=name,Values=al2023-ami-2023*-x86_64*" "Name=state,Values=available" --query "Images | sort_by(@, &CreationDate) | [-1].[ImageId,Name,CreationDate]" --output table
```

This command retrieves the most recent Amazon Linux 2023 AMI. For example:

```shell
----------------------------------------------------
|                  DescribeImages                  |
+--------------------------------------------------+
|  ami-08ec94f928cf25a9d                           |
|  al2023-ami-2023.6.20241010.0-kernel-6.1-x86_64  |
|  2024-10-11T16:52:36.000Z                        |
+--------------------------------------------------+
```

The **AMI ID** in this case is `ami-08ec94f928cf25a9d`, which you'll need in the next step.

### Launch an instance

Now that we have the **key pair**, **security group**, and **AMI ID**, we can launch the instance using the following
command:

```shell
aws ec2 run-instances --image-id ami-08ec94f928cf25a9d --count 1 --instance-type t2.micro --key-name aws-cli-demo-key --security-group-ids sg-05b4dc0764daba57f --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=AWSCLIDemoInstance}]"
```

This command launches a `t2.micro` instance (1 vCPU, 1 GB RAM) running **Amazon Linux 2023**. The instance will be
accessible from the command line by SSH using our security key.

The command returns the ID of our machine, save it for future use.

## Verify the instance

Let's get the public IP of the instance.

```shell
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query "Reservations[*].Instances[*].[InstanceId,PublicIpAddress]" --output table
```

This will return you active instance IDs and their public IP addresses

```shell
-----------------------------------------
|           DescribeInstances           |
+----------------------+----------------+
|  i-09c2ac8f98d149947 |  3.120.183.65  |
+----------------------+----------------+
```

Now use the access key we created earlier to SSH to this box.

```shell
ssh -i aws-cli-demo-key.pem ec2-user@3.120.183.65
```

## Conclusion

Today, we covered how to create an EC2 instance and its dependencies using the AWS CLI. Using the CLI can save time by
eliminating the need to navigate the user interface manually. Additionally, if you perform certain tasks frequently, you
can further streamline your workflow by automating commands in a script.

I hope this guide was helpful to you. See you in the next article!
