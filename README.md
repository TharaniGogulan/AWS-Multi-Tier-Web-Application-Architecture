# 🚀 AWS Multi-Tier Web Application Architecture

---

## 📌 Project Description

This project demonstrates a secure and scalable **3-Tier Web Architecture** deployed on **Amazon Web Services (AWS)**.

The architecture separates the infrastructure into three logical layers:

- **Presentation Layer (Web Tier)**
- **Application Layer (App Tier)**
- **Database Layer (RDS Tier)**

The design follows real-world production best practices including private subnets, security groups, and controlled access between tiers.

---

## 🎯 Problem Solved / Goal

Traditional single-tier deployments expose applications and databases directly to the internet, creating security risks and scalability issues.

This project solves that problem by:

- Isolating the Application and Database layers in private subnets
- Allowing public access only through an Application Load Balancer
- Restricting database access strictly to the Application tier
- Implementing secure communication using Security Groups

The goal is to simulate a production-grade cloud architecture with proper network segmentation and security.

---

## 🛠 AWS Services Used

| Service | Purpose |
|----------|----------|
| Amazon VPC | Network isolation |
| Public & Private Subnets | Tier separation |
| Internet Gateway (IGW) | Public internet access |
| NAT Gateway | Outbound internet for private instances |
| EC2 (Web Server) | Presentation layer |
| EC2 (App Server) | Application processing |
| Application Load Balancer (ALB) | Traffic distribution |
| Target Group | Route traffic to Web instances |
| Amazon RDS (MySQL) | Managed database |
| Security Groups | Firewall control |
| Route Tables | Network routing |

---

## 🏗 Architecture Diagram

```
                Internet
                    │
                    ▼
        ┌─────────────────────┐
        │ Application Load    │
        │     Balancer        │
        └─────────────────────┘
                    │
                    ▼
        ┌─────────────────────┐
        │   Web Server (EC2)  │
        │   Public Subnet     │
        └─────────────────────┘
                    │
                    ▼
        ┌─────────────────────┐
        │   App Server (EC2)  │
        │   Private Subnet    │
        └─────────────────────┘
                    │
                    ▼
        ┌─────────────────────┐
        │   Amazon RDS MySQL  │
        │   Private Subnet    │
        └─────────────────────┘
```


## ⚙ Deployment Instructions

### Step 1: Create VPC
- CIDR: `10.0.0.0/16`

### Step 2: Create Subnets
- Public Subnet (Web + ALB)
- Private Subnet (App)
- Private Subnet (RDS)

### Step 3: Attach Internet Gateway
- Add route `0.0.0.0/0 → IGW` in Public Route Table

### Step 4: Create NAT Gateway
- Place in Public Subnet
- Add route `0.0.0.0/0 → NAT` in Private Route Table

### Step 5: Launch EC2 Instances
- Web Server (Public Subnet)
- App Server (Private Subnet)

Install Apache on Web Server:

```bash
sudo yum install httpd -y
sudo systemctl start httpd
```

### Step 6: Create RDS MySQL
- Private Subnet
- Public Access: No
- Security Group: Allow `3306` from SG-APP only

### Step 7: Configure Security Groups

**SG-WEB**
- HTTP 80 → 0.0.0.0/0
- SSH 22 → My IP

**SG-APP**
- Custom TCP 8080 → SG-WEB
- SSH 22 → SG-WEB

**SG-DB**
- MySQL 3306 → SG-APP

### Step 8: Create Application Load Balancer
- Internet-facing
- Public Subnets only
- Listener: HTTP 80
- Target Group → Web Server

---

## 🧪 Test Cases

### ✅ Test Case 1 – Access Website via ALB
**Description:** Verify website loads using ALB DNS  
**Expected Result:** Web page loads successfully  
**Test Result:** ✅ Pass  

---

### ✅ Test Case 2 – Web → App Server Communication
SSH into Web Server and run:

```bash
curl http://<APP_PRIVATE_IP>:8080
```

**Expected Result:** Application response received  
**Test Result:** ✅ Pass  

---

### ✅ Test Case 3 – App → Database Connectivity
SSH into App Server and run:

```bash
mysql -h <RDS-ENDPOINT> -u admin -p
```

**Expected Result:** MySQL login successful  
**Test Result:** ✅ Pass  

---

### ✅ Test Case 4 – Security Test
**Description:** Attempt browser access to private IP  
**Expected Result:** Connection timed out  
**Test Result:** ✅ Pass  

---

## 📸 Screenshots / Demos

Include screenshots of:

- VPC and Subnets
- Route Tables
- Security Groups
- EC2 Instances running
- RDS instance
- ALB DNS working in browser
- Successful MySQL login
- Blocked App Server browser access

---

## 📚 Lessons Learned

- Importance of placing ALB in public subnets
- Private instances require NAT Gateway for internet access
- Security Groups are stateful
- Proper SG source configuration is critical
- Multi-tier architecture improves security and scalability
- RDS should never be publicly accessible in production

Troubleshooting requires checking:
- Security Groups
- Route Tables
- NACL
- Service status

---

## 🔐 Security Best Practices Implemented

- Database isolated in private subnet
- App server not publicly accessible
- Security Group referencing between tiers
- No hardcoded credentials
- Controlled SSH access

---

## 🎯 Conclusion

This project successfully demonstrates a production-ready AWS Multi-Tier Architecture with proper network isolation, load balancing, secure database connectivity, and tier-based access control.

The deployment validates core AWS networking concepts and cloud security best practices.

---

## 👨‍💻 Author

Tharani G
