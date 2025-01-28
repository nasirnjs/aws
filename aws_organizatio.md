<h2> Provides Best Practices for Optimizing AWS Accounts </h2>


Key Concepts
Root Account (Management Account):

This is the master account of your AWS Organization.

You can only have one root account per organization.

It is used to create and manage the organization, including creating OUs and inviting member accounts.

Member Accounts:

These are regular AWS accounts that are part of the organization.

They are not root accounts; they are managed by the root account.

You can have multiple member accounts under your organization.

Organizational Units (OUs):

Logical groupings of member accounts.

OUs help you organize accounts by purpose, environment, or team.



---
What You Should Do
Create a Root Account:

This will be your management account for the AWS Organization.

Use this account to create the organization and manage OUs and member accounts.

Create Member Accounts:

These are not root accounts; they are regular AWS accounts that belong to the organization.

You can create as many member accounts as you need.

Move Member Accounts to the Organization:

If you already have existing AWS accounts, you can invite them to join your organization.

These accounts will become member accounts under the root account.

---


What You Should NOT Do
Do Not Create Multiple Root Accounts:

AWS Organizations only allows one root account per organization.

You cannot have multiple root accounts in the same organization.

Do Not Move Root Accounts:

You cannot move a root account into another organization. Each AWS account can only belong to one organization.
---

## AWS Organizations SCP


`LeaveOrganization-Disable`

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "organizations:LeaveOrganization",
      "Resource": "*"
    }
  ]
}
```


## AWS Landing Zone

## AWS Control Tower
















[References 1](https://spacelift.io/blog/aws-multi-account-strategy)

[References 2](https://aws.amazon.com/blogs/architecture/new-whitepaper-provides-best-practices-for-optimizing-aws-accounts/)

