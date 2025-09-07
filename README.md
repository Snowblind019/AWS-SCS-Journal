# AWS Certified Security Specialty (SCS-C02) Notes

**Welcome!** This is my personal lab journal as I work through the **AWS Certified Security Specialty (SCS-C02)** certification.

I’m currently following **Adrian Cantrill’s in-depth AWS SCS course**, and documenting the hands-on labs, configurations, and lessons I learn as I build secure, real-world AWS environments. This repo captures both my technical progress and my commitment to mastering cloud security through practical experience.

---

## Current Progress

### ✅ Module 1 – Course Fundamentals and AWS Account Setup

- Built the foundation for secure AWS operations by creating Management and Production accounts.
- Implemented IAM best practices, including MFA setup on root and admin users to reduce risk.
- Enabled IAM access to billing data, improving transparency and team-level visibility.
- Configured cost control measures: PDF invoices, Free Tier alerts, CloudWatch billing alarms, and $10/month budget notifications.
- Created IAM users with `AdministratorAccess` and switched daily operations away from root usage.
- Practiced secure access key lifecycle management for CLI-based workflows.
- Installed AWS CLI on Linux and configured named profiles for efficient multi-account access and automation.

---

### ✅ Module 2 – Management and Security Governance with AWS Organizations

- Established a centralized governance model by creating an AWS Organization from the Management account.
- Linked the Production account under the Organization and configured secure cross-account access using `OrganizationAccountAccessRole`.
- Practiced role switching via the AWS Console, enabling safe and efficient access to the Production and Development accounts from the Management account.
- Created and organized accounts into `PROD` and `DEV` Organizational Units (OUs) to support environment-level policy controls.
- Managed real-world service quotas by requesting an account creation limit increase through the AWS Service Quotas console.
- Tested Service Control Policies (SCPs) to enforce permission boundaries across OUs—explicitly denying S3 actions to demonstrate policy effectiveness.
- Gained experience with the permission evaluation model: IAM + SCP interaction, explicit denies, and the non-permissive nature of SCPs.

---

### ✅ Module 3 – Advanced Identity and Access Management in AWS

- Built secure delegated admin patterns using IAM permission boundaries and scoped policies.
- Enabled federated authentication via AWS Cognito and Google OAuth 2.0 to control access to protected S3 assets.
- Configured IAM Identity Center (SSO) with custom permission sets and user/group access across the Organization.
- Explored S3 access controls including presigned URLs, cross-account access, bucket policies, and object ownership behavior.
- Created EC2 environments to query and analyze instance metadata for automation and identity-aware configuration.
- Deployed CloudFormation templates to provision S3 and IAM resources and tested cross-account upload/delete behavior.

---

### 🔄 Module 4 – [To Be Documented]

- Notes and labs for this module will be added as I progress.

---

### 🔄 Module 5 – [To Be Documented]

- Notes and labs for this module will be added as I progress.

---

### 🔄 Module 6 – [To Be Documented]

- Notes and labs for this module will be added as I progress.

---

### 🔄 Module 7 – [To Be Documented]

- Notes and labs for this module will be added as I progress.

---

## About Me

From the start of my career, I’ve been fueled by curiosity, discipline, and a passion for learning. I earned my **AAS in Network Design and Administration by age 18**, started as a **NOC Analyst I**, and was promoted to **NOC Analyst II** within three months — driven by my adaptability and eagerness to grow.

I’ve gained real-world experience working with **Cisco, Juniper, and Nokia equipment**, supporting complex **MPLS**, **DWDM**, and **fiber optic** networks. Alongside this, I’ve been steadily expanding into cloud technologies and cybersecurity — laying a foundation through certifications like **CompTIA Network+, Security+, and CySA+**.

Now, I’m diving deeper into cloud security. I’m actively preparing for the **AWS Security Specialty** and **CCSP** certifications — building practical skills and a strategic mindset to become a strong **Cloud Security Engineer** who can design, defend, and evolve secure cloud environments.

---

## Connect with Me

- **LinkedIn:** [https://www.linkedin.com/in/emilp-profile/](https://www.linkedin.com/in/emilp-profile/)

---

**Thank you for visiting!** This repo is more than notes — it’s a reflection of my journey, mindset, and passion for secure cloud design.
