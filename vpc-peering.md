

# AWS VPC Peering Setup - Step By Step

## Step 1: Create Two VPCs
1. Go to the **VPC Console** in AWS.
2. Click on **Create VPC**:
   - **VPC A**:
     - Name: `VPC-A`
     - CIDR Block: `10.0.0.0/16`
   - **VPC B**:
     - Name: `VPC-B`
     - CIDR Block: `10.1.0.0/16`
3. Ensure the CIDR ranges of the two VPCs do not overlap.

---

## Step 2: Create an Internet Gateway for Each VPC
1. For **VPC A**:
   - Go to **Internet Gateway** in the VPC Console.
   - Click on **Create Internet Gateway**.
   - Name it `IGW-A`.
   - Attach it to `VPC-A`.
2. For **VPC B**:
   - Repeat the steps to create and attach an Internet Gateway.
   - Name it `IGW-B`.
   - Attach it to `VPC-B`.

---

## Step 3: Create One Subnet in Each VPC
1. For **VPC A**:
   - Go to **Subnets** in the VPC Console.
   - Click **Create Subnet**.
   - Choose `VPC-A`.
   - Provide:
     - **Name**: `Subnet-A`
     - **CIDR Block**: `10.0.1.0/24`
     - **Availability Zone**: Choose any.
2. For **VPC B**:
   - Repeat the steps for `VPC-B`:
     - **Name**: `Subnet-B`
     - **CIDR Block**: `10.1.1.0/24`

---

## Step 4: Create Route Tables
1. For **VPC A**:
   - Go to **Route Tables** in the VPC Console.
   - Click **Create Route Table**.
   - Name it `RouteTable-A`.
   - Associate it with `VPC-A`.
2. For **VPC B**:
   - Repeat the steps for `VPC-B`:
     - Name it `RouteTable-B`.

---

## Step 5: Associate Subnets and Internet Gateways with Route Tables
1. **VPC A**:
   - Select `RouteTable-A`.
   - Add a route:
     - **Destination**: `0.0.0.0/0`
     - **Target**: `IGW-A`.
   - Associate `Subnet-A` with `RouteTable-A`.
2. **VPC B**:
   - Select `RouteTable-B`.
   - Add a route:
     - **Destination**: `0.0.0.0/0`
     - **Target**: `IGW-B`.
   - Associate `Subnet-B` with `RouteTable-B`.

---

## Step 6: Create a VPC Peering Connection
1. Go to **VPC Peering Connections** in the VPC Console.
2. Click **Create VPC Peering Connection**.
   - **Requester VPC**: `VPC-A`.
   - **Accepter VPC**: `VPC-B`.
   - Add a name, e.g., `VPC-A-to-VPC-B`.
3. Click **Create Peering Connection**.

---

## Step 7: Accept the VPC Peering Connection
1. Go to **VPC Peering Connections**.
2. Select the created peering connection.
3. Click **Actions > Accept Request**.

---

## Step 8: Modify Route Tables for VPC Peering
1. **For VPC A**:
   - Select `RouteTable-A`.
   - Add a route:
     - **Destination**: `10.1.0.0/16` (CIDR of VPC-B).
     - **Target**: Select the VPC Peering Connection.
2. **For VPC B**:
   - Select `RouteTable-B`.
   - Add a route:
     - **Destination**: `10.0.0.0/16` (CIDR of VPC-A).
     - **Target**: Select the VPC Peering Connection.

---

## Step 9: Test the Connectivity
1. Launch EC2 instances in `Subnet-A` and `Subnet-B`.
2. Assign public IPs or Elastic IPs to both instances.
3. Update **security groups** to allow ICMP traffic (ping) between the instances:
   - Inbound rule: Allow traffic from `10.0.0.0/16` for `VPC-A` and `10.1.0.0/16` for `VPC-B`.
4. Connect to one instance and ping the other instance's private IP.

---

## Outcome
- The two VPCs (`VPC-A` and `VPC-B`) are now peered and can communicate over their private CIDR blocks while maintaining independent internet gateways for external communication.
