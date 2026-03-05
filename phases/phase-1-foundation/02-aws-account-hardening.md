## AWS Account Hardening

![aws-account-hardening.png](../../assets/img/Account-hardening.png)

In Phase 1, I applied multiple layers of security controls to protect my AWS account credentials and prevent unauthorized access. This follows current AWS best practices (2026) and aligns with the Well-Architected Security Pillar.

AWS supports several identity types:

- **Root user** — The account owner with unrestricted access (use only when absolutely required).
- **IAM Identity Center users** — Recommended for workforce/human access.
- **Federated principals** — Temporary access via external identity providers (e.g., Okta, Entra ID).
- **IAM users** — Legacy/direct users (avoid where possible; use sparingly).

### Key Best Practices I Followed

1. **Created an Administrator User (Do NOT use root daily)**  
   To minimize risk, I avoid daily operations with the root user. Instead:
   - Created IAM administrator user.
   - Created a permission set with administrative access (e.g., AdministratorAccess).
   - Created a group/user and assigned the permission set.
   - Sign in exclusively via Identity Center for all administrative tasks — this provides temporary credentials and centralized management.

   ![it-team.png](../../assets/screenshot/phase-1/iam-admin-user.png)

2. **Secured the all Account**
   - Following NIST password security guidelines, (convenience with long passphrases) i have set password requirement with minimum 16 characters, and not set mandatory for complexity. (recommended to use password manager for hases and salting)

   ![pawwsord-policy.png](../../assets/screenshot/phase-1/password-policy.png)
   - Enabled multi-factor authentication (MFA) immediately (multiple devices for resiliency): - Preferred: Passkey or FIDO security key (phishing-resistant and strongly recommended by AWS). - Acceptable fallback: Hardware TOTP token or authenticator app (e.g., Authy).

   ![mfa-authentication](../../assets/screenshot/phase-1/mfa-authentication.png)
   - **Never created or used access keys** for the root user — deleted any if present.
   - Use root only for rare tasks (e.g., account closure, enabling Organizations).

3. **Avoided Long-Term Access Keys**  
   Long-term access keys (access key ID + secret) do not expire automatically and are a common breach vector.
   - Preferred approach: Use temporary credentials:
     - IAM roles for workloads/EC2/Lambda/etc.
     - IAM Identity Center for human users (federated SSO).
   - If long-term keys are unavoidable (rare legacy cases only):
     - Rotate regularly (e.g., every 90 days).
     - Apply least privilege.
     - Monitor usage with IAM last accessed information.
     - Remove unused keys promptly.

4. **Additional Controls**
   - Used IAM Access Analyzer to identify unused permissions and refine policies.
   - Applied least privilege everywhere (started with AWS managed policies, then customized).
   - Planned for future: Organization-level controls (e.g., SCPs) once using AWS Organizations (post-core phases to preserve free tier).

**Outcome & Skills Demonstrated**  
This hardening significantly reduces credential compromise risk and provides a secure foundation for NOC/SOC simulation in later phases. It shows my understanding of modern identity management — critical for Cloud Security Engineer, SOC Analyst, or NOC roles.
