
# Configure AWS CLI with Access Keys

**Step 1: Install AWS CLI**

Install AWS Command Line Interface from [Here](https://aws.amazon.com/cli/)

`aws --version`
 

**Step 2: Configure AWS CLI with Access Keys**

To use AWS CLI, you need to configure it with your AWS access keys (Access Key ID and Secret Access Key). If you don’t have these keys, you can create them in the AWS Management Console.

1. Create Access Keys in AWS Console:
- Go to the IAM Console.
- In the left sidebar, click Users.
- Select the user you want to create access keys for.
- Go to the Security credentials tab.
- Under Access keys, click Create access key.
- Download the Access Key ID and Secret Access Key. Save them securely, as the Secret Access Key will not be shown again.

Configure AWS CLI with Access Keys.\
`aws configure`

Enter the following details:
- AWS Access Key ID: Enter the Access Key ID from the previous step.
- AWS Secret Access Key: Enter the Secret Access Key from the previous step.
- Default region name: Enter the AWS region you want to use (e.g., us-west-2).
- Default output format: Enter the output format (e.g., json, text, or table).
  
**Step 3: Verify Configuration**

Verify the setup by running a simple AWS CLI command to check your configuration.

`aws s3 ls`

