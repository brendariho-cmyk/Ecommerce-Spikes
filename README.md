
---

# **Scalable E-Commerce Web Application on AWS**

A highly available, fault-tolerant, and scalable e-commerce web application architecture built using AWS cloud services. This project demonstrates VPC-level network design, load balancing, autoscaling, database tiering, and monitoring suitable for handling real-world traffic spikes.

---

## **1. Business Problem: Handling Traffic Spikes in E-Commerce**

E-commerce platforms experience unpredictable and sometimes extreme traffic surges—especially during sales, promotions, holidays, and product launches. If the backend infrastructure cannot scale automatically, the site becomes slow, unresponsive, or completely unavailable, leading to poor user experience and revenue loss.

---

## **2. Solution Overview**

This project implements a **scalable multi-tier web application architecture** on AWS, designed to automatically adjust capacity based on real-time demand. Using a custom VPC, public/private subnets, an Application Load Balancer, Auto Scaling Group with EC2 instances, a private RDS PostgreSQL database, and CloudWatch monitoring, the system ensures high availability, tight security boundaries, and efficient resource utilization for e-commerce workloads.

---

## **3. Architecture Diagram**

*(Embed your PNG from your repo folder)*

```
![Architecture Diagram](architecture-diagram/final-diagram.png)
```

---

## **4. Components**

### **Network Layer**

* Custom VPC (10.0.0.0/16)
* Public Subnet (10.0.1.0/24)
* Private Subnet (10.0.2.0/24)
* Internet Gateway
* Public & Private Route Tables

### **Compute Layer**

* EC2 Instance (On-Demand)
* Launch Template
* Auto Scaling Group
* Application Load Balancer

### **Database Layer**

* Amazon RDS PostgreSQL
* Private Subnet Placement
* Security Group Controls

### **Monitoring Layer**

* CloudWatch Metrics
* CloudWatch Alarms

---

## **5. Network Design**

### **Custom VPC**

* CIDR: **10.0.0.0/16**
* Segmented into public and private subnets for strict separation of concerns.

### **Subnets**

* **Public Subnet (10.0.1.0/24)**

  * Hosts ALB and EC2 Instances
  * Internet-facing resources

* **Private Subnet (10.0.2.0/24)**

  * Hosts RDS PostgreSQL
  * No direct internet access

### **Route Tables**

* **Public Route Table:**

  * `0.0.0.0/0 → Internet Gateway`
  * Enables inbound/outbound traffic for ALB and EC2

* **Private Route Table:**

  * Local routing only
  * Ensures database isolation

---

## **6. Security**

### **Security Groups**

#### **ALB Security Group**

* Inbound: 80/443 from Anywhere (0.0.0.0/0)
* Outbound: To EC2 on port 80

#### **EC2 Security Group**

* Inbound: 80 from ALB only
* Outbound: 5432 to RDS only

#### **RDS Security Group**

* Inbound: 5432 from EC2 only
* Outbound: Local VPC

### **Rationale**

* ALB exposed to the internet for user access
* EC2 instances only accessible through ALB (zero public exposure)
* RDS only accessible from EC2 (full database isolation)

---

## **7. Autoscaling & Load Balancing**

### **Application Load Balancer**

* Distributes traffic across EC2 instances
* Increases fault tolerance
* Listener: HTTP (80)

### **Auto Scaling Group**

* Uses Launch Template with On-Demand instance
* Policies:

  * **Scale Out:** CPU > 70%
  * **Scale In:** CPU < 30%
* Ensures enough capacity during traffic spikes
* Reduces cost during low demand

---

## **8. Database Design**

### **Amazon RDS PostgreSQL**

* Private subnet deployment
* Security Group restricts access to EC2 only
* Automated Backups enabled (default 7 days)
* Multi-AZ not enabled in free tier but recommended for production

### **Database Access**

* Not publicly accessible
* Access via EC2 bastion environment only

---

## **9. Monitoring**

### **CloudWatch Metrics**

* CPUUtilization
* NetworkIn / NetworkOut
* ALB RequestCount
* HealthyHostCount

### **CloudWatch Alarms**

* **High CPU Alarm:** Triggers ASG scale-out
* **Low CPU Alarm:** Triggers ASG scale-in
* Email/SNS optional

---

## **10. Cost Considerations & Free Tier Notes**

* EC2 On-Demand: Free tier covers 750hrs/month (t2.micro or t3.micro depending on region)
* ALB is **not free tier** but was used for learning purposes
* RDS PostgreSQL: Free tier 750hrs/month + 20GB storage
* CloudWatch basic metrics are free
* EIP charged only when not attached
* Architecture designed with cost awareness; production optimizations recommended

---

## **11. Testing Performed**

### **1. EC2 + ALB Test**

* Access ALB DNS name
* Verified load balancer routes requests correctly

### **2. Auto Scaling Test**

* Manually increased CPU load
* Observed ASG scale-out event

### **3. RDS Connection Test**

* Verified EC2-to-RDS access via internal VPC
* Confirmed database connectivity and security boundaries

### **4. CloudWatch Alarm Trigger Test**

* CPU artificially increased
* Alarm transitioned from `OK → ALARM`

*(Screenshots stored in `/screenshots/` folder in the repo)*

---

## **12. What I Would Improve (Future Work)**

To evolve this into a production-grade system:

* Add **CloudFront CDN** for caching and global performance
* Add **S3 bucket** for static assets (images, CSS, JS)
* Add **AWS WAF** for DDoS protection at ALB
* Enable **Multi-AZ RDS** for high availability
* Add **CI/CD Pipeline** using GitHub Actions + CodeDeploy
* Use **Bastion Host or SSM** for secure EC2/RDS access
* Add **Private ALB + Public CDN architecture** for additional isolation
  
(Optional) Add Terraform to fully automate provisioning.

---

## **13. How to Reproduce This Architecture**

### **Checklist**

1. Create Custom VPC
2. Add Public and Private subnets
3. Attach Internet Gateway
4. Configure route tables
5. Create Security Groups
6. Launch Template for EC2
7. Create Auto Scaling Group
8. Deploy ALB in two public subnets
9. Deploy RDS PostgreSQL in private subnet
10. Configure CloudWatch metrics & alarms
11. Test ALB, ASG, and RDS connectivity

---

## **14. Contact**

**Author:** Brenda Ariho
**GitHub:** *brendaariho-cmyk*
**Email:** [brenda.ariho.d@gmail.com](mailto:brenda.ariho.d@gmail.com)

---





