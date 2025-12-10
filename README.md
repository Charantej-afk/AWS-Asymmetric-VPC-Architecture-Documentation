# **AWS Asymmetric VPC Architecture (No NAT Gateway)**

## **📌 Project: asymmetric-vpc-build**

This README document describes the architecture and configuration of the **asymmetric-vpc**, a fully customized AWS VPC designed using variable-size subnets to simulate real-world IP capacity planning.
All resources were deployed using the **AWS Console (GUI)**—not Terraform.

---

# **📖 Overview**

The **asymmetric-vpc** is a production-grade Virtual Private Cloud designed with **six subnets of different sizes**, distributed across multiple Availability Zones, and aligned with expected resource scaling.

This setup includes:

✔ VPC with /16 CIDR
✔ 3 Public Subnets (varying IP capacities)
✔ 3 Private Subnets (varying IP capacities)
✔ Internet Gateway for public subnets
✔ **No NAT Gateways**
✔ **Private subnets are fully isolated (local routing only)**
✔ Individual Route Tables per subnet
✔ Consistent tagging for cost allocation and resource organization

This architecture is ideal for environments that require strict isolation for private workloads with no outbound internet connectivity.

---

# **📡 Architecture Summary**

## **VPC**

* **Name:** asymmetric-vpc
* **CIDR:** 10.0.0.0/16
* **DNS Resolution:** Enabled
* **DNS Hostnames:** Enabled

### **Global Tags Applied**

| Key         | Value                |
| ----------- | -------------------- |
| Environment | production           |
| Owner       | network-team         |
| Project     | asymmetric-vpc-build |
| CostCenter  | AWS-Networking       |

---

# **📍 Subnet Design (Variable Sizes)**

| Subnet Name      | Type    | Approx IP Capacity | CIDR Size | Purpose(Example)           |
| ---------------- | ------- | ------------------ | --------- | -------------------------- |
| Public Subnet A  | Public  | ~256               | /24       | LB, bastion, public apps   |
| Public Subnet B  | Public  | ~4,096             | /20       | Scalable public workloads  |
| Public Subnet C  | Public  | ~8,192             | /19       | Very large public services |
| Private Subnet A | Private | ~1,024             | /22       | DB tier / backend servers  |
| Private Subnet B | Private | ~512               | /23       | Small-scale internal apps  |
| Private Subnet C | Private | ~8,192             | /19       | Large-scale backend tier   |

### **Tag: Tier = public or private**

### **Subnet Behavior**

* **Public Subnets**

  * Auto-assign public IPv4 enabled
  * Internet access via Internet Gateway
* **Private Subnets**

  * Auto-assign public IPv4 disabled
  * **No NAT Gateway**
  * **No Internet Access**
  * **Only local routing allowed**

---

# **🌐 Internet Gateway**

* **Name:** asym-igw
* Attached to: asymmetric-vpc
* Used exclusively by public subnets

---

# **🔀 Route Table Strategy**

Each subnet has **its own dedicated route table** (no sharing).

## **Public Route Tables**

Each public subnet route table includes:

```
0.0.0.0/0 → Internet Gateway
```

Allows public resources outbound and inbound internet connectivity.

## **Private Route Tables (Local Only)**

Each private subnet route table includes:

```
10.0.0.0/16 → local
```

### **Important:**

🚫 **No NAT Gateway**
🚫 **No VPC Endpoints**
🚫 **No VPC Peering**
🚫 **No internet access from private subnets**

Private subnets host workloads that must remain fully isolated.

---

# **🔒 Private Subnet Isolation Model**

The private subnets are intentionally sealed from outbound traffic:

* Unable to reach the internet
* Cannot connect to AWS services like S3 unless privately connected by other means (not used here)
* Only communicate with internal resources in the VPC
* Maximum security for databases, backend services, and internal-only workloads

This aligns with strict enterprise security requirements.

---

# **🛠 Deployment Steps (High-Level)**

### 1️⃣ Create VPC

Configured /16 CIDR + DNS support → Added tags
📎 *[Screenshot placeholder]*

### 2️⃣ Create All Public and Private Subnets

Configured variable CIDR sizes + AZ distribution
📎 *[Screenshot placeholder]*

### 3️⃣ Create Internet Gateway

Created and attached to VPC
📎 *[Screenshot placeholder]*

### 4️⃣ Create Route Tables

Six route tables total (one per subnet)
📎 *[Screenshot placeholder]*

### 5️⃣ Associate Route Tables with Subnets

Public → IGW route
Private → Local-only
📎 *[Screenshot placeholder]*

### 6️⃣ Apply Resource Tagging

Added tags for cost visibility and management
📎 *[Screenshot placeholder]*

---

# **📦 Summary**

This VPC architecture provides:

✔ Flexible IP sizing
✔ Strict network segmentation
✔ High scalability in public tiers
✔ Maximum isolation in private tiers
✔ Clear routing and separation of concerns
✔ Production-level tagging and organization

### **Private subnets are completely isolated**, making this environment ideal for sensitive applications, internal systems, and secure backend workloads.
