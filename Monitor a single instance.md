# Lazy Way to Start Using CloudWatch
## This tutorial aims to provide you a practical guidence in using CloudWatch to monitor the health of your ec2 instance.

Note: This tutorial is most suitable for monitoring only one ec2 instance without the need to reuse the configuration file. 

**Step 1. Attach an IAM Role to the instance that you want to monitor**
Go to EC2 service interface in AWS. On the left lane, click *intances*. Here you need to find your target instance in the pool. Mind the instance you want to monitor needs to be up and running.

Target your mouse on the instance and right click it. Now you see a menu which starts with the item *connect*. In this menu, find the item *Instance Settings*. Under this item, find and click Attach/Replace IAM Role. After doing this, you will be redirect to a new web interface, where you can choose an exisitng IAM role or to create a new IAM role. If you have not yet had an IAM role which 





**Changes may occur due to the updates of the platform.** 

An IAM role for the instance profile is required when you install the CloudWatch agent on an Amazon EC2 instance. This role enables the CloudWatch agent to perform actions on the instance. Use one of the roles you created earlier. For more information about creating these roles, see Create IAM Roles and Users for Use With CloudWatch Agent. You can scroll through the list to find them, or use the search box.

If you are going to use this instance to create the CloudWatch agent configuration file and copy it to Systems Manager Parameter Store, use the role you created that has permissions to write to Parameter Store. This role may be called CloudWatchAgentAdminRole.

For all other instances, select the role that includes just the permissions needed to install and run the agent. This role may be called CloudWatchAgentServerRole.

Attach this role to the instance on which you install the CloudWatch agent. For more information, see Attaching an IAM Role to an Instance in the Amazon EC2 User Guide for Windows Instances.

