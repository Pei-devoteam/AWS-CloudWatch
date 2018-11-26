### Step 1. Create two IAM Roles
The first role named CloudWatchAdminRole needs the following policies: AmazonEC2RoleforSSM, CloudWatchAgentAdminPolicy and AmazonSSMFullAccess. 
(We are sure we need getparameter right for this role to start the CW Agent for sure, but we do not know what else is missing at this stage.)

The second role named CloudWatchSeverRole needs the following policies:
AmazonEC2RoleforSSM, CloudWatchAgentServerPolicy and AmazonSSMFullAccess.


### Step 2. Attach the IAM Role to the instances that you want to monitor.

### Step 3. Go to SSM page in aws. Run the commands to install CloudWatch Agent and Start command.

### Step 4. If it is the first instance you where you download the CloudWatch, you should create a configuration file there and push it back to the ssm parameter store using the following command:
> $aws ssm put-parameter --name "cloudwatch_config" --type "String" --value file:///opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

Don't forget to put right setting for configuration file.

**If it is not the first one, you need to...
https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-EC2-Instance-fleet.html

