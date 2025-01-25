
<h2>Connecting to an Amazon DocumentDB cluster from outside an Amazon VPC</h2>


<p align="center">
  <img src="./ref-image/documentdb-local-connection.png" alt="Getting started with Amazon DocumentDB using SSH Tunnel" title="Getting started with Amazon DocumentDB using SSH Tunnel" height="250" width="800"/>
  <br/>
  Pic: Getting started with Amazon DocumentDB using SSH Tunnel
</p>

## Step 1: Create Two Security Groups

1. **Create Two New Security Groups in Your VPC**:
   - **First Security Group (`bastion-host-sg`)**:  
     This security group allows SSH access to your EC2 instance from your local machine (client).
   - **Second Security Group (`document-db-sg`)**:  
     This security group enables connections to your Amazon DocumentDB cluster on port `27017` from your EC2 instance.

2. **Configuration Details**:
   - The DocumentDB security group should allow traffic `27017` port from the Bastion Host security group.


## Steps 2: to Launch an Amazon DocumentDB Cluster

2.1. **Navigate to the Amazon DocumentDB Console**  
   - Go to the [Amazon DocumentDB console](https://console.aws.amazon.com/docdb/).  
   - Under the **Clusters** section, select **Create**.

2.2. **Configure Your Cluster**  
   - On the **Create Amazon DocumentDB cluster** page, configure the following settings:
     - **Instance class**: Choose `db.t3.medium` or another instance class based on your requirements.
     - **Number of instances**: Select `2`.
     - Leave other settings at their default values.

2.3. **Set Up Authentication**  
   - In the **Authentication** section:
     - Enter a **username**.
     - Enter a **password**.

2.4. **Enable Advanced Settings**  
   - Turn on the **Show advanced settings** option.

2.5. **Configure Network Settings**  
   - In the **Network settings** section:
     - For **VPC security groups**, select `document-db-sg`.

2.6. **Create the Cluster**  
   - Review the settings and click **Create cluster**.

2.7. **Wait for Provisioning**  
   - Amazon DocumentDB will now provision your cluster. This process can take a few minutes.  
   - Wait until both the **cluster** and **instance** statuses show as **Available**.

2.8. **Connect to Your Cluster**  
   - Once the cluster is ready, proceed with the necessary steps to connect to your Amazon DocumentDB cluster.



## Steps 3: Creating an EC2 instance
The next step is to create an EC2 instance in the same Region and VPC that you use to provision your Amazon DocumentDB cluster with Security Group `bastion-host-sg`



## Steps 4: Test SSH Connection with bastion host EC2 instance

```
ssh -i "nasir-desktop-ohia.pem" ubuntu@ec2-34-229-221-164.compute-1.amazonaws.com
```

## Steps 5: Download the public key for Amazon DocumentDB from AWS

Transport Layer Security (TLS) is enabled on your Amazon DocumentDB cluster, you need to download the public key for Amazon DocumentDB from AWS.

`wget https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem`


## Steps 6: Set up an SSH tunnel to the Amazon DocumentDB cluster

```
ssh -i "nasir-dekstop-ohia.pem" -L 27017:docdb-2025-01-24-19-30-29.cluster-cryusegsgush.us-east-2.docdb.amazonaws.com:27017 ubuntu@ec2-3-14-142-76.us-east-2.compute.amazonaws.com -N
```

**Explanation**

- `ssh`: Initiates an SSH connection.
- `-i "nasir-desktop-ohia.pem"`: Specifies the private key file (nasir-dekstop-ohia.pem).
- `-L 27017:docdb-2025-01-24-19-30-29.cluster-cryusegsgush.us-east-2.docdb.amazonaws.com:27017`: Local port 27017 is forwarded to port 27017 on the Amazon DocumentDB cluster end point `docdb-2025-01-24-19-30-29.cluster-cryusegsgush.us-east-2.docdb.amazonaws.com`
- `ubuntu@ec2-3-14-142-76.us-east-2.compute.amazonaws.com`: Specifies the bastion host and the ubuntu user for the connection.
- `-N`: Instructs SSH not to execute remote commands; the session is used solely for port forwarding.

**Note: Keep the tunnel open; do not close console. Open a new console to continue working.**

## Steps 7: To connect to your Amazon DocumentDB cluster from your Local machine

```
mongosh --tlsAllowInvalidHostnames --tls --tlsCAFile global-bundle.pem --username root --password pATXXYYZZmRl  mongosh --retryWrites=false
```


## From MongoDB Compass

Establish a new connection, paste the connection string, and configure the certificate in the TLS/SSL settings. Select the certificate bundle, verify the tlsAllowInvalidHostnames parameter, and then click "Connect.

<p align="center">
  <img src="./ref-image/mongodb-compass-tls.png" alt="MongoDB Compass connection string, and point to the certificate in the TLS/SsL configurations" title="MongoDB Compass connection string, and point to the certificate in the TLS/SsL configurations" height="500" width="800"/>
  <br/>
  Pic: MongoDB Compass connection string, and point to the certificate in the TLS/SsL configurations
</p>

Replace the connection string with your own. Here's an example of mine for reference.\
```
mongodb://root:pAT3T6PmnalbmRl@127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&tlsAllowInvalidHostnames=true&tls=true&tlsCAFile=global-bundle.pem
```


🎉 Congratulations! You have successfully connected to AWS DocumentDB from your local machine. 🚀🎊


[References 1](https://aws.amazon.com/blogs/database/part-3-getting-started-with-amazon-documentdb-using-robo-3t/)

[References 2](https://aws.amazon.com/blogs/database/part-1-getting-started-with-amazon-documentdb-using-amazon-ec2/)

[References 3](https://docs.aws.amazon.com/documentdb/latest/developerguide/connect-from-outside-a-vpc.html)

[References 4](https://levelup.gitconnected.com/spring-boot-with-amazon-documentdb-2623d7b6cf43)

[References 5](https://www.youtube.com/watch?v=a224dfdfwgc)

