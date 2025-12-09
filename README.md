<h1>OVERVIEW</h1>

This project implements a multi-AZ 3-tier architecture on AWS with an internet-facing Application Load Balancer fronted by AWS WAF, auto-scaled EC2 instances distributed across availability zones for the web and application tiers, and a **RDS** primary-replica cluster isolated in private subnets for the database tier. Traffic enters through **WAF**, is routed by the **ALB** to the **EC2** tier based on health checks, and database access remains restricted to the private network boundary. Full observability is enabled through **ALB access logging**, **connection logging** and **health check logging** to **S3**, **VPC Flow Logs** to **CloudWatch**, and custom **CloudWatch dashboards** tracking **EC2**, internet facing **ALB**, and **Aurora** performance metrics, ensuring visibility into traffic patterns, security events, and backend resource health.


<h1>3. KEY COMPONENTS</h1>

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

<h1>4. DEPLOYMENT TOPOLOGY</h1>

<h2>4.1 VPC & Subnets (Multi-AZ layout)</h2>
<img width="1326" height="380" alt="Screenshot 2025-12-09 134825" src="https://github.com/user-attachments/assets/f4222bf0-72ff-4f2f-bc46-7b1921348fe7" />
Shows the 3-tier VPC distribution across two availability zones (us-east-1a and us-east-1b), including dedicated public subnets for the web tier, private subnets for the application tier, and isolated private subnets for the database tier, with routing separated through distinct public and private route tables and controlled ingress via the Internet Gateway and NAT.

<h2>4.2 EC2 Auto Scaling Group</h2>
This shows independent Auto Scaling Groups for the web and application tiers, each configured with a desired capacity of 2 instances distributed across multiple availability zones. Instances are launched from dedicated templates and monitored by ALB health checks to ensure automatic replacement and uninterrupted service during node failures or scale events.
<img width="4464" height="3225" alt="Code Analysis (2)" src="https://github.com/user-attachments/assets/fdaebad3-eff1-47db-8d9d-c435d59ea8a1" />

<h2>4.3 ALB Configuration</h2>

<img width="6079" height="4591" alt="Code Analysis (1)" src="https://github.com/user-attachments/assets/93bbe7c1-de69-4aa5-a1da-954a9c16452c" />

Internet-facing ALB deployed across multiple availability zones, serving as the sole ingress path into the web tier. It performs health-based routing to EC2 instances and enforces edge-level security through attached AWS WAF policies before any traffic enters the VPC.

<h2>4.4 RDS Multi-AZ Configuration</h2>

RDS MySQL instance deployed in private subnets with Multi-AZ standby maintained by AWS for automatic failover. The database is not publicly accessible and only reachable from the application tier through tightly scoped security groups, ensuring isolation, controlled ingress, and fault tolerance at the data layer.
<img width="1357" height="495" alt="Screenshot 2025-12-09 135112" src="https://github.com/user-attachments/assets/1fd8a21b-7e29-42c2-8762-f2ad1f0dffb7" />

<h1>5. SECURITY CONTROLS</h1>

<h2>5.1 WAF Association with ALB</h2>
The configured Web ACL is actively attached to the internet-facing Application Load Balancer, ensuring all inbound traffic is inspected at the edge before reaching the web tier. This confirms enforcement of Core rule set, Known bad inputs, and IP reputation controls directly on the public entry point.
<img width="1365" height="414" alt="Screenshot 2025-12-09 135443" src="https://github.com/user-attachments/assets/7f2eafbd-2056-4a1a-b2b5-2ad0ebfa8d7e" />

<h2>5.2 WAF Blocked Requests / Rule Trigger</h2>
Dashboard confirms active inspection and rule execution at the edge, showing blocked versus allowed requests across the three enabled rule groups (Core rule set, Known bad inputs, and Amazon IP reputation). This validates that WAF is not only associated but successfully intercepting malicious traffic patterns and preventing invalid or attack-origin requests from reaching the web tier.
<img width="1365" height="501" alt="Screenshot 2025-12-09 135537" src="https://github.com/user-attachments/assets/ea3f9356-3b83-49d2-84f1-4b92ecfb4ab2" />

<img width="5061" height="3848" alt="Code Analysis (3)" src="https://github.com/user-attachments/assets/86a529c8-22bf-4559-a787-fdfc94da0810" />

<h2>5.3 WAF Runtime Validation (sqlmap Block Test)</h2>
<img width="690" height="131" alt="Screenshot 2025-12-09 124638" src="https://github.com/user-attachments/assets/636c7404-3c14-4298-9d48-4edc1c1c044b" />
A simulated attack request using the sqlmap/1.7 scanner signature was sent to the external ALB. WAF intercepted the request and returned 403 Forbidden, confirming active rule enforcement at the edge and preventing malicious payloads from reaching the web tier.

<h1>6. OBSERVABILITY & LOGGING</h1>

<h2>6.1 ALB Access Logs in S3</h2>

<h2>6.2 VPC Flow Logs Enabled</h2>

<h2>6.3 CloudWatch Dashboard</h2>

<h2></h2>


