## A Lazy Way to Start Using CloudWatch

This tutorial aims to provide you a practical guidence in using CloudWatch to monitor the health of your ec2 instance.
Note: This tutorial is most suitable for monitoring only one ec2 instance without the need to reuse the configuration file. 

### Step 1. Create IAM Roles
**Reason**: In order to enable CloudWatch agent to be installed on a server and send metrics to CloudWatch, you need to create an IAM role with the right permissions. 

**Procedures**:
1. Go to IAM service in aws and create a role.
2. Under "Select type of trusted entity", choose "AWS service".
3. Under "Choose the service that will use this role", choose "EC2" and click "Next:Persmissions".
4. Now you are being redirected to a new web page. Next to "Filter policies", type in "CloudWatchAgentServerPolicy", and attach this        policy for your new role. Then proceed to the next page.
5. Now pick up a name for your new role and leave everything else in the default setting.

### Step 2. Attach the IAM Role to the instance that you want to monitor
**Procedures**:
1. Go to EC2 service interface in AWS. On the left lane, click "intances". Here you need to find your target instance in the pool. Mind    the instance you want to monitor needs to be up and running.
2. Target your mouse on the instance and right click on it. Now you see a menu which starts with the item "connect". In this menu, find    the "Instance Settings". Under "Instance Settings", click "Attach/Replace IAM Role". After doing this, you will be redirect to a new    web interface, where you can choose an exisitng IAM role or to create a new IAM role. Now choose the role you created in Step 1.

### Step 3. Download the CloudWatch Agent Package on your Amazon EC2 Instance.
There are two ways to accomplish this step. One with AWS Systems Manager, and another with S3 Download Link. Here I will introduce the second one. [Please check out the documentation on aws for more information.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html#download-CloudWatch-Agent-on-EC2-Instance-first) 

**Procedures**:
1. Download the CloudWatch Agent Package on an Amazon EC2 Instance Using an S3 Download Link



**Changes may occur due to the updates of the platform.** 

An IAM role for the instance profile is required when you install the CloudWatch agent on an Amazon EC2 instance. This role enables the CloudWatch agent to perform actions on the instance. Use one of the roles you created earlier. For more information about creating these roles, see Create IAM Roles and Users for Use With CloudWatch Agent. You can scroll through the list to find them, or use the search box.

If you are going to use this instance to create the CloudWatch agent configuration file and copy it to Systems Manager Parameter Store, use the role you created that has permissions to write to Parameter Store. This role may be called CloudWatchAgentAdminRole.

For all other instances, select the role that includes just the permissions needed to install and run the agent. This role may be called CloudWatchAgentServerRole.

Attach this role to the instance on which you install the CloudWatch agent. For more information, see Attaching an IAM Role to an Instance in the Amazon EC2 User Guide for Windows Instances.

The other role is needed to store your CloudWatch agent configuration in System Manager Parameter Store, which enables multiple servers to use "
