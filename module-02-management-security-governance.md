# Module 2 – Domain 6: Management and Security Governance

## Objective
This module focused on building a centralized governance model in AWS using **AWS Organizations**, **Organizational Units (OUs)**, **cross-account IAM roles**, and **Service Control Policies (SCPs)**. The goal was to establish multi-account structure and governance controls that mirror enterprise cloud environments, enabling consistent management, secure role delegation, and enforced compliance boundaries across environments such as Production and Development.

---

## Hands-On Tasks Completed

- **Created an AWS Organization**  
  Promoted the Management account to serve as the root of a new AWS Organization.

- **Invited and Linked the Production Account**  
  Sent an invitation to the Production account using its AWS account ID.  
  Logged into the Production account and accepted the invite to join the Organization under the Management account.

- **Set Up Cross-Account Role Access**  
  In the Production account:  
  - Created an IAM Role named `OrganizationAccountAccessRole` with `AdministratorAccess`.  
  - Specified the Management account as a trusted entity, enabling role assumption.  

- **Switched Role from Management Account**  
  Used the Switch Role feature to assume the `OrganizationAccountAccessRole` in the Production account from the Management account.  
  - Labeled the role as `PROD` with a red color tag for easy identification.  
  - Switched into the role and confirmed access, then returned to the original session.

- **Attempted to Create a New Account (Development)**  
  Attempted to create a new member account named `Development`.  
  - Encountered a quota error: *"You have exceeded the allowed number of AWS accounts."*  
  - Navigated to the **Service Quotas** console, searched for `AWS Organizations`, located the `Default maximum number of accounts`, and submitted a request to AWS Support for a quota increase.

- **Set Up Role Switching for Development Account**  
  After account creation, configured a switch role entry for the `Development` account in the Management account console.  
  - Named the profile `DEV`.

- **Created Organizational Units (OUs)**  
  - Created two OUs: `PROD` and `DEV`.  
  - Moved the Production account into the `PROD` OU.  
  - Moved the Development account into the `DEV` OU.

- **Validated Role Permissions with S3**  
  - Switched into the `PROD` role.  
  - Created an S3 bucket, uploaded an image, and accessed it to verify full admin privileges via the `OrganizationAccountAccessRole`.

- **Tested Service Control Policies (SCPs)**  
  - Created a new SCP allowing all actions except an explicit deny on S3.  
  - Attached the SCP to the `PROD` OU.  
  - Detached the default `FullAWSAccess` SCP to enforce the restriction.  
  - Verified that the `PROD` role could no longer access S3.  
  - Reattached `FullAWSAccess` and detached the deny-S3 SCP to restore access.  
  - Emptied and deleted the previously created S3 bucket to complete cleanup.

---

## What I Learned
- **AWS Organizations**:  
  Learned how to create and manage a centralized organization using the Management account. The Organization acts as a master control plane from which you can invite or create accounts, structure them into OUs, and apply policies uniformly.

- **Cross-Account IAM Role Delegation**:  
  By creating roles like `OrganizationAccountAccessRole` and trusting the Management account, I understood how administrators can securely assume roles in member accounts without using the root user or creating IAM users in every account. Role switching is a critical method for secure, scalable access management.

- **Switch Role Utility**:  
  Using the AWS Console’s "Switch Role" functionality allowed seamless access to member accounts with visual distinctions (e.g., named “PROD” with color red), improving both UX and administrative efficiency.

- **Service Quotas**:  
  Encountered a real-world limitation while attempting to create a new account and learned how to use the **Service Quotas** console to view and request quota increases. This taught me the practical boundaries and soft limits imposed on Organizations and how to resolve them.

- **Organizational Units (OUs)**:  
  OUs help segment accounts by function, environment, or business unit. Structuring accounts into OUs like `PROD` and `DEV` allows policy-based governance and operational separation.

- **Service Control Policies (SCPs)**:  
  Discovered the power of SCPs as an overarching permissions boundary that limits even root or admin access within accounts. Learned how to:
  - Apply SCPs at the OU level.
  - Use **explicit deny** to block S3 while allowing all other services.
  - Detach and reattach SCPs to test the impact on permissions.
  - Understand that SCPs do not grant permissions but **limit them**.

- **Policy Evaluation**:  
  This module clarified how permission evaluation works in AWS when IAM policies, permission boundaries, and SCPs intersect. Even with `AdministratorAccess`, an SCP with an explicit deny will override everything.

---

## Real-World Application
- **Centralized Account Governance**:  
  AWS Organizations are widely used in large enterprises to separate workloads and reduce blast radius. This module reflects a foundational setup for managing security, compliance, and cost at scale.

- **Secure Admin Delegation**:  
  Cross-account role assumption is the standard for secure access—this replaces the old practice of managing IAM users in every account. It also supports logging, session duration control, and minimal credential sprawl.

- **Quotas and Scaling Limits**:  
  Understanding and navigating service quotas is essential in cloud operations. It prevents rollout disruptions and helps with forward planning during growth or automation.

- **Use of SCPs in Compliance**:  
  SCPs are used in real companies to enforce guardrails, such as prohibiting use of unapproved regions, disallowing certain services (e.g., S3 in dev environments), or enforcing encryption requirements.

---

## Personal Reflection
This module helped me move beyond basic IAM and into **governance-level architecture**. Setting up an AWS Organization and learning how to govern multiple accounts through SCPs and role delegation felt like stepping into real-world cloud operations.

Seeing how a role in the Management account could securely assume full access in another account without needing extra users was eye-opening. It emphasized the **principle of least privilege** while maintaining operational control.

I especially appreciated the hands-on with SCPs—learning how a simple `deny` can shut down entire services regardless of admin access was powerful. It highlighted the importance of planning policies carefully and understanding the broader permission evaluation model.

Overall, this module made me appreciate how AWS provides both **flexibility and control** when it comes to multi-account environments, and it gave me the confidence to implement similar structures in a production context.
