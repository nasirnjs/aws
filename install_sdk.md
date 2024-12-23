# Connect to AWS Console Services Using boto3

## Prerequisites

- Python 3.x installed on your machine.
- An AWS account with the required permissions.
- AWS credentials configured on your local machine or environment.

## Install Required Python Packages

**Steps 1:** Install the `venv` module:\
`sudo apt install python3.10-venv`

**Steps 2:** Create boto3 Script\
**Create a Python script (e.g., app.py) and add the following code**

```py
import boto3

# Create a session without specifying credentials (uses the default profile)
session = boto3.Session(
    region_name='ap-south-1'
)

print(f"Connected to AWS region: {session.region_name}")

sts_client = session.client('sts')
identity = sts_client.get_caller_identity()

print(f"AWS Account ID: {identity['Account']}")
print(f"User ARN: {identity['Arn']}")
print(f"User ID: {identity['UserId']}")

# Create an S3 client to list buckets
s3_client = session.client('s3')
response = s3_client.list_buckets()

# Check if there are any S3 buckets and print them
if 'Buckets' in response:
    print("\nList of S3 Buckets:")
    for bucket in response['Buckets']:
        print(f"- {bucket['Name']}")
else:
    print("No S3 buckets found.")
```

**Steps 3:** Create a Virtual Environment: Navigate to your project directory and run:\
`python3 -m venv .venv`

**Steps 4:** Activate the Virtual Environment:\
`source .venv/bin/activate`

**Steps 5:** Install the boto3 library:\
`pip install boto3`

**Steps 6:** Run the Python script:\
`python3 app.py`

**Steps 7:** To exit from a virtual environment in Python.\
`deactivate`


## Additional Resources

- [boto3 Documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)
- [AWS CLI Configuration](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)