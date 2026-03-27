# Web Server Configuration and App Deployment

![deployment workflow](../../assets/screenshot/phase-1/06-user-flow-and-ci:di-pipeline.png) \*_Figure 1: High-level user and developer flow through the environment._

---

## Overview

This section documents the deployment of a secure, scalable web tier using Nginx as a reverse proxy and the OWASP Juice Shop as the backend application.

**Nginx Reverse Proxy**
I placed Nginx behind the internet-facing Application Load Balancer (ALB) to add a dedicated Layer 7 control point. This provides:

- Fine-grained request routing and header manipulation
- Enhanced visibility for SOC monitoring (access logs, custom headers)
- An additional security layer (hiding backend details, basic hardening)
- Better separation between public exposure and internal services

**Juice Shop Intro**
[OWASP Juice Shop](https://owasp.org/www-project-juice-shop/) is an intentionally vulnerable web application designed for security training, awareness, and red/blue team exercises. It contains realistic vulnerabilities aligned with the OWASP Top 10, making it ideal for:

- Simulating attack scenarios
- Validating detection rules
- Practicing incident response and log analysis in a controlled SOC environment

Key facts

- Initial release: 2014
- Lead developer: Björn Kimminich
- Programming stack: Node.js, Express, Angular, SQLite
- License: MIT
- Latest release: Version 19.2.1 (March 2026)

The application runs in a Docker container on the `app-asg` Auto Scaling Group, enabling stateless, repeatable, and auto-healing deployments perfect for demonstrating production-like operations in this lab.

---

## Nginx Reverse Proxy Configuration

Traffic from the Public ALB is forwarded to Nginx instances, which then proxy requests to the Private (internal) ALB. This design keeps backend routing flexible and supports future microservices expansion.

To ensure consistency in an Auto Scaling environment, all configuration is handled via User Data in the Launch Template for the `web-asg`.

**Launch Template Configuration**

Add the Startup Script for the Launch template (User Data) used by the `web-asg`.

User Data:

```
#!/bin/bash

# Update system & install Nginx
apt update -y
apt install -y nginx

# Remove default config
rm -f /etc/nginx/sites-enabled/default

# Create custom Nginx reverse proxy config
cat <<EOF > /etc/nginx/sites-available/reverse-proxy
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;

    # Health check endpoint (for ALB)
    location = /health {
        access_log off;
        add_header Content-Type text/plain;
        return 200 'OK';
    }

    # Reverse proxy to internal ALB
    location / {
        proxy_pass http://internal-inner-load-balancer-2121773739.ap-southeast-3.elb.amazonaws.com;

        # Preserve original request details
        proxy_set_header Host \$host;
        proxy_set_header X-Real-IP \$remote_addr;
        proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto \$scheme;

        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;

        # Basic security hardening
        proxy_hide_header X-Powered-By;
    }
}
EOF

# Enable config
ln -sf /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/default

# Test config before restart (IMPORTANT)
nginx -t

# Restart Nginx
systemctl restart nginx
systemctl enable nginx
```

**Test the Nginx Reverse Proxy**

![Nginx test](../../assets/screenshot/phase-1/06-nginx-reverse-proxy-test.png)
_\*Figure 2: Nginx service status, reverse proxy test, and health endpoint validation.._

- `systemctl status nginx` → Active (running)
- `curl -I http://localhost` → HTTP/1.1 200 OK
- `curl http://localhost/health` → OK

---

## Juice App Deployment (Ubuntu)

Similar to the Nginx configuration on the web ASG, I configured the launch template for the application Auto Scaling Group. The container is exposed on port 80 and registered with the internal ALB target group, allowing seamless load balancing across application instances.

This way:

```
ASG launches EC2
      |
User Data script runs automatically
      |
Docker installs
      |
Juice Shop container starts
```

No manual installation is needed.

**Step 1 — Edit the Launch Template**

Go to:

```
EC2
-> Launch Templates
-> Select the template (used by the app-asg)
-> Actions
-> Modify template
-> Advanced details
```

Find the User Data section.

**Step 2 — Add the Startup Script**

![app juice script](../../assets/screenshot/phase-1/06-app-juicy-script-template.png)
_Figure 3: User Data script for automated Docker + Juice Shop deployment_

What this script does:

| Step                     | Action            |
| ------------------------ | ----------------- |
| Update packages          | prepares system   |
| Install Docker           | container runtime |
| Start Docker             | enable service    |
| Run Juice Shop container | launch app        |

The flag: `--restart always` ensures the container restarts automatically if the server reboots.

**Step 3 — Save New Template Version**

After editing:

```
Create new template version
```

Then update the Auto Scaling Group to use the new version.

**Step 4 — Refresh the Instances**

Trigger instance replacement.

Options:

```
ASG → Instance Refresh
```

or terminate one instance manually.

When ASG launches a new instance:

```
EC2 starts
User Data script runs
Docker installs
Juice Shop container launches
```

**Step 5 — Verifications**

1. Check running container (via AWS SSM Session Manager):

   ```bash
   ~$ sudo docker ps
   ```

   ![docker running container](../../assets/screenshot/phase-1/06-docker-test.png)
   _Figure 4: Juice Shop container running and mapped to port 80_

2. Confirm target group health in the AWS Console (EC2 → Target Groups → Targets).

---

## End-to-End Testing

Additionally, I verified end-to-end connectivity by sending requests through the public ALB DNS, confirming that traffic successfully flows through WAF → ALB → Nginx → Private ALB → Application.

**Console Test**

From local machine:

![end-to-end test](../../assets/screenshot/phase-1/06-end-to-end-test.png)
_\*Figure 5: `curl -I -L` against the public domain shows successful routing with Nginx headers_

**Browser Test (real user simulation)**

![brwoser test](../../assets/screenshot/phase-1/06-browser-test.png)
_\*Figure 6: OWASP Juice Shop homepage loaded successfully through the full stack._

---

## Manual vs Automated Deployment Comparison

| Aspect          | Manual Deployment            | Automated (User Data + Docker)       | Benefit for Production / SOC Lab           |
| --------------- | ---------------------------- | ------------------------------------ | ------------------------------------------ |
| Consistency     | High risk of drift           | Identical on every instance          | Reliable monitoring & detection baselines  |
| Scalability     | Slow and error-prone         | Instant with ASG instance refresh    | Supports auto-healing and horizontal scale |
| Recovery        | Manual intervention required | Automatic via --restart always + ASG | Better availability (NOC perspective)      |
| Security        | Exposed install steps        | Immutable infrastructure pattern     | Reduced attack surface                     |
| Maintainability | Hard to reproduce            | Version-controlled Launch Templates  | Easier auditing and documentation          |
