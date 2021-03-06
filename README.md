
# Grand Central App for Splunk

## Manage and Monitor your Cloud Data Providers in Splunk from one centralized data platform.

### This Splunk based app relies on the work done by Project Trumpet and the AWS Organizational model.


Grand Central User's Guide :

Version 1.1.1



Getting Started

Amazon Web Services
Requirements
Grand Central works with the AWS Organizations framework and does not require either Landing Zone or Control Tower to work. By having the organization setup with multiple accounts, Grand Central will be able to discover the accounts and add into management within Splunk. 

Please refer to the Amazon Web Services documentation on how to get started with Organizations : https://aws.amazon.com/premiumsupport/knowledge-center/get-started-organizations/  
    
Before Deploying Grand Central

You will need to be able to create an IAM User in the Master Account and the sub accounts that will be added into management under Splunk. By default there will be two IAM policies created, one to list all the accounts in the Organization and the second will be a deployment policy. 

```
IAM Role Creation Shortcut (beta)

1. Open Cloudformation Template in target account and run https://grandcentraldeployment.s3.amazonaws.com/CFTemplates_GCDeployer_User.json 

2. Copy the Access Key / Secret Key from CloudFormation Output

3. Install grand_central.spl file from github
```
    
Setting up your Grand Central Environment

You will need to create an IAM User in your master account that has a policy with access to list organizations. Here is an example of the JSON Policy :

IAM Policy - Grand_Central_Lister_Policy

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "organizations:ListAccounts",
            "Resource": "*"
        }
    ]
}
```
Next, each AWS Account will need to have the following IAM User and Policy created in order to deploy the data collection capabilities for Splunk : 

```

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "autoscaling:Describe*",
                "cloudformation:*",
                "cloudwatch:Describe*",
                "cloudwatch:Get*",
                "cloudwatch:List*",
                "cloudwatch:PutMetricData",
                "config:DeliverConfigSnapshot",
                "config:DescribeConfigRuleEvaluationStatus",
                "config:DescribeConfigRules",
                "config:GetComplianceDetailsByConfigRule",
                "config:GetComplianceSummaryByConfigRule",
                "ec2:DescribeAddresses",
                "ec2:DescribeImages",
                "ec2:DescribeInstances",
                "ec2:DescribeKeyPairs",
                "ec2:DescribeNetworkAcls",
                "ec2:DescribeRegions",
                "ec2:DescribeReservedInstances",
                "ec2:DescribeSecurityGroups",
                "ec2:DescribeSnapshots",
                "ec2:DescribeSubnets",
                "ec2:DescribeVolumes",
                "ec2:DescribeVpcs",
                "events:DescribeRule",
                "events:PutEvents",
                "events:PutRule",
                "events:PutTargets",
                "firehose:CreateDeliveryStream",
                "firehose:DeleteDeliveryStream",
                "firehose:DescribeDeliveryStream",
                "iam:AttachRolePolicy",
                "iam:CreateRole",
                "iam:CreateUser",
                "iam:DeleteRole",
                "iam:DeleteRolePolicy",
                "iam:DeleteUser",
                "iam:DetachRolePolicy",
                "iam:GetAccessKeyLastUsed",
                "iam:GetAccountPasswordPolicy",
                "iam:GetRole",
                "iam:GetUser",
                "iam:ListAccessKeys",
                "iam:ListUsers",
                "iam:PassRole",
                "iam:PutRolePolicy",
                "kinesis:DescribeStream",
                "kinesis:Get*",
                "kinesis:ListStreams",
                "kms:Decrypt",
                "lambda:CreateFunction",
                "lambda:DeleteFunction",
                "lambda:GetFunctionConfiguration",
                "lambda:RemovePermission",
                "logs:DeleteSubscriptionFilter",
                "logs:DescribeLogGroups",
                "logs:DescribeLogStreams",
                "logs:PutSubscriptionFilter",
                "logs:GetLogEvents",
                "s3:CreateBucket",
                "s3:DeleteBucket",
                "s3:GetAccelerateConfiguration",
                "s3:GetBucketCORS",
                "s3:GetBucketLocation",
                "s3:GetBucketLogging",
                "s3:GetBucketPolicy",
                "s3:GetBucketPolicyStatus",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetBucketTagging",
                "s3:GetLifecycleConfiguration",
                "s3:GetObject",
                "s3:GetObjectAcl",
                "s3:ListAllMyBuckets",
                "s3:ListBucket",
                "s3:PutBucketVersioning",
                "sns:Get*",
                "sns:List*",
                "sns:Publish",
                "sqs:DeleteMessage",
                "sqs:GetQueueAttributes",
                "sqs:GetQueueUrl",
                "sqs:ListQueues",
                "sqs:ReceiveMessage",
                "sqs:SendMessage",
                "sts:AssumeRole"
            ],
            "Resource": "*"
        }
    ]
}
``` 
 
## Credential Smusher
Download the credentials files into a single directory for all the accounts. Once you have all the files (e.g. credentials.csv, credentials-1.csv or accessKey.csv) then run the credentials_smusher.py which will create all_account_credentials.json. 

Adding Master Account

Log into the Grand Central App and navigate to the Accounts Section.  

Click on the “New Organization Master Account” button: 
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/01_gc.png)

Add the Master Account access key / secret key to Splunk: 
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/02_gc.png)

The Master Account should now show up in Splunk:
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/03_.png)

Once the Master Account has been added, now you should be able to view the accounts in the organization. Under Actions, navigate to the List All Accounts in the dropdown:  
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/04_gc.png)

Click the Add button: 

All the accounts in your organization will now show up in Splunk :  
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/05_gc.png)

In order to add the discovered accounts into Splunk, select "Add Acounts in Organizaion to Grand Central" in the Actions dropdown:
![master_account]( https://grandcentraldeployment.s3.amazonaws.com/screenshots/06_gc.png)

Click the add button:
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/07_gc.png)

All the accounts should now show up under management in Splunk:
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/08_gc.png)

```
## Bulk Credentials Upload - two step process : 

# Step 1 - get the credential files
Download all the credential files created for each account into one directory on your local machine. 

#Step 2 - Run credentials_musher.py
Run the credentials_musher.py python script against the directory containing your credentials.csv or AccessKey.csv files.

all_account_accessKeys.json file created.
```
Take the all_account_accessKey.json file (or all_account_credentials.json) and hit the Bulk Credentials Upload button and upload the file: 
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/09_gc.png)

After uploading the file, all of your accounts should be set to Configured:
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/10_gc.png) 

Now, add the destination where you will be sending your data. This is typically a Firehose endpoint on your Splunk Cloud Deployment. 


Here is an example of how you should fill out the fields: 

Note that if you are using Splunk Cloud the URL for your firehose endpoint should look like this:
```
https://http-inputs-firehose-<customer_name>.splunkcloud.com:443
```

BYOL Cloud Deployments
```
https://<your-hec-url>:8088 
```

Where <customer_name> is your stack name. The port (:443) needs to be put in the URL in order for this system to work.
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/11_gc.png)

Your Splunk Accounts should look like this when you're done:
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/12_gc.png) 

### Pro Tip : For now, create a Splunk HEC token for each sourcetype, e.g. aws:cloudtrail for CloudTrail, aws:config for Config and aws:cloudwatchlogs:vpcflow for VPCFlow Logs


Finally, now let’s deploy data collection to all these accounts, click on the Bulk Deplyoment button and select your accounts, regions, destination and data source: 

![master_account]( https://grandcentraldeployment.s3.amazonaws.com/screenshots/13_gc.png)

Click Deploy.

Splunk will communicate with AWS and begin deploying the CloudFormation templates in all the accounts and regions you've selected:


In the Observation Deck dashboard you will see the succesfully deployed Accounts and Regions:
![master_account](https://grandcentraldeployment.s3.amazonaws.com/screenshots/14_gc.png)
