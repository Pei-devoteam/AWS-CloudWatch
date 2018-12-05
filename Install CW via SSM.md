## Install CloudWatch Agent on ec2 instances.

To install CloudWatch Agent on the instances in a new environment via ssm, you need to follow different steps for the first instance and the rest of the instances. Nevertheless, you start doing it by creating two IAM roles.

### Step 1. Create two IAM Roles
The first role named CloudWatchAdminRole needs the following policies: 
* AmazonEC2RoleforSSM, 
* CloudWatchAgentAdminPolicy, 
* AmazonSSMFullAccess. 
(We are sure we need getparameter right for this role to start the CW Agent for sure, but we do not know what else is missing at this stage.)

The second role named CloudWatchSeverRole needs the following policies:
* AmazonEC2RoleforSSM, 
* CloudWatchAgentServerPolicy,
* AmazonSSMFullAccess.


### Step 2. Install CloudWatch Agent on your first instance.
a) Attach the IAM Role (CloudWatchAdminRole) to the first instance that you want to monitor.



b) Download the CloudWatch Agent on Amazon EC2 instance using AWS Systems Manager.

### Step 3. Download 




### Step 4. Go to SSM page in aws. Run the commands to install CloudWatch Agent and Start command.

### Step 4. If it is the first instance you where you download the CloudWatch, you should create a configuration file there and push it back to the ssm parameter store using the following command:
> $aws ssm put-parameter --name "cloudwatch_config" --type "String" --value file:///opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

Don't forget to put right setting for configuration file.

**If it is not the first one, you need to...
https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-EC2-Instance-fleet.html

