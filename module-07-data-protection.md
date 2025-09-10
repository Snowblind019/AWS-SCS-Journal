# Module 7 – Domain 5: Data Protection

## Objective
The goal of this module was to explore and implement various data protection mechanisms in AWS. This included configuring encryption for data at rest using different server-side encryption methods (SSE-S3, SSE-KMS), creating and managing AWS Key Management Service (KMS) keys, and enforcing encryption access control through IAM policies. In addition, I experimented with data encryption and decryption at the command line using the AWS CLI. The module concluded with testing session stickiness behavior in an Application Load Balancer (ALB), demonstrating how load balancing can maintain consistent client sessions across backend EC2 instances.

---

## Hands-On Tasks Completed

- **Encrypted S3 Objects Using Multiple Encryption Methods**
  - Created an S3 bucket named `winterday2331`.
  - Created a symmetric single-region KMS key named `WinterKey` with no admin access—trusted only by the root user.
  - Uploaded three separate images to the bucket using:
    - **SSE-S3** encryption.
    - **SSE-KMS** encryption using the default AWS-managed key `aws/s3`.
    - **SSE-KMS** encryption using the custom key `WinterKey`.

- **Tested IAM Permissions Impact on Encrypted Object Access**
  - Verified all three images were accessible using the `iamadmin` user.
  - Applied a deny policy on `iamadmin` that blocked all `kms:*` actions:
    ```json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Sid": "VisualEditor0",
          "Effect": "Deny",
          "Action": "kms:*",
          "Resource": "*"
        }
      ]
    }
    ```
  - Confirmed:
    - SSE-S3 object still accessible.
    - SSE-KMS encrypted objects failed to open due to denied KMS permissions.
  - Removed the deny policy and confirmed access was restored.
  - Set default encryption on the S3 bucket to **SSE-KMS** and confirmed successful upload of a new image using the new default setting.
  - Cleaned up all created resources.

- **Performed CLI-Based File Encryption and Decryption with AWS KMS**
  - Created a new symmetric KMS key named `WinterBot` (single-region).
  - Assigned `iamadmin` as both Key Administrator and Key User.
  - Launched AWS CloudShell and executed:
    ```bash
    echo "Snow can be blinding" > snowblind.txt

    aws kms encrypt \
      --key-id alias/WinterBot \
      --plaintext fileb://snowblind.txt \
      --output text \
      --query CiphertextBlob \
      | base64 --decode > non_snowblind.enc

    aws kms decrypt \
      --ciphertext-blob fileb://non_snowblind.enc \
      --output text \
      --query Plaintext | base64 --decode > visiblesnow.txt
    ```
  - Verified that `non_snowblind.enc` was encrypted and `visiblesnow.txt` correctly decrypted to plaintext.
  - Deleted all related resources.

- **Tested ALB Session Stickiness Behavior**
  - Deployed a CloudFormation stack that launched 6 EC2 instances, each hosting a web page with a cat GIF and unique instance ID.
  - Opened the ALB DNS and confirmed load balancing rotated through all 6 EC2 instances.
  - Enabled **Session Stickiness** in the target group settings:
    - Turned on stickiness.
    - Set duration to 1 minute.
  - Verified stickiness by refreshing the ALB URL—remained on the same instance.
  - Stopped the active instance and confirmed ALB redirected to a new instance.
  - Restarted the previous instance and confirmed ALB stuck to the new one.
  - Disabled stickiness and verified that ALB resumed rotating across all instances.
  - Cleaned up the entire deployment.

---

## What I Learned

- **S3 Encryption Options**  
  I learned the differences between SSE-S3 (server-side encryption with Amazon-managed keys) and SSE-KMS (server-side encryption with AWS KMS keys), including how access is controlled through IAM policies and KMS permissions. I discovered that denying access to KMS affects the ability to read SSE-KMS encrypted objects, while SSE-S3 remains accessible.

- **Default Bucket Encryption Configuration**  
  I configured default encryption settings at the S3 bucket level and confirmed that any uploads afterward automatically inherit the specified encryption method, ensuring consistent encryption enforcement without requiring manual selection.

- **KMS Key Permissions and Access Control**  
  Creating KMS keys with explicit administrators and usage roles helped reinforce my understanding of key-level permissions. Denying KMS access via IAM policies showed how fine-grained control can block encrypted data access even when broader permissions are assigned.

- **CLI-Based Encryption and Decryption**  
  Using `aws kms encrypt` and `aws kms decrypt` in CloudShell gave me a deeper understanding of how data can be programmatically secured and restored using KMS. This reinforced the utility of KMS in automated workflows and script-based environments.

- **Application Load Balancer Stickiness**  
  Testing ALB stickiness gave me insight into how session persistence works. Enabling stickiness ensured users were consistently directed to the same backend EC2 instance, which is crucial for stateful applications. I also observed how ALB responds when the sticky instance is terminated and how it handles failover and recovery.
