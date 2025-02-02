AWS S3 Command List


List all buckets.\
`aws s3 ls`

List objects in a bucket.\
`aws s3 ls s3://bucket-name`

List objects recursively in a bucket.\
`aws s3 ls s3://bucket-name --recursive`

To create an Amazon S3 bucket in a specific region.\
`aws s3 mb s3://my-new-buckettttttttttttttttttttttttt --region us-east-2`

Get information about a bucket.\
`aws s3api get-bucket-location --bucket bucket-name`

If the LocationConstraint is null, that means the S3 bucket is located in the US East (N. Virginia) region default region for S3.\
`aws s3api get-bucket-location --bucket nasirtechtalks.xyz`


**Bucket Management**

Create a bucket.\
`aws s3 mb s3://bucket-name`

Delete a bucket.\
`aws s3 rb s3://bucket-name`

Delete a bucket with contents.\
`aws s3 rb s3://bucket-name --force`


**Object Management**

Upload a file to a bucket.\
`aws s3 cp file.txt s3://bucket-name`

Download a file from a bucket.\
`aws s3 cp s3://bucket-name/file.txt .`

Sync local folder to bucket.\
`aws s3 sync /local/path s3://bucket-name`

Sync bucket to local folder.\
`aws s3 sync s3://bucket-name /local/path`

Delete an object.\
`aws s3 rm s3://bucket-name/file.txt`

Delete objects recursively in a bucket.\
`aws s3 rm s3://bucket-name --recursive`

Move an object.\
`aws s3 mv s3://source-bucket/file.txt s3://destination-bucket/`

**Object and Bucket Policies**

Set bucket policy.\
`aws s3api put-bucket-policy --bucket bucket-name --policy file://policy.json`

Get bucket policy.\
`aws s3api get-bucket-policy --bucket bucket-name`

Delete bucket policy.\
`aws s3api delete-bucket-policy --bucket bucket-name`

Enable bucket versioning.\
`aws s3api put-bucket-versioning --bucket bucket-name --versioning-configuration Status=Enabled`

Get bucket versioning status.\
`aws s3api get-bucket-versioning --bucket bucket-name`

**Access Management**

List bucket access control list (ACL).\
`aws s3api get-bucket-acl --bucket bucket-name`

Set bucket ACL.\
`aws s3api put-bucket-acl --bucket bucket-name --acl public-read`

Get object ACL.\
`aws s3api get-object-acl --bucket bucket-name --key object-key`

Set object ACL.\
`aws s3api put-object-acl --bucket bucket-name --key object-key --acl public-read`

**Advanced Operations**

Enable static website hosting.\
`aws s3 website s3://bucket-name --index-document index.html --error-document error.html`

Get bucket website configuration.\
`aws s3api get-bucket-website --bucket bucket-name`

Delete bucket website configuration.\
`aws s3api delete-bucket-website --bucket bucket-name`

**Enable bucket logging:**

`aws s3api put-bucket-logging --bucket bucket-name --bucket-logging-status file://logging.json`

Get bucket logging configuration.\
`aws s3api get-bucket-logging --bucket bucket-name`

**Encryption**

Enable server-side encryption.\
`aws s3api put-bucket-encryption --bucket bucket-name --server-side-encryption-configuration file://encryption.json`

Get bucket encryption.\
`aws s3api get-bucket-encryption --bucket bucket-name`

**Delete bucket encryption:**

`aws s3api delete-bucket-encryption --bucket bucket-name`

**Replication**

Set up replication configuration.\
`aws s3api put-bucket-replication --bucket bucket-name --replication-configuration file://replication.json`

Get replication configuration.\
`aws s3api get-bucket-replication --bucket bucket-name`

Delete replication configuration.\
`aws s3api delete-bucket-replication --bucket bucket-name`


**batch job or bucket replications**

**S3 Cloud Front Distribution**



**Amazon S3 bucket policy that grants public read access to all objects in the bucket**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::test-bycket/*"
        }
    ]
}
```

# AWS S3 Block Public Access Settings

## 1. Block Public Access to Buckets and Objects Granted Through New Access Control Lists (ACLs)
**What it Does:**  
Prevents the creation of new public ACLs for buckets or objects. Existing ACLs that grant public access remain unchanged.

**Use Case:**  
You want to enforce a policy where no future objects or buckets can accidentally be set to public using ACLs while keeping the existing ones intact.

**Example:**
- **Before Enabling:** A user can upload an object and assign it a `public-read` ACL, making it accessible to anyone.
- **After Enabling:** Attempts to set `public-read` ACLs on new objects or buckets are blocked.

---

## 2. Block Public Access to Buckets and Objects Granted Through Any Access Control Lists (ACLs)
**What it Does:**  
Completely disables public access granted through any existing or new ACLs.

**Use Case:**  
You want to ensure that ACLs cannot be used to grant public access, even if they were created in the past.

**Example:**  
If an object already has a `public-read` ACL, enabling this setting will override it, ensuring the object is no longer publicly accessible.

---

## 3. Block Public Access to Buckets and Objects Granted Through New Public Bucket or Access Point Policies
**What it Does:**  
Prevents the creation of new bucket or access point policies that grant public access.

**Use Case:**  
You want to maintain strict access control by ensuring no new policies can expose your data publicly, while keeping existing policies intact for legacy applications.

**Example:**
- **Before Enabling:** A developer can create a bucket policy that allows `s3:GetObject` for all users (`Principal: *`).
- **After Enabling:** Attempts to create such policies are blocked.

---

## 4. Block Public and Cross-Account Access to Buckets and Objects Through Any Public Bucket or Access Point Policies
**What it Does:**  
Ignores all public or cross-account access granted through bucket or access point policies.

**Use Case:**  
You want to strictly prevent any public or cross-account access to your bucket, regardless of existing bucket policies.

**Example:**  
If a bucket policy allows access to a specific external account or makes it public, enabling this setting will override and ignore that policy.
