# Module 6 – Domain 2: Security Logging and Monitoring

## Objective
The goal of this module was to implement security logging, monitoring, alerting, and detection services in AWS. I focused on building visibility into infrastructure and application behavior using tools like CloudWatch, CloudTrail, Athena, and Amazon Macie. This included real-time and historical analysis using logs and metrics, automated event detection, and scalable alerting. The objective was to gain hands-on experience with configuring agents, triggering Lambda functions, scanning for sensitive data, and integrating alerts across multiple AWS services to support continuous monitoring and compliance workflows.

---

## Hands-On Tasks Completed

- **Created S3 Buckets and IAM Role for Lambda**
  - Created two S3 buckets: `winterday-source` and `winterday-processed`.
  - Created IAM role `PixelatorRole` for Lambda with an inline policy `pixelator_access_inline`:
    - Granted full access to both S3 buckets.
    - Allowed necessary CloudWatch Logs actions for Lambda execution.

- **Developed and Deployed Local Lambda Function**
  - Built folder structure under: `~/Downloads/my_lambda_deployment/lambda`.
  - Created `lambda_functions.py` using the Pillow imaging library:
    - Read image from source bucket.
    - Pixelated at multiple resolutions (8x8 through 64x64).
    - Uploaded modified images to the destination bucket.
  - Zipped and deployed to a Lambda function named `winterday`:
    - Runtime: Python 3.9  
    - Architecture: x86_64  
    - Set environment variable `processed_bucket = winterday-processed`.  
    - Increased timeout to 1 minute.  
    - Triggered on `ObjectCreated` events from `winterday-source`.
  - Verified execution in CloudWatch Logs (~2.5 seconds) and presence of processed images.

- **Deployed CloudWatch Agent via CloudFormation and EC2**
  - Launched CloudFormation stack named `CWAGENT` with EC2 instance `Winter-Wordpress`.
  - Installed CloudWatch Agent:
    ```bash
    sudo dnf install amazon-cloudwatch-agent
    ```
  - Created IAM role `CloudWatch-Role` with:
    - `CloudWatchAgentServerPolicy`
    - `AmazonSSMFullAccess`
  - Attached the role to the instance.
  - Ran CloudWatch Agent configuration wizard:
    - Enabled metrics: CPU, memory, disk, network.
    - Enabled CollectD and logs from:
      - `/var/log/secure`
      - `/var/log/httpd/access_log`
      - `/var/log/httpd/error_log`
    - Set interval: 60 seconds.
    - Set retention: Unlimited.
  - Stored configuration in Systems Manager Parameter Store under `AmazonCloudWatch-linux`.
  - Fixed agent launch by manually creating missing CollectD directories.
  - Started agent using:
    ```bash
    sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
    -a fetch-config \
    -m ec2 \
    -c ssm:AmazonCloudWatch-linux \
    -s
    ```

- **Verified CloudWatch Setup**
  - Verified metrics published under `CWAgent` namespace.
  - Confirmed logs appeared in CloudWatch.
  - Cleaned up all EC2 and related resources.

- **Configured AWS CloudTrail Organizational Trail**
  - Created organization-wide trail named `SnowyWinter`.
  - Created S3 bucket `cloudtrail-winterday2231` with log file validation enabled.
  - Streamed logs to:
    - CloudWatch Log Group: `aws-cloudtrail-logs-731733337976-f7cc1dc4`
    - IAM Role: `CloudTrailRoleForCloudWatchLogs_Winter`
  - Enabled only Management Events (Read/Write).
  - Verified logs in S3 and CloudWatch after ~15 minutes.
  - Stopped logging to avoid charges.

  - Created S3 bucket: `winter-query-results-2331` as Athena query results location.
  - Created Athena database: `Winter`
  - Created table: `planet` using public dataset from `osm-pds` S3 bucket.
  - Validated schema with:
    ```sql
    SELECT * FROM planet LIMIT 100;
    ```

  - Ran geolocation filter query to find veterinary facilities by coordinates.
  - Cleaned up with:
    ```sql
    DROP TABLE planet;
    DROP DATABASE Winter;
    ```

- **Set Up Amazon Macie and Ran Discovery Jobs**

  - Created S3 bucket: `ep-mixed-data-2331`.
  - Uploaded mock data:
    - `cc.txt` (credit card numbers)
    - `employees.txt` (employee data)
    - `keys.txt` (AWS credentials)
    - `plates.txt` (license plates)
    - One image file
  - Enabled Macie.
  - Created discovery job `Winterland` using all managed identifiers.
  - Verified sensitive data detection after ~20 minutes.

- **Integrated Macie Alerts with SNS**
  - Created SNS topic: `Macie-Alerts`.
  - Subscribed via email and confirmed subscription.

- **Integrated EventBridge for Automated Alerts**
  - Created EventBridge rule `macie-events` to forward findings to SNS.
  - Created Customer Managed Identifier `LicensePlate` using a regex for license plate formats.
  - Created second discovery job `LicensePlates` using custom identifier.
  - Verified successful detection of additional patterns.

- **Cleaned Up All Resources**
  - Deleted:
    - Macie discovery jobs
    - S3 buckets
    - EventBridge rules
    - SNS topics

---

## What I Learned

- **CloudWatch Agent Configuration**  
  I learned how to configure detailed OS-level metrics and logs using the CloudWatch Agent Wizard, including storing configurations in Parameter Store for repeatable use across EC2 instances.

- **SSM Parameter Store & Reusability**  
  I now understand how to persist configuration data in SSM and retrieve it dynamically during agent startup. This allows scalable and consistent observability setups.

- **CloudTrail Monitoring**  
  I gained insight into enabling organization-wide trails, validating log file integrity, and streaming logs to CloudWatch for real-time correlation.

- **EventBridge and SNS for Alerting**  
  I saw how EventBridge rules can trigger automated alerting workflows through SNS, reinforcing rapid incident detection.

- **Amazon Macie for Sensitive Data Detection**  
  Macie’s ability to detect sensitive data via both managed and custom identifiers showed its power in automated compliance enforcement and audit readiness.

- **Athena for Serverless Querying**  
  I practiced SQL-based data analysis on S3 data without infrastructure. Athena proved useful for both exploration and security auditing.

---

## Real-World Application

- **Centralized Monitoring & Observability**  
  Using CloudWatch with SSM-configured agents mirrors how enterprises monitor health and logs across server fleets.

- **Alerting and Incident Response**  
  EventBridge and SNS form real-world pipelines for alerting on security events, helping teams respond faster.

- **Compliance and Data Security**  
  Macie is used in regulated environments to detect and prevent unauthorized data exposure.

- **CloudTrail Analysis for Forensics**  
  Storing and querying CloudTrail logs is standard practice in incident response and security auditing.

- **Serverless Data Exploration**  
  Athena supports scalable, low-cost log analysis workflows in environments without dedicated analytics infrastructure.

---

## Personal Reflection

This module taught me that logging and monitoring go far beyond just collecting data—they’re about building intelligent feedback systems. The CloudWatch agent configuration and SSM integration helped me see how monitoring can be standardized and scaled efficiently.

Working with Macie was especially eye-opening. The speed and accuracy with which it identified mock sensitive data made me realize the risks of unsecured S3 buckets and the value of automated scanning.

CloudTrail felt like the backbone of AWS auditability, and combining it with Athena and CloudWatch made it clear how powerful AWS is for post-incident analysis and compliance. I left this module with a better appreciation for building secure, observable systems that help detect, respond to, and prevent issues before they become critical.
