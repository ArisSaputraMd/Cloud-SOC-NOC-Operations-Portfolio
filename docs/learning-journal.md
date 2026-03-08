<!--learning-journal

Problems hit

Misconfigurations

Cost mistakes

Security lessons

Architecture redesign decisions
-->

## Learning Journal

---

### Architecture Design

![architecture](../assets/diagrams/initial-architecture-diagram.png) \*_Figure 1: Initial design preview – showing overall early concept._

At the beginning, I planned to design this lab to be as "pocket-friendly" and simple as possible. However, I soon realized that this approach was too far from a real production environment, which directly contradicted with the core goals of the project itself.

For that reason, I've created `architecture-design-2.0`.

![architecture-design-2.0](../assets/diagrams/vpc-design.png) \*_Figure 2: Current design preview – architecture-design-2.0_

**Key improvements:**

- Two Availability Zones for high availability and fault tolerance.
- Public Subnet
  - No server instances placed directly in public subnets (internet-facing traffic now handled via Application Load Balancer).
  - AWS WAF attached to ALB as first-line web application protection (L7 filtering).
- Private Subnet
  - 3-tier private subnet structure for defense-in-depth
  - Separate dedicated Monitoring subnet

Even though it is not yet exactly the same as a full production environment (e.g., multiple VPCs, multiple AWS accounts, cross-region replication, etc.), the current architecture has significantly improved security posture and fault tolerance.

This redesign better aligns with real-world cloud security operations practices and gives me stronger material to discuss in interviews — particularly around defense-in-depth, least privilege, high availability trade-offs, and iterative architecture improvement.
