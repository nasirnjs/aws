# **AWS Global Accelerator**

## **Overview**
AWS **Global Accelerator** is a **networking service** that improves the **performance and availability** of global applications by routing user traffic through **AWS’s global network infrastructure** instead of the public internet.

---

## **How It Works**
1. **Uses Anycast IPs**
   - AWS assigns **two static IP addresses** to your application.
   - These IPs remain the same even if backend resources change.

2. **Optimized Traffic Routing**
   - Routes user requests via **AWS’s global network** instead of the public internet.
   - Reduces latency and improves **reliability**.

3. **Health Checks & Failover**
   - Monitors **endpoint health**.
   - If an endpoint is **unhealthy**, traffic is automatically rerouted.

---

## **Key Features**
- ✅ **Global Traffic Acceleration**: Uses AWS's high-speed **backbone network**.
- ✅ **Static IP Addresses**: Unlike ALB/NLB, provides **two fixed IPs**.
- ✅ **Automatic Failover**: Redirects traffic to **healthy endpoints**.
- ✅ **DDoS Protection**: Integrated with **AWS Shield**.
- ✅ **Multi-Region Load Balancing**: Distributes traffic across **AWS Regions**.
- ✅ **Edge Location Routing**: Uses AWS **Edge locations** for better performance.

---

## **AWS Global Accelerator vs CloudFront**
| Feature              | AWS Global Accelerator | AWS CloudFront |
|----------------------|----------------------|---------------|
| **Purpose**         | Global traffic routing & failover | CDN for caching content |
| **Latency Reduction** | Optimized routing over AWS backbone | Caches content at Edge |
| **Static IPs**      | Yes | No |
| **Best For**        | Real-time apps, APIs, gaming, VPNs | Static content, video streaming |
| **Multi-Region Support** | Yes | No (Region-specific) |

---

## **Use Cases**
- 🚀 **Latency-sensitive Applications** → Gaming, SaaS, Video Conferencing  
- 🌍 **Multi-Region Apps** → Global APIs, Authentication Services  
- 📈 **High Availability Requirements** → Disaster Recovery, Multi-AZ Load Balancing  
- 🔒 **DDoS Protection** → Protection against attacks with AWS Shield  

---

## How to Set Up AWS Global Accelerator

### 1. Create a Global Accelerator
```sh
aws globalaccelerator create-accelerator \
    --name myAccelerator \
    --ip-address-type IPV4
```
### 2. Add Listeners
```
aws globalaccelerator create-listener \
    --accelerator-arn arn:aws:globalaccelerator::123456789012:accelerator/example-accel \
    --protocol TCP \
    --port-ranges FromPort=80,ToPort=80
```
### 3. Add Endpoints (EC2, ALB, NLB)
```
aws globalaccelerator create-endpoint-group \
    --listener-arn arn:aws:globalaccelerator::123456789012:listener/example-listener \
    --endpoint-group-region us-east-1 \
    --endpoint-configurations "EndpointId=arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/app/my-alb"
```