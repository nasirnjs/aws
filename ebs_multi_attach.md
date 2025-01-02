
# Clustered Storage System with GlusterFS on AWS EC2 Instances

This guide describes how to set up utilizing EBS Multi-Attach for shared storage on two AWS EC2 instances.


## Clustered Storage System Overview

A **clustered storage system** is a distributed network of storage devices that work together as a single entity to provide scalable and reliable storage services. It is designed to meet high-performance, availability, and redundancy requirements, especially in environments where large volumes of data need to be processed and stored. These systems allow for data to be accessed by multiple nodes (servers or clients) simultaneously, and they often support automatic failover and load balancing for high availability.


## Simplified GlusterFS 

**GlusterFS** is an open-source, distributed file system designed to provide scalable and highly available network-attached storage (NAS) solutions. It aggregates storage resources from multiple servers, creating a single, unified pool of storage that can be accessed as a single file system. GlusterFS is designed to be highly scalable, fault-tolerant, and elastic, making it ideal for environments that require large-scale storage solutions with high availability and performance. It is widely used in scenarios such as cloud storage, media storage, big data applications, and as a backend for containerized applications.

## Solution Overview

## Prerequisites

-   Minimum 2 EC2 instances
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


### Respective Diagram
![enter image description here](./diadram.drawio.png)
----------

## Step 1: Create EC2 Instances and EBS Volumes

### 1.1 Create EC2 Instances

-   Create 2 EC2 instances in the same region and availability zone on aws console.
-   Assign public IPs to the instances so that they can be accessed via SSH.

### 1.2 Create EBS Volumes (io2)

-   Create two EBS volumes with the type **Provisioned IOPS SSD (io2)**.
-   Ensure that these volumes are in the same availability zone as your EC2 instances.
-   Enable **Multi-Attach** for both volumes so they can be attached to multiple instances.

### 1.3 Attach EBS Volumes to EC2 Instances

-   Attach the created EBS volumes to both EC2 instances.

----------

## Step 2: Format and Mount EBS Volumes

### 2.1 Verify EBS Volume Connection

-   SSH into both EC2 instances and verify the connection of the EBS volume by running:
    
    ```bash
    lsblk
    
    ```
    
-   Ensure that the volume is attached to the instance.
    

### 2.2 Format the EBS Volume

-   Format the EBS volume once on either of the instances (only format it once for the shared file system):
    
    ```bash
    sudo mkfs.xfs /dev/nvme1n1  # Replace 'nvme1n1' with your disk name
    
    ```
    

### 2.3 Mount the EBS Volume

-   Create a directory to mount the EBS volume:
    
    ```bash
    sudo mkdir /home/ubuntu/data
    
    ```
    
-   Mount the EBS volume to the newly created directory:
    
    ```bash
    sudo mount /dev/nvme1n1 /home/ubuntu/data
    
    ```
    
-   Verify the mount:
    
    ```bash
    df -h /home/ubuntu/data
    
    ```
    

----------

## Step 3: Install GlusterFS

### 3.1 Install GlusterFS on Both Instances

-   Add the GlusterFS repository and install the GlusterFS server package:
    
    ```bash
    sudo add-apt-repository ppa:gluster/glusterfs-10
    sudo apt-get update
    sudo apt-get install -y glusterfs-server
    
    ```
    

### 3.2 Start GlusterFS Service

-   Start the GlusterFS service on both instances:
    
    ```bash
    sudo systemctl start glusterd
    sudo systemctl enable glusterd
    
    ```
    

----------

## Step 4: Set Up GlusterFS Cluster

### 4.1 Peer Probe

-   Choose one instance as the primary and the other as the secondary.
    
-   On the **primary instance**, run the following command to add the secondary instance to the cluster:
    
    ```bash
    sudo gluster peer probe <secondary_instance_privateIP>
    
    ```
    
-   If successful, the output will display: `Success`.
    

----------

## Step 5: Create GlusterFS Volume

### 5.1 Create Shared Volume

-   On the **primary instance**, create a GlusterFS volume called `shared-volume` using the mounted EBS volumes. This volume will be replicated across both instances:
    
    ```bash
    sudo gluster volume create shared-volume replica 2 transport tcp <primary_instance_privateIP>:/home/ubuntu/data <secondary_instance_privateIP>:/home/ubuntu/data force
    
    ```
    

### 5.2 Start the GlusterFS Volume

-   Start the `shared-volume` on both instances:
    
    ```bash
    sudo gluster volume start shared-volume
    
    ```
    

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
    
    ```
    
-   Verify that the GlusterFS volume is mounted correctly by creating or updating a file in the `/mnt/shared` directory on either instance.
    

----------

## Step 7: Auto-Mount EBS Volume on Reboot

### 7.1 Add to `/etc/fstab`

-   Edit `/etc/fstab` to automatically mount the EBS volume on reboot:
    
    ```bash
    echo '/dev/nvme1n1 /home/ubuntu/data xfs defaults 0 0' | sudo tee -a /etc/fstab
    
    ```
    

----------

## Conclusion

You have successfully set up a clustered storage system using GlusterFS with shared EBS volumes in AWS. The shared storage is now accessible from both EC2 instances, and the GlusterFS volume ensures that data is synchronized between the two instances.

----------

## Troubleshooting

-   **GlusterFS Peer Probe Fails**: Ensure that security groups and network ACLs allow communication between the EC2 instances on the required ports (24007-24008 for GlusterFS).
-   **Volume Mount Issues**: Verify that the `/etc/fstab` entry is correct and that the volume is accessible before the mount command.

----------
