# AWS EC2 Monitoring with Cloudwatch | Monitor Memory Utilization using CloudWatch | AWS CloudWatch Demo

## Steps:

### Step 1: Create an IAM and Attach CloudWatch and SSM Full Access - EC2-CloudWatch-Role
### Step 2: Create a parameter in Systems Manger --> parameter store with the name "/alarm/AWS-CWAgentLinConfig" and store the value.
#### same paramater store can be used for multiple ec2 instances
### Step 3: Create an EC2 Instance, Attach the role created in Step 1 and Add the commands in the Userdata Section.


## Commands that needs to be added in Userdata Section:
```bash
#!/bin/bash
wget https://s3.amazonaws.com/amazoncloudwatch-agent/linux/amd64/latest/AmazonCloudWatchAgent.zip
unzip AmazonCloudWatchAgent.zip
sudo ./install.sh
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:/alarm/AWS-CWAgentLinConfig -s
```
## Check if EC2 Instance has CWAgent Installed or not:
```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a status
```

## Value for the SSM Parameter (/alarm/AWS-CWAgentLinConfig):
- go to System manager (SSM) --> parameter store --> create a store with same name as(/alarm/AWS-CWAgentLinConfig) used above to fetch data, in userdata section.
- create it before EC2
```bash
{
	"metrics": {
		"append_dimensions": {
			"InstanceId": "${aws:InstanceId}"
		},
		"metrics_collected": {
			"mem": {
				"measurement": [
					"mem_used_percent"
				],
				"metrics_collection_interval": 60
			}
		}
	}
}
```
## Reference/Additional Reading:
1. https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent-New-Instances-CloudFormation.html
2. https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html


