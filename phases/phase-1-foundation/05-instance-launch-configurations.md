# EC2 Instances, Auto Scaling & Workload Configuration

---

## Overview

Deployed multi-tier application (web → app → DB) with monitoring in isolated subnets.  
Used Graviton (t4g) instances for cost savings (~20–40% vs x86).  
Auto Scaling configured for web & app tiers (min 1, desired 2, max 4 per AZ — adjust based on load).

![EC2 list overview screenshot](../../screenshots/phase-1/ec2-instances-tagged-tiered.png)
_Figure X: Console view of all instances with tags, types, subnets_

## Application Load Balancer + WAF

**Application Load Balancer**

| Attribute      | Value                               |
| -------------- | ----------------------------------- |
| Tag-Name       | app-load-balancer                   |
| IP type        | IPv4                                |
| AZ and Subnet  | ap-southeast-3a/public-a            |
|                | ap-southeast-3b/public-b            |
| Security Group | app-load-balancer-sg                |
| Listener       | HTTPS/443 (forward to target group) |
|                | HTTP/80 (redirect to URL)           |
| Target-Group   | tg-web-servers                      |

- Listener: HTTP/80 → redirect to HTTPS/443
- Target group: tg-web-servers
- WAF associated for OWASP rules & rate limiting
- I leave WAF config to default Auto-create, pre-defined WAF
  Includes 3 managed rules for protections against: - The most common vulnerabilities found in web applications. - Malicious actors discovering application vulnerabilities. - Potential threats based on Amazon internal threat intelligence.

![ALB listeners & WAF](../../screenshots/phase-1/alb-waf-association.png)

## Web Tier Instances

| Attribute      | Value                            |
| -------------- | -------------------------------- |
| Tag Name       | ec2-web-server-a                 |
| IPv4v          | 10.0.2.10                        |
| Instance Type  | t4g.small, 2vCPU, 2GB Memory     |
| AMI            | Ubuntu server 22.04 LTS(HVM)     |
| Architecture   | 64-bit(ARM)                      |
| Key Pair       | -                                |
| Storage        | 1 volum(s) - 8GiB (Auto Scaling) |
| Security Group | sg-web-server                    |
| IAM role       | EC2-Web-Server                   |

- Auto Scaling Group: web-asg (across private-web-a/b)
- Launch template: Ubuntu 22.04 ARM, t4g.small/micro mix for HA/cost

## App Tier Instances

(Your app A/B tables here)

## Database Tier

(Your primary/replica tables here)

- Manual replication (or note future RDS migration)
- Separate EBS volumes for data (25 GiB)

## Monitoring / SIEM Instance

(Your ec2-security-monitoring table here)

- Larger instance (r6i.large) for Wazuh + Kibana + log processing
- Agents on all other instances sending logs via TCP 1514/1515

## Cost & Sizing Rationale

- t4g family: Better price/performance on ARM
- Mixed small/micro: Simulate production HA without over-provisioning
- Monitoring oversized for demo → right-size in production
- Estimated EC2 cost: ~$50–80/mo running 24/7 → Use instance scheduler / stop when idle

Future improvements: Migrate DB to RDS Multi-AZ, add proper ASG scaling policies (CPU-based), add CloudWatch agent on all instances.
