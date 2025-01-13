
AWS EC2 Auto Scaling steps by steps


Create VPC name prod-vpc 10.0.0.0/16
Create Internet Gateway prod-vpc-igw attached with vpc prod-vpc
Create Subnet under the prod-vpc name prod-public-subnet-1a 10.0.1.0/24
Create routetable  name prod-vpc-public-rt andassociate with IGW prod-vpc-igw
EC2 Section
create target group name prod-nginx-tg and keep everything is default
Create a Security group prod-ec2-tg-sg for ALB allow http and https select vpc prod-vpc
Create Application LoadBalancer prod-nginx-tg-alb select intenet facing vpc and subnetes, security group prod-ec2-tg-sg, Listernner and routing select prod-nginx-tg Target group
Create a lunch template, allow ssh, http and https enable autoassign public IP don't need to select subnet
create autoscalling group

 

subnet prod-public-subnet-1a




