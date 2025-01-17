# AWS EC2 Auto Scaling Setup Guide

## 1. VPC Setup

### Step 1: Create VPC
- **Name**: `prod-vpc`
- **CIDR Block**: `10.0.0.0/16`

### Step 2: Create Internet Gateway
- **Name**: `prod-vpc-igw`
- **Attach to VPC**: `prod-vpc`

### Step 3: Create Subnets
- **Subnet Name**: `prod-public-subnet-1a`
  - **CIDR Block**: `10.0.1.0/24`
- **Subnet Name**: `prod-public-subnet-1b`
  - **CIDR Block**: `10.0.2.0/24`

### Step 4: Create Route Table
- **Name**: `prod-vpc-public-rt`
- **Add Route**:
  - **Route**: `0.0.0.0/0`
  - **Target**: `prod-vpc-igw`
- **Associate Route Table** with `prod-public-subnet-1a` and `prod-public-subnet-1b`

---

## 2. EC2 Setup

### Step 1: Create Target Group
- **Name**: `prod-nginx-tg`
- **Configuration**: Keep default settings

### Step 2: Create Security Group
- **Name**: `prod-alb-sg` (for ALB)
- **Inbound Rules**:
  - **HTTP (Port 80)**
  - **HTTPS (Port 443)**
- **Select VPC**: `prod-vpc`

### Step 3: Create Application Load Balancer (ALB)
- **Name**: `prod-nginx-tg-alb`
- **Type**: Internet-facing
- **Select VPC**: `prod-vpc`
- **Select Subnets**: `prod-public-subnet-1a`, `prod-public-subnet-1b`
- **Attach Security Group**: `prod-alb-sg`
- **Configure Listeners and Routing**:
  - **Listener**: HTTP (Port 80)
  - **Routing**: Target Group: `prod-nginx-tg`

### Step 4: Create Launch Template
- **Inbound Rules**:
  - **SSH (Port 22)**
  - **HTTP (Port 80)**
  - **HTTPS (Port 443)**
- **Enable Auto-assign Public IP**
- **Subnet**: Do not select (Auto Scaling will handle it)

---

## 3. Auto Scaling Setup

### Step 1: Create Auto Scaling Group
- **Auto Scaling Group Name**: Choose an appropriate name
- **Choose Launch Template**: Select the launch template created earlier

### Step 2: Choose Instance Launch Options
- **Network**: Select VPC `prod-vpc`
- **Subnets**: Select both subnets `prod-public-subnet-1a` and `prod-public-subnet-1b`

### Step 3: Integrate with Other Services (Optional)
- **Load Balancing**: Select **Existing ALB** (`prod-nginx-tg-alb`) created earlier

### Step 4: Configure Group Size and Scaling
- **Group Size**: Set Min, Max, and Desired capacity
- **Scaling Policy**:
  - Select **Target Tracking Policy** (e.g., Average CPU Utilization)
  - **Target Value**: Set the desired target (e.g., 50% CPU utilization)
  - **Instance Warm-up**: Set the appropriate warm-up time (e.g., 300 seconds)

### Step 5: Add Notifications (Optional)
- Set up **SNS Topic** for notifications on scaling actions, instance launches, or terminations.

### Step 6: Add Tags (Optional)
- Add tags to your Auto Scaling group (e.g., `Environment=Production`).

### Step 7: Review
- Review all settings, and then create the Auto Scaling Group.


[Stree CPU](https://stackoverflow.com/questions/2925606/how-to-create-a-cpu-spike-with-a-bash-command)



