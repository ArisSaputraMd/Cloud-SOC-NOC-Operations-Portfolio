# Logging Baseline

![logs-collection](../../assets/diagrams/logs-collection-flow.png) \*_Figure 1: Preview Logs Collections flows_

---

The environment implements a multi-layer logging strategy to ensure visibility across infrastructure, network, and host activity.

## Infrastructure Logging

AWS CloudTrail logs all API activity across the account and streamsevents to CloudWatch Logs and S3 for retention and analysis.

## Network Logging

VPC Flow Logs capture network traffic metadata across all subnets and send logs to CloudWatch Logs.

## Web Layer Logging

AWS WAF logs blocked and allowed HTTP requests.
Application Load Balancer access logs record inbound traffic.

## Host Logging

EC2 instances run the Wazuh agent to collect system, authentication,
and integrity monitoring logs.

## Monitoring

CloudWatch provides centralized metrics, alarms, and log analysis
to support NOC and SOC operations.

What This Achieves (For Your Portfolio)

Your project now demonstrates real cloud security architecture:

Layer Tool Purpose
Cloud audit CloudTrail API activity
Network VPC Flow Logs traffic visibility
Web WAF + ALB logs application attack monitoring
Host Wazuh SOC detection
Monitoring CloudWatch NOC operations

1. Cloud Infrastructure Logging Baseline

These logs give visibility into who changed what in AWS.

AWS CloudTrail

Purpose:
Audit all API activity in your AWS environment.

Baseline configuration:

Enabled organization-wide or account-wide

Logging all management events

Logging write + read events

Delivery to S3 bucket

Integrated with CloudWatch Logs

Critical events captured:

IAM user creation

Security group modification

VPC changes

EC2 start/stop

Policy changes

Role assumptions

Example security detections:

Privilege escalation

Unauthorized IAM policy changes

New access keys created

Example baseline statement for your repo:

CloudTrail is enabled for all regions and configured to log management events.
Logs are delivered to S3 for long-term retention and streamed to CloudWatch Logs
for real-time monitoring and alerting. 2. Network / Edge Logging Baseline

This layer shows traffic behavior.

Amazon VPC Flow Logs

Purpose:
Capture network traffic metadata.

What it logs:

Source IP

Destination IP

Source port

Destination port

Protocol

Accept / Reject

Baseline configuration:

Enabled on VPC or Subnet level

Delivered to CloudWatch Logs

Example detections:

Port scanning

Unexpected outbound connections

Lateral movement attempts

Blocked traffic

Example baseline statement:

VPC Flow Logs are enabled on all private and public subnets.
Traffic metadata is delivered to CloudWatch Logs for network visibility
and anomaly detection. 3. Web / Edge Protection Logs

If you deployed WAF + ALB, you should log them.

AWS WAF

Purpose:
Detect and analyze application layer attacks.

Logs include:

SQL injection attempts

XSS attempts

IP reputation matches

Rate limit triggers

Baseline configuration:

WAF attached to ALB

Logging enabled to CloudWatch Logs

Example statement:

AWS WAF logging is enabled to capture blocked and allowed web requests.
Logs are streamed to CloudWatch Logs for threat analysis.
Elastic Load Balancing (ALB Access Logs)

Purpose:

Track every HTTP request reaching the application.

Logs include:

Client IP

URL requested

HTTP status code

Target response time

User agent

Baseline configuration:

Access logs enabled

Delivered to S3 bucket

Example statement:

Application Load Balancer access logging is enabled to provide full
visibility into inbound HTTP traffic and application usage patterns. 4. Host-Level Logging Baseline

This is where SOC monitoring becomes powerful.

Your EC2 instances should log:

System logs

Authentication attempts

sudo usage

service failures

kernel messages

Example files (Linux)
/var/log/auth.log
/var/log/syslog
/var/log/secure

These logs should be collected by:

Wazuh

Your Wazuh agent should collect:

authentication logs

process activity

file integrity monitoring

command execution

root access

Example baseline:

All EC2 instances run the Wazuh agent which collects system,
authentication, and integrity monitoring logs and forwards them
to the Wazuh manager for centralized security analysis. 5. Monitoring & Alerting Layer

Logs must feed a monitoring system.

Amazon CloudWatch

Used for:

metric monitoring

alarms

log ingestion

dashboards

Example:

CloudWatch alarms on:

EC2 CPU > threshold

instance health check failure

unusual API activity

6. Example Logging Baseline Section (For Your Repo)

You could add this in a file like:

/docs/logging-baseline.md
