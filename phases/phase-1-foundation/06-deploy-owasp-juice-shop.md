# Web Server Configuration and App Deployment

![deployment workflow](../../assets/screenshot/phase-1/06-user-flow-and-ci:di-pipeline.png) \*_Figure 1: Preview Diagram of User and Dev flow_

---

## Overview

**Nginx Reverse Proxy**
I used Nginx as a reverse proxy behind the public ALB to introduce an additional Layer 7 control point. This allows me to implement custom routing, enhance logging for SOC monitoring, and add an internal security layer before traffic reaches private services

**Juice Shop Intro**
OWASP Juice Shop is an open-source, intentionally vulnerable web application developed under the Open Worldwide Application Security Project. It serves as a flagship platform for security education, penetration testing practice, and tool benchmarking, incorporating the full spectrum of vulnerabilities from the OWASP Top Ten and beyond. Its realistic e-commerce interface and gamified challenges make it one of the most advanced training tools in web application security.

**Key facts**

- Initial release: 2014
- Lead developer: Björn Kimminich
- Programming stack: Node.js, Express, Angular, SQLite
- License: MIT
- Latest release: Version 19.1.1 (November 2025)

I've configured Nginx reverse proxy and deployed Juice Shop on my `app-asg` using docker container, this app will help me to simulate NOC operation and SOC monitoring simulation, which is perfect for this lab. This file is document of step-by-step how i deployed the app

---

## Nginx Reverse Proxy Configuration

To make the lab stable and realistic, the best approach is to configure Auto Scaling Group so that every new EC2 instance automatically install and start Nginx.
This is done using User Data in the Launch Template.

**Edit the Launch Template**
Modify the template (used by the web-asg) by adding the Startup Script:

## Juice App Deployment (Ubuntu)

Simillar To Nginx configuration on Web-asg, I've configured the launch template on App Auto Scaling Group so that every new EC2 instance automatically starts the container for OWASP Juice Shop.

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
_Figure 2: Preview App Juice startup script_

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

**Step 5 — Verify the Container**

Connect to `app-asg` instance using AWS Systems Manager.

Run:

```bash
sudo docker ps
```

You should see something like:
![docker running container](../../assets/screenshot/phase-1/06-docker-test.png)
_Figure 3: Console running container_

**Step 6 — Confirm Load Balancer Target Health**

Go to:

```
EC2
→ Target Groups
→ Targets
```

Your instance should become:

```
Healthy
```

because the container is serving traffic on:

```
port 80
```

---

## Manual deployment Vs Auto Script

| Manual deployment | Problem                         |
| ----------------- | ------------------------------- |
| Manual install    | lost when ASG replaces instance |
| npm build         | high memory usage               |
| long install time | slow scaling                    |

Manual deployment required us to install the app manualy to every single instace, which not suitable for ASG.

| Automation script    | Benefit          |
| -------------------- | ---------------- |
| Docker container     | lightweight      |
| User Data automation | consistent       |
| ASG replacement      | instant recovery |

By using docker container and Automation script, it will help to for easier scaling deployment, consistent configuration, and improve performence.
