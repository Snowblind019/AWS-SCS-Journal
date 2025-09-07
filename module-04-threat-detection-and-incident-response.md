# Module 4 – Domain 1: Threat Detection and Incident Response

## Objective
The goal of this module was to understand how attackers can exploit **EC2 instance metadata** to steal temporary credentials and use them outside of AWS, and how to detect and mitigate such incidents without disrupting legitimate users. The focus was on simulating a **realistic credential compromise scenario**, identifying how the **Instance Metadata Service (IMDS)** can be abused, and implementing **mitigation strategies** using session revocation and reauthentication via instance lifecycle control.

---

## Hands-On Tasks Completed

- **Deployed a CloudFormation Stack on the Management Account**  
  - Used a predefined CloudFormation template to provision two EC2 instances for simulation and testing of credential exposure.

- **Simulated Credential Theft from EC2 Metadata Service**  
  - Connected to the EC2 instance.  
  - Queried the instance metadata endpoint using:  
    `curl http://169.254.169.254/latest/meta-data/iam/security-credentials/`  
  - Retrieved the IAM role name assigned to the EC2 instance.  
  - Queried the full temporary credentials using:  
    `curl http://169.254.169.254/latest/meta-data/iam/security-credentials/WinterRole-2R6KKQ5F3QU`

- **Exported Stolen Credentials on Local Machine**  
  - Set `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `AWS_SESSION_TOKEN` on a Windows local machine using `SET` commands.  
  - Used the AWS CLI to run:  
    - `aws s3 ls`  
    - `aws ec2 describe-instances --region us-east-1`  
  - Confirmed that the temporary credentials granted valid access.

- **Repeated Credential Abuse Simulation on EC2**  
  - Used AWS CLI on the EC2 instance to verify access with compromised credentials.  
  - Repeated metadata queries after stopping and restarting the instance to test for token rotation behavior.

- **Validated Real-Time Credential Leakage**  
  - Captured a live temporary credential JSON payload directly from the instance metadata endpoint, confirming how credentials can be harvested and reused externally if not protected.

- **Mitigated the Vulnerability via Session Revocation**  
  - Without modifying the IAM role or permissions (to avoid affecting legitimate users), executed a session revocation strategy.  
  - Revoked all active session tokens issued before a specific timestamp to invalidate the stolen credentials.  
  - Enforced re-authentication of the EC2 instance by stopping and starting it, which triggered issuance of fresh session credentials.

---

## What I Learned
- **Instance Metadata Service (IMDS) Exposure**  
  The IMDS is accessible from within EC2 instances and provides a wealth of sensitive data, including **temporary IAM credentials** for roles attached to the instance. If an attacker gains access to an EC2 instance (e.g., via SSRF, remote shell, or vulnerability exploitation), they can retrieve these credentials and use them from any location until expiration.

- **Temporary Credential Extraction**  
  Using basic commands (`curl`), it's possible to retrieve the role name and its associated credentials. These credentials consist of an access key, secret key, and session token, which can be exported and used with the AWS CLI to gain full access based on the IAM role's permissions.

- **Credential Reuse from External Machines**  
  Once extracted, these credentials can be reused from a local system to run AWS CLI commands such as listing S3 buckets or describing EC2 instances. This demonstrates how lateral movement or data exfiltration can happen entirely from outside the cloud environment, using legitimate session tokens.

- **IAM Role Permissions vs. Session Tokens**  
  The IAM role itself might be legitimate, and used by many services or customers. Therefore, deleting the role or removing its permissions can cause widespread impact. Instead, **revoking the session tokens** is a safer and more precise approach.

- **Session Revocation Strategy**  
  AWS allows **revocation of temporary credentials** by setting a specific timestamp. All tokens issued before that time become invalid. This is ideal in incident response scenarios, as it cuts off compromised credentials without affecting role configuration.

- **Force Reauthentication via EC2 Restart**  
  Since EC2 instances request new credentials from IMDS on boot, stopping and starting the instance causes a **fresh session to be created**, invalidating any previously issued credentials. This is a low-impact way to ensure that future sessions are secure.

---

## Real-World Application
- **Cloud Incident Response Readiness**:  
  This exercise simulates a common real-world scenario where attackers exploit misconfigured or vulnerable applications to access the IMDS and extract credentials. Understanding how to detect this and mitigate it quickly is essential for cloud security professionals.

- **Threat Modeling**:  
  Knowing how IMDS can be abused allows engineers to threat-model services that run on EC2. It also highlights the importance of using IMDSv2, disabling metadata where unnecessary, and segmenting workloads.

- **Damage Control Without Downtime**:  
  In production environments, revoking sessions instead of deleting IAM roles allows security teams to isolate the threat while maintaining operational continuity.

- **Security Automation**:  
  The lessons from this module can be translated into real-world detection automation using tools like AWS CloudTrail, GuardDuty, and Lambda responders to auto-revoke sessions if metadata access anomalies are detected.

---

## Personal Reflection
This was one of the most impactful modules so far. Simulating a credential theft from EC2 metadata gave me a real sense of how **dangerous simple misconfigurations can be** in the cloud. It reminded me that IAM roles—even temporary ones—can be abused just like long-term credentials if not properly secured.

The session revocation method stood out as an elegant and non-disruptive way to shut down compromised access. It taught me that **incident response isn’t always about removing things—it’s about precision**.

This experience has made me more aware of the need to monitor metadata access patterns and has strengthened my mindset around **proactive cloud defense** and **least privilege implementation**.
