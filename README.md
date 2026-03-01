## Cloud Security Operations Portfolio

#### Overview

This repository documents the design and implementation of a production-style Cloud Security Operations environment built in AWS.

> The goal of this project is to simulate real-world NOC and SOC operations within a cloud infrastructure, covering:
>
> - Secure cloud architecture design
> - Network monitoring and availability management
> - Centralized logging and log analysis
> - Threat detection and incident response
> - Detection engineering and security automation

This project is structured as a phased engineering program rather than a single lab, reflecting how modern cloud security operations are built and matured over time.

#### Project Objectives

- Design a secure and segmented cloud network architecture.
- Implement infrastructure following security best practices.
- Establish logging, monitoring, and alerting mechanisms.
- Simulate attack scenarios and document investigation workflow.
- Build detection use cases aligned with real SOC operations.

#### Architecture Overview

The environment simulates a small production cloud deployment:

- Public-facing web tier.
- Private application tier.
- Isolated database tier.
- Centralized logging and monitoring layer.
- Security detection and response layer.

> _Architecture diagrams and design decisions are documented in the _[/architecture](./architecture/)_ directory._

#### Project Structure.

```
cloud-security-operations-portfolio/
│
├── docs/
├── architecture/
├── assets/
└── phases/
    ├── phase-1-foundation/
    ├── phase-2-cloud-noc/
    ├── phase-3-soc-operations/
    └── phase-4-detection-engineering/
```

#### Phased Execution Plan

- Phase 1 – Foundation.
  - AWS account hardening.
  - IAM security baseline.
  - VPC design and network segmentation.
  - Logging baseline configuration.
  - Cost control and governance setup.

- Phase 2 – Cloud NOC Operations.
  - Availability monitoring.
  - Resource health dashboards.
  - Alerting configuration.
  - Uptime validation and service checks.

- Phase 3 – SOC Operations.
  - Centralized log aggregation.
  - Security event monitoring.
  - Incident simulation and response documentation. - Alert triage workflow.

- Phase 4 – Detection Engineering.
  - Custom detection use cases.
  - Log-based threat hunting.
  - Attack simulation and detection validation.
  - Continuous improvement of security controls

#### Target Roles

This portfolio is designed to demonstrate readiness for:

- Cloud NOC Analyst
- SOC Analyst (Tier 1 / Tier 2)
- Junior Cloud Security Engineer

#### Status

Phase 1 – [In Progress..](./phases/phase-1-foundation/)
