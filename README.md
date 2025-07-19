# 🔐 Private Application Load Balancer - AWS EC2 (Windows Server 2025)

This project demonstrates how to create a **private internal Application Load Balancer (ALB)** on AWS, load balancing between 3 Windows Server 2025 EC2 instances running IIS.

---

## 📋 Prerequisites

* AWS Account
* Basic knowledge of EC2, Load Balancer, and Windows RDP
* IAM permissions for EC2, ALB, and VPC resources

---

## 🛠️ Setup Steps

### ✅ 1. Create Resource Group (Logical Naming)

Create a project resource group:

```bash
LBRG
```

---

### 🔐 2. Configure Security Group

Create a security group with the following inbound rules:

| Type  | Port | Source   |
| ----- | ---- | -------- |
| RDP   | 3389 | Your IP  |
| HTTP  | 80   | VPC CIDR |
| HTTPS | 443  | VPC CIDR |

> ⚠️ Make sure this SG is associated with both EC2 and Load Balancer

---

### 🖥️ 3. Launch EC2 Instances

* Launch **3 EC2 instances**
* **AMI**: Windows Server 2025 Base
* **Instance Type**: `t3.medium`
* **Key Pair**: Create or select existing `key.pem`

---

### 🔑 4. Decrypt Credentials & RDP

* Download the **RDP file**
* Use `key.pem` to decrypt admin password from EC2 console
* Use RDP to connect to instances

---

### 🌐 5. Install IIS on All Servers

On each instance:

1. Open **Server Manager**
2. Add Roles and Features → Web Server (IIS)
3. Go to: `C:\inetpub\wwwroot\index.html`
4. Modify it like below:

```html
<h1>Webserver 1</h1> <!-- Change for 2, 3 on respective instances -->
```

---

### ⚖️ 6. Create Internal Load Balancer

In EC2 Console:

* Navigate to **Load Balancers**
* Click **Create Load Balancer**

  * Type: **Application Load Balancer**
  * Scheme: **Internal**
  * Name: `my-load-balancer`
  * VPC: Select existing
  * Subnets: Private or public (based on design)

---

### 🎯 7. Create Target Group

* Go to **Target Groups**
* Create:

  * Type: `Instances`
  * Protocol: HTTP
  * Port: 80
  * Health check path: `/index.html`
* Register all 3 EC2 instances

---

### 🔁 8. Attach Target Group to Load Balancer

* In ALB configuration, attach the target group
* Wait for health checks to show `healthy` state

---

### 🌍 9. Test Load Balancer (Internally)

* Copy **DNS name** of Load Balancer (from EC2 → Load Balancers)
* **RDP into one instance**
* Open browser, paste internal ALB URL

You should see different webserver names like:

* `Webserver 1`
* `Webserver 2`
* `Webserver 3`

on page refresh (round-robin)

---

## 🧹 Clean-up Steps

To avoid costs, remove resources:

1. **Delete Target Group**
2. **Deregister Instances**
3. **Delete Load Balancer**
4. **Terminate EC2 Instances**
5. **Delete Security Group / Key Pair**

---

## 📁 Repository Structure

```
private-alb-windows/
├── README.md
└── assets/
    └── architecture-diagram.png (optional)
```

---

## 🧠 Notes

* This project simulates **internal** load balancing for IIS applications.
* You can use this as a base for **domain-joined servers**, **AD authentication**, or **private app hosting** scenarios.

---
