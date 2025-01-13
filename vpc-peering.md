# AWS VPC Peering Setup - Step by Step

## Step 1: Create Two VPCs
1. Navigate to the **VPC Console** in AWS.
2. Click on **Create VPC**:
   - **VPC A**:
     - **Name**: `staging-vpc-a`
     - **CIDR Block**: `10.0.0.0/16`
   - **VPC B**:
     - **Name**: `staging-vpc-b`
     - **CIDR Block**: `11.0.0.0/16`
3. Ensure the CIDR ranges for the two VPCs do not overlap.

---

## Step 2: Create an Internet Gateway for Each VPC
1. For **VPC A**:
   - Navigate to **Internet Gateway** in the VPC Console.
   - Click **Create Internet Gateway**.
   - **Name**: `staging-vpc-igw-a`
   - Attach it to `staging-vpc-a`.
2. For **VPC B**:
   - Repeat the steps to create and attach an Internet Gateway.
   - **Name**: `staging-vpc-igw-b`
   - Attach it to `staging-vpc-b`.

---

## Step 3: Create One Subnet in Each VPC
1. For **VPC A**:
   - Navigate to **Subnets** in the VPC Console.
   - Click **Create Subnet**.
   - Choose `staging-vpc-a`.
   - Provide the following details:
     - **Name**: `staging-vpc-a-subnet-1a`
     - **CIDR Block**: `10.0.1.0/24`
     - **Availability Zone**: Choose any.
2. For **VPC B**:
   - Repeat the steps for `staging-vpc-b`.
   - **Name**: `staging-vpc-b-subnet-1a`
   - **CIDR Block**: `11.0.2.0/24`
   - **Availability Zone**: Choose any.

---

## Step 4: Create Route Tables
1. For **VPC A**:
   - Navigate to **Route Tables** in the VPC Console.
   - Click **Create Route Table**.
   - **Name**: `staging-vpc-a-rt`
   - Associate it with `staging-vpc-a`.
2. For **VPC B**:
   - Repeat the steps for `staging-vpc-b`.
   - **Name**: `staging-vpc-b-rt`
   - Associate it with `staging-vpc-b`.

---

## Step 5: Associate Subnets and Internet Gateways with Route Tables
1. **For VPC A**:
   - Select `staging-vpc-a-rt`.
   - Add a route:
     - **Destination**: `0.0.0.0/0`
     - **Target**: `staging-vpc-igw-a`.
   - Associate Subnet `staging-vpc-a-subnet-1a`.
2. **For VPC B**:
   - Select `staging-vpc-b-rt`.
   - Add a route:
     - **Destination**: `0.0.0.0/0`
     - **Target**: `staging-vpc-igw-b`.
   - Associate Subnet `staging-vpc-b-subnet-1a`.

---

## Step 6: Create a VPC Peering Connection
1. Navigate to **VPC Peering Connections** in the VPC Console.
2. Click **Create VPC Peering Connection**.
   - **Name**: `staging-vpc-a-to-staging-vpc-b`
   - **Requester VPC**: `staging-vpc-a`
   - **Accepter VPC**: `staging-vpc-b`
3. Click **Create Peering Connection**.

---

## Step 7: Accept the VPC Peering Connection
1. Navigate to **VPC Peering Connections**.
2. Select the created peering connection.
3. Click **Actions > Accept Request**.

---

## Step 8: Modify Route Tables for VPC Peering
1. **For VPC A**:
   - Select `staging-vpc-a-rt`.
   - Add a route:
     - **Destination**: `11.0.0.0/16` (CIDR of `staging-vpc-b`)
     - **Target**: Select the VPC Peering Connection.
2. **For VPC B**:
   - Select `staging-vpc-b-rt`.
   - Add a route:
     - **Destination**: `10.0.0.0/16` (CIDR of `staging-vpc-a`)
     - **Target**: Select the VPC Peering Connection.

---

## Step 9: Test the Connectivity
1. Launch EC2 instances in `staging-vpc-a-subnet-1a` and `staging-vpc-b-subnet-1a`.
2. Assign public IPs or Elastic IPs to both instances.
3. Update **security groups** to allow traffic for SSH and HTTP.
4. Use **User Data** to install and configure NGINX:
   - Launch an instance with NGINX installed.
5. Access the NGINX web server using the private IP of the other instance.
