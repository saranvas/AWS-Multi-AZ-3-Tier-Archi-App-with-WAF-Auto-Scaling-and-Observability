<h1>OVERVIEW</h1>

This project implements a multi-AZ 3-tier architecture on AWS with an internet-facing Application Load Balancer fronted by AWS WAF, auto-scaled EC2 instances distributed across availability zones for the web and application tiers, and a **RDS** primary-replica cluster isolated in private subnets for the database tier. Traffic enters through **WAF**, is routed by the **ALB** to the **EC2** tier based on health checks, and database access remains restricted to the private network boundary. Full observability is enabled through **ALB access logging**, **connection logging** and **health check logging** to **S3**, **VPC Flow Logs** to **CloudWatch**, and custom **CloudWatch dashboards** tracking **EC2**, internet facing **ALB**, and **Aurora** performance metrics, ensuring visibility into traffic patterns, security events, and backend resource health.


<h1>3 KEY COMPONENTS</h1>

- **VPC & Subnets (Public / Private, Multi-AZ)**
Multi-AZ VPC layout with distinct public subnets for ingress via ALB and private subnets for application and database tiers. All compute and database resources remain isolated from direct internet exposure, ensuring controlled ingress only through the load balancer.

- **ALB + WAF**
Internet-facing Application Load Balancer integrated with AWS WAF to inspect and filter inbound traffic before it reaches the web tier. Core rule sets, known bad input filters, and IP reputation controls block malicious payloads and scanning attempts, enforcing security at the edge.

- **Web/App EC2 + Auto Scaling**
EC2 instances distributed across multiple availability zones within an Auto Scaling Group. Health-checked by ALB, instances scale out based on demand and replace unhealthy nodes automatically, maintaining application uptime without manual intervention.

- **RDS Primary/Standby**
RDS is deployed in private subnets with a primary instance and Multi-AZ standby replica managed by AWS for failover. The database is not publicly accessible and only accepts traffic from the application tier through tightly scoped security groups, ensuring isolation and controlled access at the data layer.

- **Logging & Monitoring (Flow Logs, ALB Logs, CloudWatch Dashboard)**
Operational visibility is enabled through ALB access logs stored in S3, VPC Flow Logs streamed to CloudWatch for network-level insight, and centralized CloudWatch dashboards tracking EC2 performance, Aurora health metrics, and ALB request behavior for real-time and post-incident analysis.

<h1>4 DEPLOYMENT TOPOLOGY</h1>

<h2>4.1 VPC & Subnets (Multi-AZ layout)</h2>
<img width="1326" height="380" alt="Screenshot 2025-12-09 134825" src="https://github.com/user-attachments/assets/f4222bf0-72ff-4f2f-bc46-7b1921348fe7" />
Shows the 3-tier VPC distribution across two availability zones (us-east-1a and us-east-1b), including dedicated public subnets for the web tier, private subnets for the application tier, and isolated private subnets for the database tier, with routing separated through distinct public and private route tables and controlled ingress via the Internet Gateway and NAT.

<h2>4.2 EC2 Auto Scaling Group</h2>
This shows independent Auto Scaling Groups for the web and application tiers, each configured with a desired capacity of 2 instances distributed across multiple availability zones. Instances are launched from dedicated templates and monitored by ALB health checks to ensure automatic replacement and uninterrupted service during node failures or scale events.
<img width="4464" height="3225" alt="Code Analysis (2)" src="https://github.com/user-attachments/assets/fdaebad3-eff1-47db-8d9d-c435d59ea8a1" />

<h2>4.3 ALB Configuration</h2>

<h2>4.4 Target Group Health Status</h2>

<h2>4.5 RDS Multi-AZ Configuration</h2>

<h1>5. SECURITY CONTROLS</h1>

<h2>5.1 WAF Association with ALB</h2>

<h2>5.2 WAF Blocked Requests / Rule Trigger</h2>

<h1>6. OBSERVABILITY & LOGGING</h1>

<h2>6.1 ALB Access Logs in S3</h2>

<h2>6.2 VPC Flow Logs Enabled</h2>

<h2>6.3 CloudWatch Dashboard</h2>

<h2></h2>


