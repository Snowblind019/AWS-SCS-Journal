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

### ✅ Module 4 – Threat Detection via EC2 Metadata Exploitation and Mitigation

- Deployed CloudFormation template simulating EC2 credential exposure scenarios.
- Queried EC2 instance metadata to extract IAM temporary credentials.
- Exported credentials to a local Windows machine and validated unauthorized AWS access.
- Simulated multiple credential reuse attempts post-instance restart and observed token behavior.
- Demonstrated how real-time metadata exposure can lead to credential theft.
- Executed session revocation to invalidate compromised temporary tokens without disrupting valid role usage.
- Restarted EC2 instance to force new session token issuance, ensuring fresh secure credentials.

---

### ✅ Module 5 – Infrastructure Security (VPC, VPN, Endpoints, Storage)

- Manually created custom VPC (`winter-vpc1`) with IPv4/IPv6 addressing and 12 subnets.
- Configured Internet Gateway, route tables, and tested EC2 instance connectivity.
- Rebuilt VPC using CloudFormation for automation and consistency.
- Created 3 NAT Gateways with corresponding private route tables and fixed subnet associations.
- Launched pfSense+ VPN setup:
  - Deployed pfSense via CloudFormation.
  - Configured IPsec tunnels and verified bi-directional connectivity.
  - Validated security groups and route table configurations using Fleet Manager.
- Built and tested VPC endpoints:
  - EC2 Instance Connect (interface) and S3 Gateway endpoints.
  - Verified S3 access through endpoint with endpoint-restricted policy (`aws:sourceVpce`).
- Created VPC peering mesh (A↔B, B↔C, A↔C):
  - Updated routing tables and SGs for full mesh ICMP communication.
- Performed EBS test:
  - Mounted, rebooted, detached, reattached, and snapshotted a gp3 volume.
  - Migrated to another AZ and validated data persistence.
- Tested EC2 Instance Store:
  - Wrote to ephemeral volume, verified data persisted after reboot but lost after stop/start.

---

### ✅ Module 6 – Security Logging and Monitoring

- Created S3 buckets (`winterday-source`, `winterday-processed`) and IAM role `PixelatorRole` with S3 + CloudWatch permissions.
- Built a local Python Lambda function using Pillow to pixelate images and deployed it as `winterday`.
  - Set up trigger on `ObjectCreated` event in source bucket.
  - Processed images and verified output in destination bucket and CloudWatch logs.
- Deployed EC2 instance via CloudFormation (`Winter-Wordpress`) and installed CloudWatch Agent.
  - Collected metrics (CPU, memory, disk, network) and logs from `/var/log/secure`, `httpd/access_log`, `httpd/error_log`.
  - Stored configuration in Systems Manager Parameter Store (`AmazonCloudWatch-linux`) for reusability.
- Verified CWAgent metrics namespace and log ingestion in CloudWatch.
- Created organization-wide CloudTrail (`SnowyWinter`):
  - Enabled Management Events only.
  - Streamed logs to S3 and CloudWatch log group using IAM role.
- Used Amazon Athena:
  - Set up `Winter` database and queried OpenStreetMap dataset from `osm-pds`.
  - Filtered veterinary facility data using SQL.
  - Cleaned up tables and database after analysis.
- Enabled Amazon Macie:
  - Created and scanned S3 bucket (`ep-mixed-data-2331`) with sensitive mock data.
  - Created discovery job `Winterland` using all managed identifiers.
  - Verified detection of credit card numbers, credentials, and license plates.
- Integrated Macie with SNS and EventBridge:
  - Created SNS topic `Macie-Alerts` and confirmed email subscription.
  - Built EventBridge rule `macie-events` for alert forwarding.
  - Created custom identifier (`LicensePlate`) using regex.
  - Ran second discovery job (`LicensePlates`) to detect additional sensitive data.
- Cleaned up all Macie jobs, S3 buckets, EventBridge rules, and SNS topics.

---

### ✅ Module 7 – Data Protection

- Explored multiple encryption mechanisms for Amazon S3 by uploading files with SSE-S3, SSE-KMS using the default `aws/s3` key, and SSE-KMS using a custom KMS key (`WinterKey`).
- Demonstrated access control behavior by applying an IAM policy to deny `kms:*` actions for the `iamadmin` user, confirming the inability to access SSE-KMS encrypted objects.
- Verified how S3 default encryption settings affect uploads and tested behavior with and without KMS access.
- Practiced symmetric encryption and decryption using AWS KMS via CLI:
  - Created `WinterBot` KMS key.
  - Encrypted and decrypted local text files using KMS commands within CloudShell.
- Validated the confidentiality and integrity of KMS-encrypted data by comparing encrypted vs. decrypted file contents.
- Deployed a test environment using CloudFormation to simulate Application Load Balancer (ALB) behavior across six EC2 instances.
- Enabled ALB session stickiness (1 minute) and verified consistent routing to a single instance.
- Simulated failover behavior by stopping and restarting backend EC2 instances and observing ALB’s stickiness behavior.
- Disabled stickiness to observe default round-robin behavior across all EC2 targets.
- Fully cleaned up all test resources including buckets, keys, stacks, and instances after validation.

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
