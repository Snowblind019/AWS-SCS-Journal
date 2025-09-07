# Module 3 - Domain 4: Identity and Access Management

## Objective
This module focused on mastering advanced IAM (Identity and Access Management) techniques within AWS. The core objective was to understand and apply **permission boundaries**, **delegated administration**, **federated identity using Cognito**, and **fine-grained S3 access control**. It also introduced **IAM Identity Center (formerly AWS SSO)** for organization-wide access governance, and provided practical exposure to **EC2 Instance Metadata**, which is vital for understanding context-aware access and automation.

---

## Hands-On Tasks Completed

- **Created IAM Permission Boundary (`winteruserboundary`)**  
  Wrote a JSON policy limiting user access to only `s3`, `ec2`, and `cloudwatch` services, and allowing users to manage their own credentials (e.g., passwords, access keys, certificates). This boundary was designed to be applied to all users created by a delegated admin.

- **Created IAM Admin Delegation Boundary (`winteradminboundary`)**  
  Built a JSON policy allowing delegated users to manage other users *only* if the users they manage have the `winteruserboundary` attached. This boundary:  
  - Blocks editing/deleting the boundary policies themselves.  
  - Prevents the deletion of a user's permission boundary.  
  - Excludes the delegated admin (`Snowy`) from acting on themselves.

- **Created Admin Permission Policy (`winterdminpermissionpolicy`)**  
  Granted full `iam:*` permissions and limited `cloudwatch` read permissions for delegated admin users.

- **Created IAM User `Snowy`**  
  - Enabled console access with a password.  
  - Attached the `winteradminpermissionpolicy`.  
  - Applied `winteradminboundary` as the permission boundary, limiting Snowy's actual capabilities despite broad permissions.

- **Deployed CloudFormation Template (`WEBIDF`)**  
  Launched a stack that created:  
  - Two S3 buckets (one private, one public with images).  
  - A CloudFront distribution: `d1r78yms5ewf7x.cloudfront.net`.

- **Configured Google OAuth Integration**  
  - Created a Google project `SNOWIDF`.  
  - Set up an OAuth consent screen for external users.  
  - Registered a web app client (`SNOWIDFServerlessApp`) with the CloudFront URI as the authorized origin.  
  - Retrieved the client ID for use in Cognito.

- **Configured Cognito Identity Pool for Google Federation**  
  - Created a Cognito Identity Pool with Google as the federated IdP.  
  - Assigned the retrieved Google Client ID.  
  - Created the role `SNOWIDFIDPoolAuth_Role` and attached permissions to access the private S3 bucket.

- **Updated and Uploaded Frontend Files**  
  - Edited `index.htm` and `scripts.js` to include the proper IDs.  
  - Uploaded the updated files to the public S3 bucket.

- **Tested Google SSO via CloudFront**  
  - Accessed the CloudFront URL.  
  - Logged in with Google SSO and successfully retrieved images from the private S3 bucket.

- **Cleaned Up Federated Identity Resources**  
  - Deleted the S3 buckets, CloudFront distribution, and Cognito configuration used in the test.

- **Enabled IAM Identity Center (AWS SSO)**  
  - Customized the portal URL: `https://notwinterday2331.awsapps.com/start`.  
  - Created permission sets: `AdminAccess`, `PowerUserAccess`, `ViewOnlyAccess`, `Billing` (all with 4-hour sessions).  
  - Created user `Blizzardy Winter` and added her to the `Billing` group with Organization-wide access.  
  - Verified her access was restricted to Billing.  
  - Added `PowerUserAccess` to Blizzardy via gorup membership and verified expanded access.  
  - Created and deleted a Dropbox application and removed Blizzardy’s config.

- **Created IAM User `iamadmin` and Admin Group**  
  - Created IAM user `iamadmin` and group `WINTER-ADMINS`.  
  - Assigned `AdminAccess` permission set.  
  - Logged in and configured MFA.

- **Tested Presigned URL with S3**  
  - Created S3 bucket `winterdaymedia2331` and uploaded an image.  
  - Used AWS CloudShell to generate a presigned URL:  
    `$ aws s3 presign s3://winterdaymedia2331/all5.jpg --expires-in 180`  
  - Created an inline deny policy for `s3:*` on `iamadmin` to block S3 access.  
  - Verified loss of access, then removed the deny policy and successfully accessed the image.  
  - Deleted the bucket and contents.

- **Deployed CloudFormation Stack to Production**  
  - Created a stack that generated multiple S3 buckets and an IAM role.  
  - Used an **ACL** to allow the Management account to upload a picture.  
  - Verified the Production account could not access the uploaded image.  

- **Tested Bucket Policy and Ownership Controls**  
  - Replaced the ACL approach with a bucket policy that explicitly allowed access to the `iamadmin` user.  
  - Still experienced access issues until setting **Object Ownership** to `Bucket Owner Preferred`.  
  - Updated policy to `s3:*` to allow object deletion by the Management account.  

- **Created Cross-Account Role in Production**  
  - Created a role using the Production account ID.  
  - Switched into this role from the Management account.  
  - Repeated the previous S3 access test and confirmed proper permissions from the Production context only.
- **Deployed EC2 Metadata Environment via Template**  
  - Connected to the EC2 instance via public IPv4.  
  - Ran metadata-related commands to explore instance metadata:
    - `curl http://169.254.169.254/latest/meta-data/public-ipv4`  
    - `curl http://169.254.169.254/latest/meta-data/public-hostname`  
    - Installed `ec2-metadata` tool and ran:  
      - `ec2-metadata --help`  
      - `ec2-metadata -a`  
      - `ec2-metadata -z`  
      - `ec2-metadata -s`

---

## What I Learned
- **IAM Permission Boundaries:**  
  Gained a deep understanding of permission boundaries as a powerful mechanism to limit the maximum permissions a user or role can have—even if their policies grant more. This ensures safer delegation and prevents privilege escalation.

- **Delegated Administration:**  
  Learned how to enforce boundaries at scale by building custom policies that allow admins to manage users only if they apply a specific permission boundary. This is an enterprise-grade pattern to distribute administrative duties without sacrificing security.

- **Federated Authentication via Cognito and Google:**  
  Integrated Google OAuth 2.0 with AWS Cognito to enable external users to authenticate into a web app and gain temporary AWS credentials. This process involved creating identity pools, roles, and IAM policies to restrict access to protected resources.

- **IAM Identity Center (SSO):**  
  Enabled organization-wide single sign-on via IAM Identity Center. Learned how to configure permission sets (Admin, PowerUser, Billing, ViewOnly), create custom access portals, and assign users and groups across the AWS Organization with time-limited session access.

- **S3 Access Models and Ownership:**  
  Discovered the nuances between ACLs, bucket policies, and object ownership settings like `Bucket Owner Preferred`. Understood how these affect cross-account uploads, deletions, and permission resolution.

- **Presigned URLs:**  
  Explored how presigned URLs are generated and used to securely grant temporary access to private S3 objects without making the bucket public.

- **EC2 Metadata Service:**  
  Worked with the EC2 metadata endpoint and tooling to fetch instance-specific data like IP addresses, hostname, and region info. This highlighted how services can use instance metadata for identity, configuration, and automation—and the importance of securing this data against abuse.

---

## Real-World Application
- **Enterprise IAM Governance:**  
  Permission boundaries and delegated roles are frequently used in large organizations to limit the scope of IAM admins. The patterns I implemented here mirror what is needed in production environments to safely scale identity management.

- **Cross-Platform Identity Federation:**  
  Most modern applications require support for SSO via Google, Azure AD, or Okta. Setting up Cognito with Google OAuth is directly transferable to real projects that require customer or partner logins.

- **Organizational SSO:**  
  IAM Identity Center simplifies the management of access across hundreds of AWS accounts. The approach I practiced with permission sets and custom portals aligns with best practices for centralized access control.

- **Secure Object Sharing with S3:**  
  Understanding how to share or restrict access across accounts is critical for teams working in collaborative environments or implementing multi-account architectures. Presigned URLs, ownership settings, and policies are essential tools for these workflows.

- **Instance Metadata Awareness:**  
  Cloud security teams and DevOps engineers use metadata for dynamic configuration. This module’s hands-on work is directly relevant to detecting SSRF vulnerabilities, building secure startup scripts, and performing contextual access audits.

---

## Personal Reflection
This module challenged me to think like a security architect. Building secure IAM policies is one thing—but designing **safe delegation with permission boundaries** showed me what real-world IAM governance looks like at scale. I especially enjoyed integrating Google OAuth with Cognito, as it helped me understand how AWS bridges identity from external providers into its ecosystem.

IAM Identity Center was also eye-opening. Seeing how access can be assigned centrally across multiple AWS accounts using permission sets gave me a vision of how enterprise security can be both scalable and maintainable.

The most frustrating—but also most educational—part was S3 access between accounts. Learning how ACLs, bucket policies, and ownership clash (or complement) was messy but rewarding. These are the kinds of low-level details that separate theoretical knowledge from practical cloud security.

This module helped me develop a security-first mindset for IAM, and reinforced the reality that in the cloud, **identity is the control plane**.
