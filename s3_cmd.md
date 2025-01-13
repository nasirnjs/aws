AWS S3 Command List


List all buckets.\
`aws s3 ls`

List objects in a bucket.\
`aws s3 ls s3://bucket-name`

List objects recursively in a bucket.\
`aws s3 ls s3://bucket-name --recursive`

Get information about a bucket.\
`aws s3api get-bucket-location --bucket bucket-name`

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

**S3 Cloud Front Distribution** 01:25