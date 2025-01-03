

# Script to create multiple IAM users in AWS

## Check Your Default Interpreter and change this to bash

Check Your Default Interpreter.\
`ls -l /bin/sh`

Note: If /bin/sh points to dash or something other than bash, you must explicitly invoke Bash.

Change /bin/sh to Point to Bash.\
`sudo ln -sf /bin/bash /bin/sh`

Now check again Your Default Interpreter.\
`ls -l /bin/sh`

If you want to Revert /bin/sh Back to Dash.\
`sudo ln -sf /bin/dash /bin/sh`

## Create script file and assign executable permissions

`touch aws-users.sh`

`chmod -R 700 aws-users.sh`

## pest he following script and before run script Update users inside of script

```bash
#!/bin/bash

# Ensure AWS CLI is configured with the required credentials and permissions before running.

# Fetch the AWS Account ID dynamically
account_id=$(aws sts get-caller-identity --query "Account" --output text)

# Function to create an IAM user
create_user() {
  local username=$1
  local password="TempPassword123!"  # Replace with your desired temporary password

  echo "Creating IAM user: $username"

  # Create the IAM user
  aws iam create-user --user-name "$username" > /dev/null 2>&1
  if [[ $? -eq 0 ]]; then
    echo "Successfully created user: $username"

    # Set a temporary password for the user
    echo "Setting temporary password for $username"
    aws iam create-login-profile --user-name "$username" --password "$password" --password-reset-required > /dev/null 2>&1
    if [[ $? -eq 0 ]]; then
      echo "Successfully set temporary password for $username"
    else
      echo "Failed to set temporary password for $username"
    fi

    # Optionally add the user to a group
    group_name="devops"  # Replace with your group name
    echo "Adding $username to group $group_name"
    aws iam add-user-to-group --user-name "$username" --group-name "$group_name" > /dev/null 2>&1
    if [[ $? -eq 0 ]]; then
      echo "Successfully added $username to group $group_name"
    else
      echo "Failed to add $username to group $group_name"
    fi

    # Output the Console sign-in URL
    echo "Console sign-in URL for $username: https://$account_id.signin.aws.amazon.com/console"

  else
    echo "Failed to create user: $username (Check for errors or existing user)"
  fi
}

# List of IAM usernames to create
user_list=("user1" "user2" "user3")  # Replace these with the actual usernames

# Loop through the list and create users
for user in "${user_list[@]}"; do
  create_user "$user"
done

echo "All users processed."

```

##  Set executable permissions for the script and run

Run the Script Using Bash: Execute the script explicitly using bash.
`bash ./script.sh`

or

`./script`


## Sign in Console with sign-in URL after each user creation like.

`user1: https://123456789012.signin.aws.amazon.com/console`