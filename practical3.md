Here’s a step-by-step writeup for **Set Up a VPC**, formatted with clear screenshot placeholders.

---

# Set Up a VPC

**Objective:** Create and configure a Virtual Private Cloud (VPC) with public and private subnets, internet access for public instances, and NAT Gateway for private instances.

---

## Step a) Create a Custom VPC with a Public and Private Subnet

### 1. Create the VPC

1. Log in to [AWS Management Console](https://aws.amazon.com/console/).
2. Search for **VPC** and open the VPC Dashboard.
3. Click **Your VPCs** → **Create VPC**.
4. Select **VPC only** (or use "VPC and more" wizard for automatic setup).
5. Configure:
   - **Name tag:** `MyCustomVPC`
   - **IPv4 CIDR:** `10.0.0.0/16`
   - Leave other defaults → Click **Create VPC**.

<img width="2560" height="1321" alt="image" src="https://github.com/user-attachments/assets/46e13a87-5e72-473c-a3ac-4efb5ae1828e" />


### 2. Create Subnets

1. Go to **Subnets** → **Create subnet**.
2. **Public Subnet:**
   - **VPC:** Select `MyCustomVPC`
   - **Name:** `Public-Subnet`
   - **Availability Zone:** Choose first AZ (e.g., `us-east-1a`)
   - **CIDR:** `10.0.1.0/24`
   - Click **Create subnet**.
3. **Private Subnet:**
   - Repeat steps with:
     - **Name:** `Private-Subnet`
     - **CIDR:** `10.0.2.0/24`
   - Click **Create subnet**.

<img width="2560" height="1239" alt="image" src="https://github.com/user-attachments/assets/a4606cb7-72f8-4997-9941-f989979c752f" />


### 3. Enable Auto-assign Public IP for Public Subnet

1. Select `Public-Subnet` → **Actions** → **Edit subnet settings**.
2. Check **Enable auto-assign public IPv4 address** → **Save**.

<img width="2560" height="1044" alt="image" src="https://github.com/user-attachments/assets/c3a41fc7-83d4-4c20-9dc3-4c618f9ec6cc" />


---

## Step b) Launch EC2 Instances (One in Public, One in Private Subnet)

### Launch First EC2 Instance (Public Subnet)

1. Go to **EC2 Dashboard** → **Launch instance**.
2. **Name:** `Public-Server`
3. **AMI:** Amazon Linux 2023 (free tier)
4. **Instance type:** `t3.micro`
5. **Key pair:** Create or select existing key pair.
6. **Network settings → Edit:**
   - **VPC:** `MyCustomVPC`
   - **Subnet:** `Public-Subnet`
   - **Auto-assign public IP:** Enable
   - **Security group:** Create new with SSH (port 22) from `0.0.0.0/0`
7. Click **Launch instance**.

<img width="2558" height="1329" alt="image" src="https://github.com/user-attachments/assets/988914f2-807f-4b14-8d8f-d2fa99514c0d" />

<img width="2554" height="1310" alt="image" src="https://github.com/user-attachments/assets/96932032-a850-42f8-9c4d-e5889611dd74" />


### Launch Second EC2 Instance (Private Subnet)

1. Repeat launch process.
2. **Name:** `Private-Server`
3. **Network settings → Edit:**
   - **VPC:** `MyCustomVPC`
   - **Subnet:** `Private-Subnet`
   - **Auto-assign public IP:** Disable (greyed out)
   - **Security group:** Same or new with SSH only from VPC CIDR (`10.0.0.0/16`)
4. Click **Launch instance**.

<img width="2560" height="792" alt="image" src="https://github.com/user-attachments/assets/8a13b55c-cf24-482c-88f8-3f2dd02fd432" />

---

## Step c) Configure an Internet Gateway for Public Subnet Access

### 1. Create Internet Gateway

1. Go to **VPC Dashboard** → **Internet Gateways** → **Create internet gateway**.
2. **Name:** `My-IGW` → **Create**.
3. Select the IGW → **Actions** → **Attach to VPC**.
4. Choose `MyCustomVPC` → **Attach**.

<img width="2558" height="1051" alt="image" src="https://github.com/user-attachments/assets/5f063c12-d1d9-4d4c-abe0-1637bce679af" />

### 2. Update Route Table for Public Subnet

1. Go to **Route Tables** → Find route table associated with `MyCustomVPC`.
2. **Name the main route table:** `Public-RT` (or create a custom one).
3. Select it → **Routes** tab → **Edit routes** → **Add route**:
   - **Destination:** `0.0.0.0/0`
   - **Target:** Select `My-IGW`
   - **Save routes**.
4. **Subnet associations** tab → Ensure `Public-Subnet` is explicitly associated.

<img width="2561" height="1070" alt="image" src="https://github.com/user-attachments/assets/89e6399a-a487-4022-876e-e44667279cec" />


### 3. Test Internet Access (Public Instance)

SSH into `Public-Server` and run:
```bash
ping -c 4 google.com
```

<img width="1439" height="511" alt="image" src="https://github.com/user-attachments/assets/a962a2c3-6153-458a-ae19-2b51dd1f6d16" />

---

## Step d) Use a NAT Gateway for Private Subnet Internet Access

### 1. Allocate Elastic IP

1. Go to **VPC Dashboard** → **Elastic IPs** → **Allocate Elastic IP address**.
2. Click **Allocate** → Copy the allocation ID.

<img width="2560" height="667" alt="image" src="https://github.com/user-attachments/assets/96e12a81-a581-4e1e-a54e-e9fa1b1d8a83" />


### 2. Create NAT Gateway

1. Go to **NAT Gateways** → **Create NAT gateway**.
2. Configure:
   - **Name:** `My-NAT-GW`
   - **Subnet:** `Public-Subnet` (NAT must be in public subnet)
   - **Connectivity type:** Public
   - **Elastic IP:** Select the allocated IP
3. Click **Create NAT gateway**.

<img width="2560" height="866" alt="image" src="https://github.com/user-attachments/assets/6ca7d5cc-fd59-4fbb-8370-8ad5e5f69fb1" />


### 3. Update Private Subnet Route Table

1. Go to **Route Tables** → **Create route table**:
   - **Name:** `Private-RT`
   - **VPC:** `MyCustomVPC`
   - Click **Create**.
2. **Subnet associations** tab → **Edit subnet associations** → Associate `Private-Subnet`.
3. **Routes** tab → **Edit routes** → **Add route**:
   - **Destination:** `0.0.0.0/0`
   - **Target:** Select `My-NAT-GW`
   - **Save routes**.

<img width="2560" height="1027" alt="image" src="https://github.com/user-attachments/assets/93e77e40-dba5-4cce-b2ff-a87a7a9dcf71" />


### 4. Test Internet Access (Private Instance)

> *Note: You cannot SSH directly to Private-Server. Connect from Public-Server instead.*

From your local machine (already SSH'd into `Public-Server`):
```bash
# Copy your key to Public-Server first, or use PEM file
ssh -i /path/to/key.pem ec2-user@<private-server-private-ip>
```

Then from Private-Server:
```bash
ping -c 4 google.com
```

<img width="707" height="363" alt="image" src="https://github.com/user-attachments/assets/039dc934-c41a-4603-b0d5-818cd63bbfcc" />

<img width="967" height="692" alt="image" src="https://github.com/user-attachments/assets/c39cb7d2-8291-4b87-acae-33aed78c708e" />


---

## ✅ Architecture Summary

| Component | CIDR / Identifier | Internet Access |
|-----------|-------------------|-----------------|
| **VPC** | 10.0.0.0/16 | - |
| **Public Subnet** | 10.0.1.0/24 | Via IGW |
| **Private Subnet** | 10.0.2.0/24 | Via NAT Gateway |
| **Public EC2** | In Public Subnet | ✅ Yes (IGW) |
| **Private EC2** | In Private Subnet | ✅ Yes (NAT Gateway) |

> **📸 Screenshot Placeholder:**  
> *Optional – add VPC architecture diagram or VPC Dashboard showing all components.*

---

## 🧹 Clean Up (Optional)

To avoid charges:
1. Terminate both EC2 instances.
2. Delete NAT Gateway (wait ~1 min).
3. Release Elastic IP.
4. Delete Internet Gateway.
5. Delete subnets, route tables, and finally the VPC.

---
