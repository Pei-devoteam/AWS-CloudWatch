### Step 1. Create two IAM Roles
The first role named CloudWatchAdminRole should have AmazonEC2RoleforSSM and CloudWatchAgentAdminPolicy attached.
The second role named CloudWatchSeverRole should have AmazonEC2RoleforSSM and CloudWatchAgentServerPolicy attached.

The first instance needs to be attached to the AdminRole. The later instances only need to be attached to the ServerRole.

### Step 2. Attach the IAM Role to the instance that you want to monitor

### Step 3. Go to SSM page in aws. Run the commands to install CloudWatch Agent.

### Step 4. If it is the first instance you where you download the CloudWatch, you should create a configuration file there and push it back to the ssm parameter store using the following command:
> $aws ssm put-parameter --name "cloudwatch_config" --type "String" --value file:///opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

Don't forget to put right setting for configuration file.


### Step 3. Download the CloudWatch Agent Package and install the agent on your Amazon EC2 Instance.
There are two ways to accomplish this step. One with AWS Systems Manager, and another with S3 Download Link. Here I will introduce the second one. [Please check out the documentation on aws for more information.](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html#download-CloudWatch-Agent-on-EC2-Instance-first) 

**Procedures**:
1. Depending on your architecture and platform, choose the right download link on this [webpage](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-first-instance.html#download-CloudWatch-Agent-on-EC2-Instance-first). 

2. SSH to your ec2 instance and type the following command to download the CloudWatch agent.
> $ wget download-link

3. Depending on your operation system, you need different commands to install the package. Please refer to the documentation page of        aws. If your ec2 instance is running on linux, you can use the following command: 
> $ sudo rpm -U ./amazon-cloudwatch-agent.rpm


### Step 4. Create the Agent Configuration file on your instance.
You can choose to create the configuration file using the wizard or create and edit it manually by yourself. I will introduce the method of creating it with the wizard as it is easier.

**Procedures**:
1. Type the following command in your ec2 terminal.

> $ vi /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

> $ vi /opt/aws/amazon-cloudwatch-agent/bin/config.json

2. Copy the following content into the previous configuration file.
```    
    {
      "agent": {
        "metrics_collection_interval": 10,
        "logfile": "/opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log"
      },
      "metrics": {
        "metrics_collected": {
          "cpu": {
            "resources": [
              "*"
            ],
            "measurement": [
              {"name": "cpu_usage_idle", "rename": "CPU_USAGE_IDLE", "unit": "Percent"},
              {"name": "cpu_usage_nice", "unit": "Percent"},
              "cpu_usage_guest"
            ],
            "totalcpu": false,
            "metrics_collection_interval": 10,
            "append_dimensions": {
              "customized_dimension_key_1": "customized_dimension_value_1",
              "customized_dimension_key_2": "customized_dimension_value_2"
            }
          },
          "disk": {
            "resources": [
              "/",
              "/tmp"
            ],
            "measurement": [
              {"name": "free", "rename": "DISK_FREE", "unit": "Gigabytes"},
              "total",
              "used"
            ],
             "ignore_file_system_types": [
              "sysfs", "devtmpfs"
            ],
            "metrics_collection_interval": 60,
            "append_dimensions": {
              "customized_dimension_key_3": "customized_dimension_value_3",
              "customized_dimension_key_4": "customized_dimension_value_4"
            }
          },
          "diskio": {
            "resources": [
              "*"
            ],
            "measurement": [
              "reads",
              "writes",
              "read_time",
              "write_time",
              "io_time"
            ],
            "metrics_collection_interval": 60
          },
          "swap": {
            "measurement": [
              "swap_used",
              "swap_free",
              "swap_used_percent"
            ]
          },
          "mem": {
            "measurement": [
              "mem_used",
              "mem_cached",
              "mem_total"
            ],
            "metrics_collection_interval": 1
          },
          "net": {
            "resources": [
              "eth0"
            ],
            "measurement": [
              "bytes_sent",
              "bytes_recv",
              "drop_in",
              "drop_out"
            ]
          },
          "netstat": {
            "measurement": [
              "tcp_established",
              "tcp_syn_sent",
              "tcp_close"
            ],
            "metrics_collection_interval": 60
          },
          "processes": {
            "measurement": [
              "running",
              "sleeping",
              "dead"
            ]
          }
        },
        "append_dimensions": {
          "ImageId": "${aws:ImageId}",
          "InstanceId": "${aws:InstanceId}",
          "InstanceType": "${aws:InstanceType}",
          "AutoScalingGroupName": "${aws:AutoScalingGroupName}"
        },
        "aggregation_dimensions" : [["ImageId"], ["InstanceId", "InstanceType"], ["d1"],[]]
      },
      "logs": {
        "logs_collected": {
          "files": {
            "collect_list": [
              {
                "file_path": "/opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log",
                "log_group_name": "amazon-cloudwatch-agent.log",
                "log_stream_name": "amazon-cloudwatch-agent.log",
                "timezone": "UTC"
              },
              {
                "file_path": "/opt/aws/amazon-cloudwatch-agent/logs/test.log",
                "log_group_name": "test.log",
                "log_stream_name": "test.log",
                "timezone": "Local"
              }
            ]
          }
        },
        "log_stream_name": "my_log_stream_name"
      }
    }
```

### Step 5. Start the CloudWatch Agent
**Procedures**:
1. For linux system, type the following if you saved the configuration file on your ec2 instance:
> $ sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s

### Step 6. Verify if the CloudWatch Agent is running
**Procedures**:
1. Run the following command
> $ /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a status

   You will see the following text if it's running.

> {
  "status": "running",
  "starttime": "2018-11-15T15:47:52+0000",
  "version": "1.204682.0"}

### Step 7. Upload config file to ssm parameter store

> $aws ssm put-parameter --name "cloudwatch_config" --type "String" --value file:///opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

don't forget to put right setting for configuration file.
