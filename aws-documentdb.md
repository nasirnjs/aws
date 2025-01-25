
<h2>Connecting to an Amazon DocumentDB cluster from outside an Amazon VPC</h2>

Steps 1: Create Two Security Group
- The first step is to create two new security groups in your VPC. 
- The first security group (bastion-host-sg) allows you to SSH into your EC2 instance from your local machine (client).
- The second security group (document-db-sg) enables you to connect to your Amazon DocumentDB cluster on port 27017 from your EC2 instance.
- DocumentDB security group should allow bastion-host-sg

## Steps 2: to Launch an Amazon DocumentDB Cluster

1. **Navigate to the Amazon DocumentDB Console**  
   - Go to the [Amazon DocumentDB console](https://console.aws.amazon.com/docdb/).  
   - Under the **Clusters** section, select **Create**.

2. **Configure Your Cluster**  
   - On the **Create Amazon DocumentDB cluster** page, configure the following settings:
     - **Instance class**: Choose `db.t3.medium` or another instance class based on your requirements.
     - **Number of instances**: Select `2`.
     - Leave other settings at their default values.

3. **Set Up Authentication**  
   - In the **Authentication** section:
     - Enter a **username**.
     - Enter a **password**.

4. **Enable Advanced Settings**  
   - Turn on the **Show advanced settings** option.

5. **Configure Network Settings**  
   - In the **Network settings** section:
     - For **VPC security groups**, select `document-db-sg`.

6. **Create the Cluster**  
   - Review the settings and click **Create cluster**.

7. **Wait for Provisioning**  
   - Amazon DocumentDB will now provision your cluster. This process can take a few minutes.  
   - Wait until both the **cluster** and **instance** statuses show as **Available**.

8. **Connect to Your Cluster**  
   - Once the cluster is ready, proceed with the necessary steps to connect to your Amazon DocumentDB cluster.



Steps 3: Creating an EC2 instance
The next step is to create an EC2 instance in the same Region and VPC that you use to provision your Amazon DocumentDB cluster with Security Group `bastion-host-sg`



Steps 4: Test SSH Connection with bastion host EC2 instance

`ssh -i "ec2Access.pem" ubuntu@ec2-34-229-221-164.compute-1.amazonaws.com`

Steps 5: Download the public key for Amazon DocumentDB from AWS

Transport Layer Security (TLS) is enabled on your Amazon DocumentDB cluster, you need to download the public key for Amazon DocumentDB from AWS.
`wget https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem`


Steps 5: Set up an SSH tunnel to the Amazon DocumentDB cluster

`ssh -i "nasir-dekstop-ohia.pem" -L 27017:docdb-2025-01-24-19-30-29.cluster-cryusegsgush.us-east-2.docdb.amazonaws.com:27017 ubuntu@ec2-3-14-142-76.us-east-2.compute.amazonaws.com -N`

Steps 6: To connect to your Amazon DocumentDB cluster from your Local machine

`mongosh --tlsAllowInvalidHostnames --tls --tlsCAFile global-bundle.pem --username root --password pATXXYYZZmRl  mongosh --retryWrites=false`


<p align="center">
  <img src="./ref-image/documentdb-local-connection.png" alt="Getting started with Amazon DocumentDB using SSH Tunnel" title="Getting started with Amazon DocumentDB using SSH Tunnel" height="250" width="800"/>
  <br/>
  Pic: Getting started with Amazon DocumentDB using SSH Tunnel
</p>




From MongoDB Compass

mongodb://root:pAT3T6PmnalbmRl@127.0.0.1:27017/?directConnection=true&serverSelectionTimeoutMS=2000&tlsAllowInvalidHostnames=true&tls=true&tlsCAFile=global-bundle.pem



[References 1](https://levelup.gitconnected.com/spring-boot-with-amazon-documentdb-2623d7b6cf43)

[References 2](https://www.youtube.com/watch?v=a224dfdfwgc)

