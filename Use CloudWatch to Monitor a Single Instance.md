## A Lazy Way to Start Using CloudWatch

This tutorial aims to provide you with a practical guidence in using CloudWatch.
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

### Step 3. Download the CloudWatch Agent Package and install the agent on your Amazon EC2 Instance.
There are two ways to accomplish this step. One with AWS Systems Manager, and another with S3 Download Link. Here I will introduce the second one. [Please check out the documentation on aws for more information.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html#download-CloudWatch-Agent-on-EC2-Instance-first) 

**Procedures**:
1. Depending on your architecture and platform, choose the right download link on this [webpage](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html#download-CloudWatch-Agent-on-EC2-Instance-first). 

2. SSH to your ec2 instance and type the following command to download the CloudWatch agent.
$ wget download-link

3. Depending on your operation system, you need different commands to install the package. Please refer to the documentation page of        aws. If your ec2 instance is running on linux, you can use the following command. 
$ sudo rpm -U ./amazon-cloudwatch-agent.rpm


### Step 4. Create the Agent Configuration file on your instance.
You can choose to create the configuration file using the wizard or create and edit it manually by yourself. I will introduce the method of creating it with the wizard as it is easier.

**Procedures**:
1. Type the following command in your ec2 terminal.
$ sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard

2. Answer the questions to customize the configuration file for your server. Note: you need to answer no to some questions to get the agent running. For instance:

*Do you want to turn on StatsD daemon?
1. yes
2. no

*Do you want to monitor metrics from CollectD?
1. yes
2. no

*Do you want to monitor any log files?
1. yes
2. no

After you answered all these questions, you will get the path to the configuration file, which is /opt/aws/amazon-cloudwatch-agent/bin/config.json.


**Changes may occur due to the updates of the platform.** 

