# AWS EC2 Auto Scaling Setup Guide
An EC2 Auto Scaling Group (ASG) is a feature in AWS that automatically adjusts the number of EC2 instances in your application based on demand. Whether you need to scale up during peak traffic or scale down during off-peak times, the Auto Scaling Group ensures that you always have the right number of instances running. This helps you maintain application performance, availability, and cost efficiency without manual intervention.
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

### Step 2.1: Create Target Group
A Target Group ensures your Auto Scaling Group dynamically manages traffic distribution, health checks, and scaling, leading to a reliable, highly available application.
- **Name**: `prod-nginx-tg`
- **Configuration**: Keep default settings

### Step 2.2: Create 2 Security Group for ALB & EC2 Auto Scaling Group
- **Name**: `prod-alb-sg` (for ALB)
- **Inbound Rules**:
  - **HTTP (Port 80)**
  - **HTTPS (Port 443)**
- **Select VPC**: `prod-vpc`

- **Name**: `prod-asg-sg` (for ALB)
- **Inbound Rules**:
  - **SSH (Port 22)**
  - **HTTP (Port 80)**
  - **HTTPS (Port 443)**
- **Note**: You can allow trafic from only LAB Security Group for enhance security.

### Step 2.3: Create Application Load Balancer (ALB)
- **Name**: `prod-nginx-tg-alb`
- **Type**: Internet-facing
- **Select VPC**: `prod-vpc`
- **Select Subnets**: `prod-public-subnet-1a`, `prod-public-subnet-1b`
- **Attach Security Group**: `prod-alb-sg`
- **Configure Listeners and Routing**: `prod-asg-sg`
  - **Listener**: HTTP (Port 80)
  - **Routing**: Target Group: `prod-nginx-tg`

### Step 2.4: Create Launch Template
- **Name**: `ec2-auto-scal-launch-tem
- **Launch template contents**: Quick Start
- **Network settings**: Select your vpc `prod-vpc`
- **Subnet**: Do not select (Auto Scaling will handle it)
- **Select Security Group**:
- **Adjust Storage**: As your need 
- **Advanced network configuration**: Enable Auto-assign Public IP
- **Advanced details**: Add this script in the `User Data` section to dynamically identify the host.

```bash
#!/bin/bash
# Update and install Nginx
apt update -y
apt install -y nginx

# Start and enable Nginx service
systemctl start nginx
systemctl enable nginx

# Add the private IP address to the index.html file
echo "<h1>This message from: $(hostname -i)</h1>" > /var/www/html/index.html
```
---

## 3. Auto Scaling Setup

### Step 3.1: Create Auto Scaling Group
- **Auto Scaling Group Name**: `um-prod-auto-scaling-group`
- **Choose Launch Template**: Select the launch template created earlier

### Step 3.2: Choose Instance Launch Options
- **Network**: Select VPC `prod-vpc`
- **Subnets**: Select both subnets `prod-public-subnet-1a` and `prod-public-subnet-1b`

### Step 33.: Integrate with Other Services (Optional)
- **Load Balancing**: Select **Existing ALB** (`prod-nginx-tg-alb`) created earlier

### Step 3.4: Configure Group Size and Scaling
- **Group Size**: Set Min, Max, and Desired capacity
- **Scaling Policy**:
  - Select **Target Tracking Policy** (e.g., Average CPU Utilization)
  - **Target Value**: Set the desired target (e.g., 50% CPU utilization)
  - **Instance Warm-up**: Set the appropriate warm-up time (e.g., 300 seconds)

### Step 3.5: Add Notifications (Optional)
- Set up **SNS Topic** for notifications on scaling actions, instance launches, or terminations.

### Step 3.6: Add Tags (Optional)
- Add tags to your Auto Scaling group (e.g., `Environment=Production`).

### Step 3.7: Review
- Review all settings, and then create the Auto Scaling Group.

### Steo 3.8: Auto Scaling Group & performance testing

Connect to your EC2 instance and run the following command. The EC2 instances will be automatically provisioned in the Auto Scaling group.

Installs the stress tool for CPU, memory, and I/O load testing.\
`sudo apt install stress`

Runs a CPU stress test using 2 cores for 600 seconds.\
`stress --cpu 2 --timeout 600`


