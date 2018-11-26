### Step 1. Create IAM Roles
**Reason**: In order to enable the CloudWatch agent and having it installed on a server and send metrics to CloudWatch, you need to create an IAM role with the right permissions. 

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
