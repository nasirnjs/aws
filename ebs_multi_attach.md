
# Clustered Storage System with GlusterFS on AWS EC2 Instances

This guide describes how to set up utilizing EBS Multi-Attach for shared storage on two AWS EC2 instances.


## Clustered Storage System Overview

A **clustered storage system** is a distributed network of storage devices that work together as a single entity to provide scalable and reliable storage services. It is designed to meet high-performance, availability, and redundancy requirements, especially in environments where large volumes of data need to be processed and stored. These systems allow for data to be accessed by multiple nodes (servers or clients) simultaneously, and they often support automatic failover and load balancing for high availability.


## Simplified GlusterFS 

**GlusterFS** is an open-source, distributed file system designed to provide scalable and highly available network-attached storage (NAS) solutions. It aggregates storage resources from multiple servers, creating a single, unified pool of storage that can be accessed as a single file system. GlusterFS is designed to be highly scalable, fault-tolerant, and elastic, making it ideal for environments that require large-scale storage solutions with high availability and performance. It is widely used in scenarios such as cloud storage, media storage, big data applications, and as a backend for containerized applications.

## Solution Overview

## Prerequisites

-   Minimum 2 EC2 instances
-   Suported EC2 Instances are General Purpose:`m5`, `m5d`, `m5a`, `m5ad`, `m5n`, `m5dn`, `m6i`, `m6id` Compute Optimized: `c5`, `c5d`, `c5a`, `c5ad`, `c5n`, `c6i`, `c6id` Memory Optimized: `r5`, `r5d`, `r5a`, `r5ad`, `r5n`, `r5dn`, `r6i`, `r6id` Storage Optimized: `i3en`, `i4i` Accelerated Computing: `p3dn.24xlarge`, `p4d`
-   Security Group for allow both EC2 Instance Glusterfs Port
-   Both instances in the same AWS region and availability zone
-   Tested on AWS Provided Ubuntu 24.04 LTS
-   EBS Volume Type: Provisioned IOPS SSD (io2) to support Multi-Attach
-   SSH access to EC2 instances (ensure both instances have public IPs)
-   External EBS Multi-Attach enabled for volumes
### Steps

1.  **Create EC2 Instances and EBS Volumes**
2.  **Attach EBS Volumes to Both Instances**
3.  **Format and Mount EBS Volumes**
4.  **Install GlusterFS and Set Up Cluster**
5.  **Create GlusterFS Volume and Mount Shared Storage**
6.  **Auto-mount EBS Volume on Instance Reboot**


----------

## Step 1: Create Two EC2 Instances and One EBS Volumes

### 1.1 Create 2 EC2 Instances

-   Create 2 EC2 instances in the same region and availability zone on aws console.
-   Assign public IPs to the instances so that they can be accessed via SSH.

### 1.2 Create 1 EBS Volumes, type should be (io2)

-   Create One EBS volumes with the type **Provisioned IOPS SSD (io2)**.
-   Ensure that these volumes are in the same availability zone as your EC2 instances.
-   Enable **Multi-Attach** for both volumes so they can be attached to multiple instances.

### 1.3 Attach EBS Volumes to EC2 Instances

-   Attach the created EBS volumes to both EC2 instances.

----------

## Step 2: Format and Mount EBS Volumes

### 2.1 Verify EBS Volume Connection

-   SSH into both EC2 instances and verify the connection of the EBS volume by running:
    
    `sudo lsblk`
    
-   Ensure that the volume is attached to the instance.
    

### 2.2 Format the EBS Volume from Primary

-   Format the EBS volume once on either of the instances (only format it once for the shared file system):
    
    `sudo mkfs.xfs /dev/nvme1n1`  `#Replace 'nvme1n1' with your disk name`
    

### 2.3 Mount the EBS Volume both ec2 instance

-   Create a directory to mount the EBS volume:
    
    `sudo mkdir /home/ubuntu/ebs-vol`
    
-   Mount the EBS volume to the newly created directory:
    
    `sudo mount /dev/nvme1n1 /home/ubuntu/ebs-vol`
    
-   Verify the mount:
    
    `sudo df -h /home/ubuntu/ebs-vol`
    

----------

## Step 3: Install GlusterFS

### 3.1 Install GlusterFS on Both Instances

-   Add the GlusterFS repository and install the GlusterFS server package:
    
    ```bash
    sudo add-apt-repository ppa:gluster/glusterfs-10
    sudo apt-get update
    sudo apt-get install -y glusterfs-server
    
    ```
    

### 3.2 Start GlusterFS Service on Both Instances

-   Start the GlusterFS service on both instances:
    
    ```bash
    sudo systemctl start glusterd
    sudo systemctl enable glusterd
    sudo systemctl daemon-reload
    sudo systemctl restart glusterd
    sudo systemctl status glusterd
    
    ```
    

----------

## Step 4: Set Up GlusterFS Cluster

### 4.1 Peer Probe from primary

-   Choose one instance as the primary and the other as the secondary.
    
-   On the **primary instance**, run the following command to add the secondary instance to the cluster:
    
    `sudo gluster peer probe <secondary_instance_privateIP>`

    `sudo gluster peer probe 10.0.1.213`
    
-   If successful, the output will display: `Success`.
    

----------

## Step 5: Create GlusterFS Volume

### 5.1 Create Shared Volume from primary

-   On the **primary instance**, create a GlusterFS volume called `shared-volume` using the mounted EBS volumes. This volume will be replicated across both instances:
    
    ```bash
    sudo gluster volume create ebs-shared-vol replica 2 transport tcp <primary_instance_privateIP>:/home/ubuntu/data <secondary_instance_privateIP>:/home/ubuntu/data force
    Example:
    sudo gluster volume create ebs-shared-vol replica 2 transport tcp 172.31.24.63:/home/ubuntu/ebs-vol 172.31.17.20:/home/ubuntu/ebs-vol force
    
    ```
    

### 5.2 Start the GlusterFS Volume from Primary

-   Start the `shared-volume` on both instances:
    
    `sudo gluster volume start ebs-shared-vol`
    

----------

## Step 6: Mount GlusterFS Volume

### 6.1 Mount the GlusterFS Volume on Both Instances

-   Create a mount point directory (e.g., `/mnt/shared`):
    
    ```bash
    sudo mkdir /mnt/shared
    
    ```
    
-   Mount the `shared-volume` GlusterFS volume to this directory:
    
    ```bash
    sudo mount -t glusterfs <primary_instance_privateIP>:/shared-volume /mnt/shared

    sudo mount -t glusterfs 10.0.1.227:/ebs-shared-vol /mnt/shared
    
    ```
    
-   Verify that the GlusterFS volume is mounted correctly by creating or updating a file in the `/mnt/shared` directory on either instance.
    

----------

## Step 7: Auto-Mount EBS Volume on Reboot

### 7.1 Add to `/etc/fstab`

-   Edit `/etc/fstab` to automatically mount the EBS volume on reboot:
    
    ```bash
    echo '/dev/nvme1n1 /home/ubuntu/ebs-vol xfs defaults 0 0' | sudo tee -a /etc/fstab
    
    ```
    

----------

## Conclusion

You have successfully set up a clustered storage system using GlusterFS with shared EBS volumes in AWS. The shared storage is now accessible from both EC2 instances, and the GlusterFS volume ensures that data is synchronized between the two instances.

----------

## Troubleshooting

-   **GlusterFS Peer Probe Fails**: Ensure that security groups and network ACLs allow communication between the EC2 instances on the required ports (24007-24008 for GlusterFS).
-   **Volume Mount Issues**: Verify that the `/etc/fstab` entry is correct and that the volume is accessible before the mount command.

----------
