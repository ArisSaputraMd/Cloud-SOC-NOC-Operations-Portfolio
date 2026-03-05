## Phase 1 – Foundation Overview

Goal: Establish a secure, segmented AWS baseline suitable for NOC/SOC simulation while staying within free-tier limits.

What I completed:

- Hardened the AWS account (root protection + IAM user admin).
- Designed and deployed a production-style VPC with public/private segmentation

Trade-offs due to budget:

- Skipped CloudTrail S3 logging and full cost budgets for now (planned for Phase 2)
- Deferred advanced IAM roles until workloads are running

Lessons learned:

- .
