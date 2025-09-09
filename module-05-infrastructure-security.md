# Module 5 - Domain 3: Infrastructure Security

---

### Overview
This module focused on the deep technical understanding and configuration of infrastructure security within AWS. The core objectives included building secure and scalable Virtual Private Clouds (VPCs), controlling routing and access with NAT gateways and routing tables, deploying EC2 instances for testing, securely connecting environments using Site-to-Site VPNs, implementing endpoint services like S3 and EC2 Instance Connect, and understanding volume storage types like EBS and Instance Store. The variety of tools and services covered reflect real-world scenarios that cloud security engineers frequently manage.

---

## Hands-On Tasks Completed

- **Manually Built a Custom VPC (winter-vpc1)**  
  - Created the VPC with IPv4 CIDR `10.16.0.0/16` and an Amazon-provided IPv6 block.  
  - Enabled DNS support and DNS hostname resolution.  
  - Created 12 subnets across 3 Availability Zones with manual IPv6 assignment enabled.

- **Configured Internet Gateway and Routing for Web Subnets**  
  - Created and attached an Internet Gateway (`winter-vpc1-igw`).  
  - Created a route table (`winter-vpc1-rt-web`) with IPv4 and IPv6 default routes.  
  - Associated web subnets and enabled auto-assign public IPv4 addresses.  
  - Launched and tested an EC2 instance (`winter-bastion`) in the web subnet, then deleted it and the VPC.

- **Rebuilt VPC via CloudFormation Template**  
  - Used a CloudFormation stack to recreate the entire VPC infrastructure with subnets.

- **Created 3 NAT Gateways**  
  - Provisioned `winter-vpc1-natgw-A/B/C` in `sn-web-A/B/C`, each with an Elastic IP.  
  - Created route tables (`winter-vpc1-rt-privateA/B/C`) and added NAT gateway routes.  
  - Fixed routing by manually associating private subnets with their corresponding route tables.  
  - Verified internet connectivity and deleted all resources afterward.

- **Started New Mini Project with pfSense VPN**  
  - Created EC2 key pair `infra`.  
  - Deployed pfSense+ via CloudFormation and configured it as a customer gateway.  
  - Set up a Virtual Private Gateway and a Site-to-Site VPN (static route).  
  - Configured pfSense IPsec VPN tunnels (Phase 1 & 2) for both AZ1 and AZ2 using provided IPs, PSKs, and encryption settings.  
  - Enabled tunnels and confirmed bidirectional connectivity between AWS and on-prem networks.  
  - Configured security groups and route tables to allow ICMP and all internal traffic.  
  - Used Fleet Manager to test connectivity from on-prem to AWS instances and web interface access.  
  - Deleted all resources after successful VPN simulation.

- **Built and Tested Private VPC Endpoint Access**  
  - Created ECS Instance Connect endpoint (`PrivateVPCEC2InstanceConnect`) and connected privately.  
  - Created an S3 Gateway VPC Endpoint (`PrivateVPCS3`) and connected through the on-prem router.  
  - Successfully copied an object from S3 using CLI (`aws s3 cp ...`).  
  - Set up an **Egress-Only Internet Gateway** for IPv6 outbound traffic with a `::/0` route.  
  - Deleted all infrastructure after testing.

- **Deployed WinterPRIV Stack and Validated Private S3 Access**  
  - Accessed an S3 bucket from Session Manager using private connectivity.  
  - Applied an endpoint policy that restricted access to specific S3 buckets.  
  - Verified that access was denied from the CLI (host PC) but allowed from inside the VPC.  
  - Applied a stricter policy with `aws:sourceVpce` to explicitly deny access outside the allowed VPC endpoint.

- **Set Up VPC Peering Between 3 VPCs Using WinterPEERING Stack**  
  - Created and accepted peering connections between VPC A ↔ B, B ↔ C, and A ↔ C.  
  - Configured routing tables across all three VPCs for full mesh communication.  
  - Modified security groups to allow ICMP traffic, successfully enabling ping between instances across peered VPCs.

- **Performed EBS Volume Mount and Failover Test**  
  - Created a gp3 10 GB EBS volume, attached it to Instance 1-A, formatted, mounted, and wrote data.  
  - Added `/etc/fstab` entry for persistence, verified survival after reboot.  
  - Detached and reattached volume to Instance 2-A, confirmed data was intact.  
  - Snapshot created for AZ migration, restored to new volume in AZ 1-B, mounted and validated content.  
  - Deleted all volumes and snapshots afterward.

- **Tested EC2 Instance Store Volatility**  
  - Launched `InstanceStoreTest` using `m5dn.large` with ephemeral NVMe storage.  
  - Created filesystem, mounted, and wrote a test file.  
  - Rebooted and confirmed data persisted.  
  - Stopped and started the instance, observed the IP change and loss of data, proving that instance store is non-persistent across stop/start cycles.

---

### What I Learned
- **IPv6 and Subnetting Strategy**: Learned how to configure IPv6 addressing in custom subnets and automate its assignment using the AWS console and CloudFormation templates.
- **Routing and NAT Gateways**: Understood how NAT Gateways are used for outbound-only internet access and how private subnets route traffic through them. Learned how route table associations are crucial for correct traffic flow.
- **Internet Gateways and EC2 Access**: Explored how to use Internet Gateways and subnet-level auto-assign settings to ensure public IP and IPv6 addresses are properly applied for outbound connectivity.
- **CloudFormation for Infrastructure as Code**: Improved fluency in automating VPCs, subnets, routing, and security setups using stack templates. This reduced the need for manual intervention and supported repeatability.
- **Site-to-Site VPN Configuration**: Gained hands-on experience in configuring IPSec VPN tunnels using pfSense Plus. Understood the role of customer gateways, virtual private gateways, and their integration with route tables and security groups.
- **Security Group Rules for VPN Routing**: Learned that VPN connectivity is tightly dependent on proper SG configurations across all endpoints involved in the tunnel.
- **VPC Endpoints (Interface and Gateway)**: Learned to secure access to private resources using VPC endpoints. Understood the use of endpoint policies to enforce access restrictions.
- **S3 Endpoint Policies**: Saw how endpoint-level policies can override broader permissions and deny access unless using specific VPCE IDs—an important concept in tightly scoped access control.
- **VPC Peering**: Understood VPC peering between multiple VPCs and the need for reciprocal route table entries and SG configurations to allow traffic flow.
- **EBS vs. Instance Store Volumes**: Discovered how EBS volumes are persistent and can be moved between AZs via snapshots, while instance store volumes are ephemeral and tied to the EC2 lifecycle and host.

---

### Real-World Relevance
- **VPN Tunnels for Hybrid Architecture**: The pfSense Site-to-Site VPN implementation directly reflects a real-world hybrid cloud setup where AWS VPCs must securely connect to on-prem infrastructure.
- **Infrastructure as Code (IaC)**: Using CloudFormation to replicate and tear down environments mirrors best practices in production for consistent deployments and automation.
- **Data Persistence Knowledge**: Understanding the transient nature of instance store volumes versus EBS volumes is crucial when architecting fault-tolerant systems.
- **VPC Endpoint Security**: S3 endpoint policies and the `aws:sourceVpce` condition enable fine-grained access controls, preventing public exposure of sensitive data.
- **Peering and Security Groups**: Practical implementation of VPC peering showed the importance of not just setting routes, but aligning SG rules—a common issue during cross-VPC communications.

---

### Personal Reflection
This module was incredibly immersive and aligned closely with the real-world responsibilities of a cloud security engineer. The level of detail I had to manage—from CIDR planning to VPN tunnel configurations and endpoint policies—showed me the depth of control AWS offers. I made mistakes (like routing not working until subnets were explicitly associated), but debugging those helped solidify my understanding. The hands-on VPN setup, in particular, gave me a strong sense of what’s needed to bridge on-prem and cloud securely. I now feel significantly more confident in managing VPCs, network routing, and storage in secure and scalable ways.
