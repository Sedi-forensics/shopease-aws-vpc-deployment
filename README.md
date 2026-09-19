# ShopEase Secure E-Commerce Infrastructure on AWS

A hands-on AWS networking and deployment project: designing a custom VPC, configuring public/private subnet segmentation, and deploying a publicly accessible web server — built as part of a Cloud-Based Technologies module.

## Overview

ShopEase Online is a fictional e-commerce startup migrating from on-premises infrastructure to AWS. This project implements the core networking foundation for their infrastructure: a custom VPC with properly segmented public and private subnets, secure internet connectivity, and a live Apache web server running on EC2.

## Architecture

| Component | Detail |
|---|---|
| **VPC** | `ShopEase-VPC` — `10.10.0.0/16` |
| **Public Subnet A** | `WebSubnet-A` — `10.10.1.0/24` (AZ: us-east-1a) |
| **Public Subnet B** | `WebSubnet-B` — `10.10.2.0/24` (AZ: us-east-1b) |
| **Private Subnet A** | `AppSubnet-A` — `10.10.10.0/24` (AZ: us-east-1a) |
| **Private Subnet B** | `AppSubnet-B` — `10.10.20.0/24` (AZ: us-east-1b) |
| **Internet Gateway** | `ShopEase-IGW` attached to `ShopEase-VPC` |
| **Public Route Table** | `Public-RT` — routes `0.0.0.0/0` → IGW; associated with WebSubnet-A & B |
| **Private Route Table** | `Private-RT` — local routing only; associated with AppSubnet-A & B |
| **EC2 Instance** | `ShopEase_WebServer-Mashabane` — Amazon Linux 2023, `t3.micro` |
| **Public IP** | `13.220.33.112` |
| **Private IP** | `10.10.1.189` (WebSubnet-A) |
| **Security Group** | `SG-Web` — inbound SSH, HTTP, HTTPS |

## What Was Built

1. Designed and provisioned a custom VPC with CIDR `10.10.0.0/16`
2. Created 4 subnets across 2 Availability Zones — separating public (web) and private (application) tiers
3. Configured `Public-RT` with a `0.0.0.0/0` route pointing to the Internet Gateway, and `Private-RT` with local-only routing
4. Associated `WebSubnet-A` and `WebSubnet-B` with `Public-RT`; `AppSubnet-A` and `AppSubnet-B` with `Private-RT`
5. Launched EC2 instance `ShopEase_WebServer-Mashabane` in `WebSubnet-A` with an auto-assigned public IP
6. Connected via SSH using EC2 Instance Connect, updated the OS, and installed Apache HTTP Server (`httpd`)
7. Verified the web server live at `http://13.220.33.112` — "It works!"

## Evidence Log — Screenshots

### Route Tables

**Public-RT — before IGW route added (local only)**
![Public-RT initial](./screenshots/01-public-rt-initial.png)

**Public-RT — after adding 0.0.0.0/0 → IGW route (Active)**
![Public-RT with IGW route](./screenshots/02-public-rt-igw-route.png)

**Private-RT — AppSubnet-A and AppSubnet-B associated (no internet route)**
![Private-RT subnet associations](./screenshots/03-private-rt-subnets.png)

**Public-RT — WebSubnet-A and WebSubnet-B associated**
![Public-RT subnet associations](./screenshots/04-public-rt-subnets.png)

---

### EC2 Instance

**EC2 instance summary — Running, Public IP 13.220.33.112, Subnet WebSubnet-A**
![EC2 instance running](./screenshots/05-ec2-running.png)

**EC2 instance details — AMI, launch time, key pair, VPC**
![EC2 instance details](./screenshots/06-ec2-details.png)

---

### SSH & Apache Installation

**SSH connection established — Amazon Linux 2023 banner**
![SSH connected](./screenshots/07-ssh-connected.png)

**OS update via yum — system confirmed up to date**
![yum update](./screenshots/08-ssh-update.png)

**Apache installation — sudo yum install -y httpd (13 packages)**
![httpd install](./screenshots/09-httpd-install.png)

---

### Final Verification

**Apache web server live at 13.220.33.112 — "It works!"**
![Apache works](./screenshots/10-apache-works.png)

---

## Key Networking Concepts Demonstrated

- **Subnet segmentation** for defense-in-depth: internet-facing resources (web tier) are isolated from internal application logic (app tier)
- **Route table behavior**: a subnet is only "public" because its route table sends traffic to an Internet Gateway — not because of any inherent subnet property. This is proven by the before/after screenshots of `Public-RT`
- **Security groups** as stateful, instance-level firewalls controlling inbound traffic on specific ports only (SSH 22, HTTP 80, HTTPS 443)
- **Multi-AZ design** across `us-east-1a` and `us-east-1b` — the foundation for future high availability and load balancing
- **Private subnets** with local-only routing ensure application tier resources cannot be reached directly from the internet

## Tools Used

- AWS Management Console (VPC, EC2, Route Tables, Internet Gateways)
- AWS Academy Learner Lab environment
- Amazon Linux 2023
- Apache HTTP Server (`httpd` 2.4.68)
- EC2 Instance Connect (browser-based SSH)

---

*Completed as part of a Cloud-Based Technologies coursework module — BSc IT: Security and Network Engineering, Eduvos, 2026.*

*Author: Lesedi Lesego Reletile Mashabane | [linkedin.com/in/lesedi-mashabane-562294298](https://www.linkedin.com/in/lesedi-mashabane-562294298) | [github.com/Sedi-forensics](https://github.com/Sedi-forensics)*
