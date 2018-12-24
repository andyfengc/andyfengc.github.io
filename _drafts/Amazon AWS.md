---
layout: post
title: Amazon AWS
author: Andy Feng
---

# Amazon EC2 #
Amazon Elastic Compute Cloud (Amazon EC2) provides scalable computing capacity in the Amazon Web Services (AWS) cloud. We can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and manage storage. 

- Amazon Machine Image (AMI)
 
	It is a template that contains a software configuration (for example, an operating system, an application server, and applications). From an AMI, you launch an instance, which is a copy of the AMI running as a virtual server in the cloud. You can launch multiple instances of an AMI, as shown in the following figure.

	![](/images/posts/20181215-aws-1.png)

- Instances

	We can launch different types of instances from a single AMI. An instance type essentially determines the hardware of the host computer used for our instance. Each instance type offers different compute and memory capabilities. After we launch an instance, it looks like a traditional host, and we can interact with it as we would any computer. We have complete control of our instances; we can use sudo to run commands that require root privileges.

	Your AWS account has a limit on the number of instances that you can have running. For more information about this limit, and how to request an increase, see [How many instances can I run in Amazon EC2](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 General FAQ.

- Storage for Instance

	Our instance may include local storage volumes, known as instance store volumes, which we can configure at launch time with block device mapping. After these volumes have been added to and mapped on our instance, they are available for us to mount and use. If our instance fails, or if instance is stopped or terminated, the data on these volumes is lost; therefore, these volumes are best used for temporary data. To keep important data safe, we should use a replication strategy across multiple instances, or store our persistent data in Amazon S3 or Amazon EBS volumes. For more information, see [Storage](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Storage.html).

- Access Management (IAM) 

	Use AWS Identity and Access Management (IAM) to control access to your AWS resources, including your instances. You can create IAM users and groups under your AWS account, assign security credentials to each, and control the access that each has to resources and services in AWS. For more information, see [Controlling Access to Amazon EC2 Resources](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/UsingIAM.html).

	[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-an-iam-user](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-an-iam-user)

	[IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

- Key Pair

	AWS uses public-key cryptography to secure the login information for your instance. A Linux instance has no password; you use a key pair to log in to your instance securely. You specify the name of the key pair when you launch your instance, then provide the private key when you log in using SSH.

	If you haven't created a key pair already, you can create one using the Amazon EC2 console. Note that if you plan to launch instances in multiple regions, you'll need to create a key pair in each region. For more information about regions, see [Regions and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/resources.html).

	[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-a-key-pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-a-key-pair)

- Security Group

	Security groups act as a firewall for associated instances, controlling both inbound and outbound traffic at the instance level. You must add rules to a security group that enable you to connect to your instance from your IP address using SSH. You can also add rules that allow inbound and outbound HTTP and HTTPS access from anywhere.

	[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html)

	[Create a Security Group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-a-base-security-group)

	[Security Group Rules best practices](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html#security-group-rules)

- Regions and Zones

	Amazon EC2 is hosted in multiple locations world-wide. These locations are composed of regions and Availability Zones. Each region is a separate geographic area. Each region has multiple, isolated locations known as Availability Zones. Amazon EC2 provides you the ability to place resources, such as instances, and data in multiple locations. 

	Please note that Key pair, SEcurity Group are restrict within region. 

- Amazon VPC 

	It enables us to launch AWS resources into a virtual network that we've defined, known as a virtual private cloud (VPC). The newer EC2 instance types require that we launch our instances in a VPC. 

	[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-a-vpc](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-a-vpc)

# Instances

An instance is a virtual server in the AWS cloud. With Amazon EC2, you can set up and configure the operating system and applications that run on your instance. 

**When you launch your instance, you secure it by specifying a key pair and security group. When you connect to your instance, you must specify the private key of the key pair that you specified when launching your instance.**

![](/images/posts/20181215-aws-2.png)

## Create/Launch an instance

[Launch an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html#ec2-launch-instance)

## Connect to Your Linux Instance

[https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html)

When an instance is stopped, the instance performs a normal shutdown, and then transitions to a stopped state. All of its Amazon EBS volumes remain attached, and you can start the instance again at a later time. You are not charged for additional instance usage while the instance is in a stopped state. When an instance is in a stopped state, you can attach or detach Amazon EBS volumes. You can also create an AMI from the instance, and you can change the kernel, RAM disk, and instance type.

When an instance is terminated, the instance performs a normal shutdown. 

[Instance Lifecycle](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-lifecycle.html)

# Amazon VPC #

Amazon Virtual Private Cloud (Amazon VPC) enables us to launch AWS resources into a virtual network that we've defined. This virtual network closely resembles a traditional network that we'd operate in our own data centerS. Amazon VPC is the networking layer for Amazon EC2

A virtual private cloud (VPC) is a virtual network dedicated to your AWS account. It is logically isolated from other virtual networks in the AWS Cloud. You can launch your AWS resources, such as Amazon EC2 instances, into your VPC. You can specify an IP address range for the VPC, add subnets, associate security groups, and configure route tables.

A subnet is a range of IP addresses in your VPC. You can launch AWS resources into a specified subnet. Use a public subnet for resources that must be connected to the internet, and a private subnet for resources that won't be connected to the internet. For more information about public and private subnets, see VPC and Subnet Basics.
	

# References #
[What Is Amazon EC2?](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html)