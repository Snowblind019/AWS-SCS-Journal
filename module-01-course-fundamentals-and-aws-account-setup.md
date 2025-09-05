# Module 1 – Course Fundamentals and AWS Accounts

## Objective
The goal of this module was to lay the foundation for secure, organized, and efficient AWS account management. This involved creating separate AWS accounts for **Management** and **Production**, enforcing **identity best practices** like MFA and IAM user usage, setting up **budgeting and billing alerts**, and configuring the **AWS CLI** for command-line access using named profiles. The objective was to establish a security-first mindset and familiarize myself with account-level configurations that are essential in both personal and enterprise environments.

---

## Hands-On Tasks Completed

- **Set Up the Management AWS Root Account**  
  - Created the Management account.  
  - Enabled access to billing information for IAM users.  
  - Configured MFA using an authenticator app.  
  - Set billing preferences to receive PDF invoices, Free Tier usage alerts, and CloudWatch billing alerts.  
  - Created a monthly budget of $10 with email notifications.

- **Set Up the Production AWS Root Account**  
  - Created the Production account.  
  - Repeated all the same steps as in the Management account: enabled billing access, configured MFA, updated billing preferences, and created a $10 monthly budget with alerts.

- **Created IAM Users and Aliases**  
  - On the Management account:  
    - Created an account alias.  
    - Created an IAM user named `iamadmin` with the `AdministratorAccess` policy.  
    - Logged in as `iamadmin` to avoid using the root account.  
    - Enabled MFA on `iamadmin` using an authenticator app.  
  - On the Production account:  
    - Created an account alias.  
    - Created an IAM user with the `AdministratorAccess` policy.  
    - Enabled MFA on that IAM user.

- **Security Credentials for CLI Access**  
  - On the Management account:  
    - Created two access keys for CLI use.  
    - Tested by deactivating, reactivating, and deleting the keys.  
    - Recreated a working access key for actual use.  
  - On the Production account:  
    - Created access keys for CLI use.

- **Configured AWS CLI on Linux**  
  - Installed the AWS CLI on a Linux system.  
  - Created named CLI profiles for both the Management and Production accounts using the access keys.  
  - Verified each profile to ensure CLI commands worked against the correct account context.

---

## What I Learned
- **Root Accounts Should Be Used Sparingly**  
  The root user in each AWS account should be used only for initial configuration and critical actions. Instead, IAM users with defined roles should be used for daily operations to reduce risk.

- **IAM User Access to Billing**  
  By default, billing data is only accessible to the root user. Granting IAM users and roles permission to view billing ensures that administrators can monitor costs and usage proactively.

- **MFA (Multi-Factor Authentication)**  
  Setting up MFA via an authenticator app significantly increases security. MFA should be enabled on both root and IAM accounts to mitigate risks related to compromised credentials.

- **Account Aliases for Better UX**  
  Creating account aliases helps avoid confusion, especially when switching between multiple AWS accounts. This becomes critical in multi-account organizations.

- **Security Credentials Lifecycle**  
  Gained hands-on experience with generating, deactivating, and deleting IAM access keys. Understanding their lifecycle is critical when rotating credentials or troubleshooting access issues.

- **Budgets and Alerts**  
  AWS Budgets and CloudWatch billing alerts help set financial boundaries and prevent surprise charges—especially important in learning, testing, or sandbox environments.

- **Named CLI Profiles**  
  Using named profiles with the AWS CLI allows seamless switching between accounts in a single terminal session. This is highly useful for engineers or administrators working across environments.

---

## Real-World Application
- **Multi-Account Structure**:  
  Creating separate AWS accounts for different environments (e.g., Management vs. Production) is an industry best practice. It supports better isolation, security, and billing clarity.

- **Secure Access Management**:  
  Implementing MFA, minimizing root account usage, and managing IAM credentials reflect how organizations reduce their attack surface and enforce accountability.

- **Cost Control**:  
  Small misconfigurations can lead to large bills in cloud environments. Budgeting tools and alerts are key to staying within financial limits, especially in sandboxed or experimental accounts.

- **CLI-Based Operations**:  
  Most cloud engineers work via the CLI. Setting up named profiles mirrors real-world DevOps practices, enabling automation, scripting, and secure access without hardcoding credentials.

---

## Personal Reflection
This module was a strong reminder that **security and governance begin on day one**. While some of the tasks may seem basic—like setting up billing alerts or enabling MFA—they’re foundational steps that protect against serious issues later on.  

I appreciated how much AWS encourages a **principle of least privilege** mindset through IAM, and I now understand how easy it is to inadvertently overspend without proper alerts in place. The budgeting and CLI profile setup felt especially empowering, since they tie into how real professionals operate in multi-account, multi-environment scenarios.

This initial setup laid the groundwork for all future AWS learning. It gave me the confidence to treat my environment like a real production setup, even at this early stage.
