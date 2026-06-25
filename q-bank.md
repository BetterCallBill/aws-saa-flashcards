# AWS SAA Question Bank

---

## 1. Placement group — definition

Which of the following correctly describes a placement group?

- Redundancy group of EC2 instances deployed across multiple Availability Zones (AZs)
- The combination of CPU, memory, storage performance, and network capacity that can be selected when creating an EC2 instance.
- This feature allows you to logically group multiple EC2 instances, enabling low-latency communication between instances and mitigating the impact of hardware failures.
- This includes computing optimization, memory optimization, and storage optimization.

**Relevant AWS Service:** EC2 Placement Groups

**Trigger Words:** "logically group multiple EC2 instances" (the defining action of a placement group) + "low-latency communication" (cluster strategy) + "mitigating the impact of hardware failures" (spread/partition strategies) — together these describe what a placement group does across all three strategies.

**Right Answer + Why:** "This feature allows you to logically group multiple EC2 instances, enabling low-latency communication between instances and mitigating the impact of hardware failures." — A placement group influences how instances are physically placed on AWS hardware, covering both low-latency packing (Cluster) and failure isolation (Spread/Partition).

**Trap Tags:**

Redundancy group of EC2 instances deployed across multiple Availability Zones (AZs)
= *wrong concept* — that describes generic multi-AZ redundancy; placement groups generally keep instances together (often in one AZ), not a cross-AZ redundancy construct.

The combination of CPU, memory, storage performance, and network capacity that can be selected when creating an EC2 instance.
= *wrong service entirely* — that's the definition of an **instance type**.

This includes computing optimization, memory optimization, and storage optimization.
= *wrong service entirely* — that describes **instance type families/categories**, not placement groups.

**Rule Card:** "Logically group EC2 instances for low-latency and/or hardware-failure isolation → Placement Group (Cluster=low latency, Spread/Partition=fault isolation)."

---

## 2. Partition placement groups

Which of the following correctly describes "partition placement groups" among the EC2 placement groups?

- EC2 instances cannot be deployed in different Availability Zones (AZs).
- Each partition will contain only one EC2 instance.
- Each partition within a group does not share a rack.
- It launches on dedicated hardware, separate from other EC2 instances.

**Relevant AWS Service:** EC2 Partition Placement Groups

**Trigger Words:** "partition placement groups" (narrows to one strategy) + "does not share a rack" (the defining isolation property) — the question tests the partition-specific guarantee versus spread/dedicated distractors.

**Right Answer + Why:** "Each partition within a group does not share a rack." — AWS guarantees partitions sit on distinct racks (separate power and network), so a hardware failure in one partition stays contained. Built for large distributed/replicated workloads (HDFS, Cassandra, Kafka) where you want fault isolation across replicas while staying in one region.

**Trap Tags:**

EC2 instances cannot be deployed in different Availability Zones (AZs).
= *wrong fact* — a partition group **can** span multiple AZs within a region (up to 7 partitions per AZ).

Each partition will contain only one EC2 instance.
= *wrong strategy* — each partition can hold **many** instances; "one instance per rack" is the **Spread** group property, not partition.

It launches on dedicated hardware, separate from other EC2 instances.
= *wrong service entirely* — that describes **Dedicated Hosts / Dedicated Instances**.

**Rule Card:** "Partition placement group → instances split into partitions on distinct racks (fault isolation for distributed/replicated big-data workloads); Spread = one instance per rack; Dedicated = isolated tenancy."

---

## 3. Private subnet — appropriate resources

Which AWS resources are appropriate to place in a private subnet within a VPC?

- Web servers accessed directly from the internet
- Web servers under ELB that are not directly accessed from the internet
- SSH jump server accessed directly from the internet
- Amazon S3 objects that are not directly accessed from the internet

**Relevant AWS Service:** Amazon VPC (public vs. private subnets)

**Trigger Words:** "private subnet" (no direct route to an internet gateway) + "not directly accessed from the internet" (the resource receives traffic indirectly) — the answer is whatever sits behind a front-end and never needs an inbound internet route.

**Right Answer + Why:** "Web servers under ELB that are not directly accessed from the internet." — A private subnet has no direct internet inbound path; servers behind an ELB receive traffic via the load balancer, so they belong in a private subnet for isolation.

**Trap Tags:**

Web servers accessed directly from the internet
= *wrong subnet* — direct inbound internet access requires a **public** subnet with an internet gateway route.

SSH jump server accessed directly from the internet
= *wrong subnet* — a bastion/jump host must be reachable from the internet, so it lives in a **public** subnet.

Amazon S3 objects that are not directly accessed from the internet
= *wrong service entirely* — S3 is a regional service and does **not** live inside any VPC subnet (you reach it via a gateway endpoint, not by placing it in a subnet).

**Rule Card:** "Private subnet = no direct internet inbound → back-end/app/DB tiers behind an ELB; bastions and internet-facing servers go in public subnets; S3 isn't a subnet resource at all."

---

## 4. ELB access logs — what's recorded

What information is recorded when ELB access logs are enabled?

- AWS account operation history for ELB
- Target system logs
- Number of targets in normal/abnormal states
- Client connection information

**Relevant AWS Service:** Elastic Load Balancing — Access Logs

**Trigger Words:** "ELB access logs" + "what information is recorded" — access logs capture per-request connection detail, distinguishing them from CloudTrail, target-side logs, and health-check metrics.

**Right Answer + Why:** "Client connection information." — ELB access logs record detailed request data: client IP and port, request paths, latencies, response codes, and processing times for connections passing through the load balancer.

**Trap Tags:**

AWS account operation history for ELB
= *wrong service entirely* — API/management actions are recorded by **CloudTrail**, not access logs.

Target system logs
= *wrong source* — application/OS logs live on the targets themselves; the load balancer doesn't collect them.

Number of targets in normal/abnormal states
= *wrong feature* — healthy/unhealthy target counts come from **health checks / CloudWatch metrics**, not access logs.

**Rule Card:** "ELB access logs → per-request client connection details (IP, latency, paths, codes); API history = CloudTrail; health state = CloudWatch."

---

## 5. Shared responsibility model — AWS's responsibilities (Select two)

In AWS's shared responsibility model, which of the following are AWS's responsibilities? (Select two)

- Data center access control
- Firmware version upgrade for network devices
- Managing AWS accounts created by users
- Managing access permissions to the database
- Encryption of data saved by the user

**Relevant AWS Service:** AWS Shared Responsibility Model

**Trigger Words:** "AWS's responsibilities" (security **OF** the cloud) — anything physical/infrastructure is AWS; anything about user data, identities, or access config is the customer (security **IN** the cloud).

**Right Answer + Why:** "Data center access control" + "Firmware version upgrade for network devices." — Both are part of the underlying infrastructure AWS owns and operates (physical security and hardware/firmware maintenance), which is squarely "security of the cloud."

**Trap Tags:**

Managing AWS accounts created by users
= *customer responsibility* — identity and account management is "security in the cloud."

Managing access permissions to the database
= *customer responsibility* — configuring IAM/DB permissions is on the customer.

Encryption of data saved by the user
= *customer responsibility* — protecting customer data (including choosing/managing encryption) belongs to the customer.

**Rule Card:** "AWS = security OF the cloud (physical facilities, hardware, firmware, the managed infra). Customer = security IN the cloud (data, identities, access config, encryption choices)."

---

## 6. S3 Transfer Acceleration

Which of the following is a correct description of the S3 "Transfer Acceleration" feature? *(Problem ID: 30305)*

- A feature that speeds up data transfer between users and S3 buckets.
- A feature that automatically saves data to different regions.
- A function to transfer data to an on-premises environment via a dedicated network.
- A feature that allows for high-speed data transfer to multiple Availability Zones (AZs).

**Relevant AWS Service:** Amazon S3 Transfer Acceleration

**Trigger Words:** "Transfer Acceleration" + "speeds up data transfer between users and S3" — the feature routes traffic over CloudFront edge locations to accelerate long-distance transfers.

**Right Answer + Why:** "A feature that speeds up data transfer between users and S3 buckets." — Transfer Acceleration uses the CloudFront edge network so uploads/downloads ride AWS's optimized backbone, cutting latency for geographically distant clients.

**Trap Tags:**

A feature that automatically saves data to different regions.
= *wrong service entirely* — that's **Cross-Region Replication (CRR)**.

A function to transfer data to an on-premises environment via a dedicated network.
= *wrong service entirely* — that's **Direct Connect / Storage Gateway**.

A feature that allows for high-speed data transfer to multiple Availability Zones (AZs).
= *wrong concept* — S3 already stores data redundantly across AZs; that's not what Transfer Acceleration does.

**Rule Card:** "Speed up long-distance client↔S3 transfers → Transfer Acceleration (CloudFront edge). Cross-region copy = CRR; on-prem dedicated link = Direct Connect."

---

## 7. S3 storage class — long-term archive, 10TB, 5 years

I want to store 10TB of data in Amazon S3. Which storage class offers the best cost performance while meeting the following requirements? *(Problem ID: 30306)*
- Data must be stored for 5 years due to compliance requirements.
- The purpose is data storage; data access is generally not required.
- If data access is necessary, it's acceptable if retrieval takes up to a day.

- S3 Glacier Deep Archive
- S3 Glacier Instant Retrieval
- S3 Glacier Flexible Retrieval
- S3 Standard-IA (S3 Standard-Infrequent Access)

**Relevant AWS Service:** Amazon S3 Glacier Deep Archive

**Trigger Words:** "stored for 5 years" + "compliance" (long-term archival) + "access generally not required" (rarely read) + "retrieval up to a day is acceptable" (12–48h tolerated) + "best cost performance" (cheapest) — every clue points to the lowest-cost archive tier.

**Right Answer + Why:** "S3 Glacier Deep Archive." — It's the cheapest S3 class, designed for long-term compliance archival of rarely accessed data, with retrieval typically 12–48 hours — well within the "up to a day" tolerance.

**Trap Tags:**

S3 Glacier Instant Retrieval
= *over-provisioned* — millisecond access you don't need, at higher cost.

S3 Glacier Flexible Retrieval
= *right family, wrong tier* — fine for archives but costs more than Deep Archive.

S3 Standard-IA
= *wrong use case* — for infrequently accessed data that still needs **immediate** retrieval; more expensive for pure cold storage.

**Rule Card:** "Long-term + compliance + rarely accessed + hours/days retrieval OK + lowest cost → S3 Glacier Deep Archive."

---

## 8. Glacier Flexible Retrieval — urgent 200MB in 5 minutes

Backup data is stored in Amazon S3 Glacier Flexible Retrieval. I urgently need to retrieve 200MB of data within 5 minutes. Which of the following methods will meet this requirement? *(Problem ID: 30307)*

- Execute the restore request with "Standard Extraction" specified as the data extraction option.
- A high-performance EC2 instance is prepared specifically for data retrieval.
- Download it to your PC, then extract it.
- Execute a restore request with the "Fast Retrieve" (Expedited) option selected for data retrieval.

**Relevant AWS Service:** Amazon S3 Glacier Flexible Retrieval — retrieval tiers

**Trigger Words:** "Glacier Flexible Retrieval" (limits which tiers exist) + "within 5 minutes" (forces a sub-hour tier) + "200MB" (confirms under the 250MB Expedited cap).

**Right Answer + Why:** "Execute a restore request with the 'Fast Retrieve' (Expedited) option." — Expedited is the only tier returning small objects (≤250MB) in 1–5 minutes, so it's the sole option meeting a sub-hour deadline.

**Trap Tags:**

Execute the restore request with "Standard Extraction" specified.
= *right service, wrong tier* — Standard takes 3–5 hours, far too slow.

A high-performance EC2 instance is prepared specifically for data retrieval.
= *irrelevant mechanism* — retrieval speed is fixed by the restore tier; compute can't change it.

Download it to your PC, then extract it.
= *irrelevant mechanism* — location/destination doesn't affect Glacier restore time.

**Rule Card:** "Glacier Flexible Retrieval, need <1hr? → Expedited (1–5 min, ≤250MB). Else Standard 3–5hr, Bulk 5–12hr. Compute/destination never change retrieval time."

---

## 9. S3 lifecycle automation

Which of the following features automates the process of changing the storage class of data stored in Amazon S3 or deleting objects?

- Lifecycle
- Bucket policy
- Versioning
- MFA Delete

**Relevant AWS Service:** Amazon S3 Lifecycle

**Trigger Words:** "automates the process" (rule-based, hands-off) + "changing the storage class" (transition actions) + "deleting objects" (expiration actions) — only Lifecycle does both on a schedule.

**Right Answer + Why:** "Lifecycle." — It runs rule-based transition actions (move objects to cheaper classes like IA or Glacier) and expiration actions (auto-delete) on an age- or date-based schedule, no manual work required.

**Trap Tags:**

Bucket policy
= *wrong service entirely* — controls access permissions (who can do what), not data movement or deletion.

Versioning
= *wrong service entirely* — preserves multiple object versions against overwrites/deletes; doesn't transition or expire data.

MFA Delete
= *wrong service entirely* — adds an extra auth requirement before deleting versions; a guardrail, not automation.

**Rule Card:** "S3 auto-transition class or auto-delete objects → Lifecycle (transition + expiration rules)."

---

## 10. S3 versioning — root-only deletion

I've enabled versioning in Amazon S3 to manage data across generations. I want only the root user to have deletion privileges for the versioned data. Which of the following S3 features should I use?

- Bucket policy
- MFA Delete
- IAM policy (user policy)
- ACL (Access Control List)

**Relevant AWS Service:** Amazon S3 MFA Delete

**Trigger Words:** "versioning ... enabled" (MFA Delete requires versioning) + "only the root user" (only the bucket owner's root account can enable/manage it) + "deletion privileges for versioned data" (MFA Delete gates version deletion) — these three point only to MFA Delete.

**Right Answer + Why:** "MFA Delete." — It requires an MFA code to delete object versions (or suspend versioning), and on a versioning-enabled bucket only the root account can enable and exercise it — exactly the "only root can delete" requirement.

**Trap Tags:**

Bucket policy
= *right category, wrong tool* — can restrict delete actions broadly, but can't pin deletion exclusively to root and isn't tied to versioning.

IAM policy (user policy)
= *irrelevant mechanism* — grants/denies to IAM users/roles, but root isn't governed by IAM policies, so it can't enforce "root-only" deletion.

ACL (Access Control List)
= *wrong service entirely* — ACLs grant coarse access to grantees; no concept of MFA-gated or root-only version deletion.

**Rule Card:** "Versioning + 'only root can delete versions' / require MFA to delete → MFA Delete (root-only, versioning required)."

---

## 11. S3 access control — per bucket/object, account-level, no IP condition

Which of the following Amazon S3 access control methods has the following characteristics?
- Access control is configured per bucket and per object, with permission settings made at the AWS account level.
- It is not possible to control access based on the source IP address or domain.

- Signed URL
- Bucket policy
- IAM policy (user policy)
- ACL

**Relevant AWS Service:** Amazon S3 ACL (Access Control List)

**Trigger Words:** "per bucket and per object" (ACLs attach at both levels) + "permission settings made at the AWS account level" (ACLs grant to accounts/canonical IDs or predefined groups) + "cannot control access based on source IP or domain" (ACLs have no condition keys) — these describe ACLs exactly.

**Right Answer + Why:** "ACL." — The legacy access method that grants coarse permissions per bucket and per object to AWS-account-level grantees, and because it has no condition support, it can't filter by IP or domain.

**Trap Tags:**

Signed URL
= *wrong service entirely* — time-limited access to a specific object via a pre-signed link; not account-level bucket/object permissioning.

Bucket policy
= *right category, wrong tool* — bucket-level (not per-object) and **can** restrict by source IP/domain via conditions, contradicting the stated characteristics.

IAM policy (user policy)
= *right category, wrong tool* — attaches to IAM identities (not per bucket/object) and **supports** IP/condition control, so it fails both clues.

**Rule Card:** "Per-bucket AND per-object, AWS-account-level grants, no IP/condition filtering → ACL."

---

## 12. S3 SSE-C (Select two)

Which of the following statements correctly describes "SSE-C," one of the data encryption methods available for Amazon S3? (Select two)

- The keys are managed in AWS KMS.
- The keys are managed by S3.
- The user manages the key.
- Encryption is performed on the S3 side.
- Encryption is performed on the user's side.

**Relevant AWS Service:** Amazon S3 SSE-C (Server-Side Encryption with Customer-Provided Keys)

**Trigger Words:** "SSE-C" (the "C" = customer-provided keys) + "Select two" (you need the key-ownership fact **and** the where-encryption-happens fact) — SSE-C means you supply the key but S3 does the encryption.

**Right Answer + Why:** "The user manages the key." + "Encryption is performed on the S3 side." — In SSE-C the customer provides and manages their own key, but because it's still **server-side** encryption, S3 performs the actual encrypt/decrypt and then discards the key.

**Trap Tags:**

The keys are managed in AWS KMS.
= *right service, wrong tier* — that describes **SSE-KMS**.

The keys are managed by S3.
= *right service, wrong tier* — that describes **SSE-S3**, where AWS owns and manages the keys.

Encryption is performed on the user's side.
= *wrong concept* — that's **client-side encryption (CSE)**; SSE-C is server-side, so encryption happens on S3.

**Rule Card:** "SSE-C → customer supplies/manages the key, but S3 (server-side) does the encryption. KMS=SSE-KMS, S3-managed=SSE-S3, user-side encrypt=CSE."

---

## 13. EC2 inbound HTTP — network access control

I want to allow access from the internet via HTTP (port 80) to a web server running on an EC2 instance in a public subnet. What network access control settings should I configure?

- The network ACL for the subnet allows outbound traffic on port 80. All other inbound and outbound traffic is denied.
- Allow outbound traffic on port 80 in the web server's security group.
- Allow inbound traffic on port 80 in the web server's security group.
- The network ACL for the subnet allows inbound traffic on port 80. All other inbound and outbound traffic is denied.

**Relevant AWS Service:** EC2 Security Groups (with Network ACL contrast)

**Trigger Words:** "allow access from the internet via HTTP (port 80)" (inbound traffic) + "web server ... in a public subnet" (instance-level control applies) + "network access control settings" (tests SG vs NACL behavior) — the core fact is that inbound web access is an inbound rule, and security groups are stateful.

**Right Answer + Why:** "Allow inbound traffic on port 80 in the web server's security group." — Incoming HTTP requests are **inbound**, and because security groups are **stateful**, allowing inbound on 80 automatically permits the return traffic — no outbound rule needed.

**Trap Tags:**

NACL allows outbound port 80, all other traffic denied.
= *wrong direction + breaks return traffic* — inbound requests need an inbound allow; denying everything else blocks the stateless NACL's ephemeral return ports.

Allow outbound traffic on port 80 in the security group.
= *wrong direction* — outbound governs traffic the server initiates; does nothing for incoming visitor requests.

NACL allows inbound port 80, all other traffic denied.
= *right direction, stateless trap* — NACLs are **stateless**, so allowing inbound 80 while denying all outbound blocks the response on ephemeral ports; connection fails.

**Rule Card:** "Inbound internet HTTP to EC2 → Security Group inbound allow port 80 (SG is stateful, return traffic auto-allowed; NACLs are stateless and need explicit ephemeral-port rules)."

---

## 14. Security group referencing — dynamic web tier → DB

Multiple web servers are located in a public subnet, and one database server is located in a private subnet. The database server should only accept access from the web servers. All web servers share the same security group, and the number of servers used varies depending on the load. What network access control settings should be configured?

- Allow communication from the web server's IP address in the database server's security group.
- The network ACL of the DB subnet allows communication from the network ACL of the web server's subnet.
- The network ACL of the DB subnet allows communication from the web server's security group.
- Allow communication from the web server's security group in the database server's security group.

**Relevant AWS Service:** EC2 Security Groups (security group referencing / chaining)

**Trigger Words:** "only accept access from the web servers" (source-restricted to a specific tier) + "all web servers share the same security group" (a referenceable source SG exists) + "number of servers ... varies depending on the load" (IPs change with autoscaling, so IP-based rules break) — together these point to referencing the source security group rather than IPs.

**Right Answer + Why:** "Allow communication from the web server's security group in the database server's security group." — Referencing the web tier's security group as the source means any instance in that group is automatically allowed regardless of its IP, so scaling web servers up or down needs no rule changes.

**Trap Tags:**

Allow communication from the web server's IP address in the database server's security group.
= *brittle / wrong approach* — IP-based rules break when autoscaling adds or removes servers with new IPs; you'd have to constantly update rules.

The network ACL of the DB subnet allows communication from the network ACL of the web server's subnet.
= *wrong concept* — NACLs can't reference each other; they only filter by CIDR/IP ranges and ports, not by other NACLs.

The network ACL of the DB subnet allows communication from the web server's security group.
= *wrong concept* — NACLs cannot reference security groups at all; only security groups can reference other security groups.

**Rule Card:** "Restrict DB access to a dynamic/autoscaling web tier → reference the web tier's security group as the source in the DB security group (SGs reference SGs; NACLs only do CIDR/IP)."
