# AWS-Cloud-Cost-Optimization
Serverless cost-optimization tool built with AWS Lambda and Python (Boto3) that automatically detects and deletes unattached EBS volumes on a daily schedule via EventBridge, using a least-privilege IAM role — reducing wasted cloud storage spend without manual intervention.

## 📌 Problem Statement

When EC2 instances are terminated, their attached EBS volumes are sometimes left behind and continue to incur storage charges indefinitely. This project automates cleanup of these unattached volumes on a recurring schedule.

## 🏗️ Architecture Overview

- **Amazon EventBridge:** Triggers the Lambda function on a daily schedule
- **AWS Lambda (Python + Boto3):** Scans the account for EBS volumes in `available` (unattached) state and deletes them
- **IAM Role:** Least-privilege role scoped to only `DescribeVolumes` and `DeleteVolume`

## 🧰 Tech Stack

AWS Lambda · Python (Boto3) · Amazon EventBridge · Amazon EBS · IAM

## 💻 Lambda Function

```python
import boto3

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    deleted_volumes = []

    volumes = ec2.describe_volumes(
        Filters=[{'Name': 'status', 'Values': ['available']}]
    )['Volumes']

    for volume in volumes:
        volume_id = volume['VolumeId']
        ec2.delete_volume(VolumeId=volume_id)
        deleted_volumes.append(volume_id)

    print(f"Deleted {len(deleted_volumes)} unattached volumes: {deleted_volumes}")
    return {'statusCode': 200, 'deletedVolumes': deleted_volumes}
```

## ✅ Key Steps Performed

- Wrote a Lambda function using Boto3 to identify unattached EBS volumes
- Created a least-privilege IAM Role for the function
- Configured an EventBridge rule to trigger it daily
- Tested against manually detached volumes to confirm safe, targeted deletion

## 🧪 Testing & Validation

- Manually detached an EBS volume from a test EC2 instance
- Ran the function and confirmed only that volume was deleted, with attached volumes untouched

## 📚 Key Learnings

- Boto3 filtering to safely target only unattached resources
- Writing least-privilege IAM policies for Lambda
- Using EventBridge to run serverless functions on a schedule

## 🚀 Future Improvements

- Add a dry-run mode to preview deletions before running them
- Extend to clean up old EBS snapshots as well

## 👤 Author

**Yash**
Aspiring Cloud/IT Support Engineer | AWS Solutions Architect Associate (in progress)
