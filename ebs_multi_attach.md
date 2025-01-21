
<h2> Replicated GlusterFS Cluster on AWS EC2 via EBS </h2>

This guide walks you through setting up a replicated GlusterFS cluster using AWS EC2 instances and EBS volumes with Multi-Attach support. This setup provides high availability and shared storage across multiple instances.


## GlusterFS
GlusterFS is an open-source, distributed file system that aggregates storage resources from multiple servers into a unified pool. It is scalable, fault-tolerant, and well-suited for applications requiring high availability and performance, such as cloud storage and big data solutions.

## EBS Multi-Attach
EBS Multi-Attach is a feature of Amazon Elastic Block Store (EBS) that allows a single EBS volume to be simultaneously attached to multiple EC2 instances within the same availability zone. This capability is particularly beneficial for clustered applications like GlusterFS, as it enables shared storage access while maintaining consistency and reliability.

## Key Benefits of EBS Multi-Attach
- High Availability: Ensures data accessibility even if one instance fails.
- Performance: Each instance can achieve the full performance of the EBS volume.
- Consistency: Enables multiple nodes to access and modify data simultaneously with consistent performance.

## Solution Overview

### Prerequisites

- Minimum 2 EC2 instances.
- Supported EC2 instances include:
  - **General Purpose**: `m5`, `m5d`, `m5a`, `m5ad`, `m5n`, `m5dn`, `m6i`, `m6id`.
  - **Compute Optimized**: `c5`, `c5d`, `c5a`, `c5ad`, `c5n`, `c6i`, `c6id`.
  - **Memory Optimized**: `r5`, `r5d`, `r5a`, `r5ad`, `r5n`, `r5dn`, `r6i`, `r6id`.
  - **Storage Optimized**: `i3en`, `i4i`.
  - **Accelerated Computing**: `p3dn.24xlarge`, `p4d`.
- Security group allowing GlusterFS ports (24007-24008) between both EC2 instances.
- Both instances must be in the same AWS region and availability zone.
- Tested on AWS Provided Ubuntu 24.04 LTS.
- EBS volume type: **Provisioned IOPS SSD (io2)** to support Multi-Attach.
- SSH access to EC2 instances (ensure both instances have public IPs).
- External EBS Multi-Attach enabled for volumes.

## Steps Overview

1.  **Create EC2 Instances and EBS Volumes**
2.  **Attach EBS Volumes to Both Instances**
3.  **Format and Mount EBS Volumes**
4.  **Install GlusterFS and Set Up Cluster**
5.  **Create GlusterFS Volume and Mount Shared Storage**
6.  **Auto-mount EBS Volume on Instance Reboot**


----------

## Steps

### 1. Create EC2 Instances and EBS Volumes

1.1 **Create Two EC2 Instances**
- Create two EC2 instances in the same region and availability zone.
- Assign public IPs to both instances for SSH access.

1.2 **Create One EBS Volume**
- Create an **io2** EBS volume in the same availability zone as the EC2 instances.
- Enable **Multi-Attach** for the volume.

1.3 **Attach EBS Volume to Both Instances**
- Attach the created EBS volume to both instances.

## Step 2: Format and Mount EBS Volumes  

### 2.1 Verify EBS Volume Connection  
- SSH into both EC2 instances.  
- Verify the EBS volume is connected by running the following command:\
`sudo lsblk`
- Ensure that the volume is attached to the instance.
    

### 2.2 Format the EBS Volume from Primary  

- Format the EBS volume only once on either of the instances (this is for the shared file system):\
**Replace 'nvme1n1' with your disk name**  
`sudo mkfs.xfs /dev/nvme1n1`


### 2.3 Mount the EBS Volume both (Primary & Secondary) EC2 Instance

- Create a directory to mount the EBS volume:\
  `sudo mkdir /home/ubuntu/ebs-vol`
- Mount the EBS volume to the newly created directory:\
  `sudo mount /dev/nvme1n1 /home/ubuntu/ebs-vol`
- Verify the mount:\
  `sudo df -h /home/ubuntu/ebs-vol`

<p align="center">
  <img src="./ref-image/format-volume.png" alt="Formate and mount volume" title="Formate and mount volume" height="350" width="750"/>
  <br/>
  Pic: Formate and mount volume
</p>


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
## Step 4: Set Up GlusterFS Cluster

### 4.1 Peer Probe from Primary

-   Choose one instance as the primary and the other as the secondary.
    
-   On the **primary instance**, run the following command to add the secondary instance to the cluster:
    
    `sudo gluster peer probe <secondary_instance_privateIP>`

    `sudo gluster peer probe 10.0.1.213`
    
-   If successful, the output will display: `Success`.
    
## Step 5: Create GlusterFS Volume

### 5.1 Create Shared Volume from Primary

-   On the **primary instance**, create a GlusterFS volume called `shared-volume` using the mounted EBS volumes. This volume will be replicated across both instances:
    
    ```bash
    sudo gluster volume create ebs-shared-vol replica 2 transport tcp <primary_instance_privateIP>:/home/ubuntu/data <secondary_instance_privateIP>:/home/ubuntu/data force
    Example:
    sudo gluster volume create ebs-shared-vol replica 2 transport tcp 172.31.24.63:/home/ubuntu/ebs-vol 172.31.17.20:/home/ubuntu/ebs-vol force
    
    ```
### 5.2 Start the GlusterFS Volume from Primary

-   Start the `shared-volume` shared volume:
    
    `sudo gluster volume start ebs-shared-vol`

    `sudo gluster volume status ebs-shared-vol`
    
## Step 6: Mount GlusterFS Volume Both Instances

### 6.1 Mount the GlusterFS Volume on Both Instances

-   Create a mount point directory (e.g., `/mnt/shared`):
    
    `sudo mkdir /mnt/shared`
    
-   Mount the `ebs-shared-vol` GlusterFS volume to this `/mnt/shared` directory:
    
    ```bash
    sudo mount -t glusterfs <primary_instance_privateIP>:/shared-volume /mnt/shared

    sudo mount -t glusterfs 10.0.1.227:/ebs-shared-vol /mnt/shared
    
    ```
-   Verify that the GlusterFS volume is mounted correctly by creating or updating a file in the `/mnt/shared` directory on either instance.
    
## Step 7: Auto-Mount EBS Volume on Reboot

### 7.1 Add to `/etc/fstab`

-   Edit `/etc/fstab` to automatically mount the EBS volume on reboot:
    
    `echo '/dev/nvme1n1 /home/ubuntu/ebs-vol xfs defaults 0 0' | sudo tee -a /etc/fstab`
    

### 7.2 
- Crate file from Primary Node mount point and check from secondary node mount point
- Create file from Secondary Node and check from Primary Node
- Now check from Secondary Node mount point

<p align="center">
  <img src="./ref-image/gfs-primary-node.png" alt="Primary Node" title="Primary Node" height="350" width="750"/>
  <br/>
  Pic: Primary Node
</p>

<p align="center">
  <img src="./ref-image/gfs-secondary-node.png" alt="Secondary Node" title="Secondary Node" height="350" width="750"/>
  <br/>
  Pic: Secondary Node
</p>

### 7.3 Monitoring GlusterFS Volume
- Check volume and brick log
    
    ```
    sudo tail -f /var/log/glusterfs/brick.log`
    sudo gluster volume status ebs-shared-vol
    ```

## Conclusion

You have successfully set up a clustered storage system using GlusterFS with shared EBS volumes in AWS. The shared storage is now accessible from both EC2 instances, and the GlusterFS volume ensures that data is synchronized between the two instances.


