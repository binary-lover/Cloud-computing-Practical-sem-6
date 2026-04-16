# Install OpenStack on AWS EC2

**Objective:** Set up a local OpenStack environment for practice by deploying it on an AWS EC2 instance.

> **📸 Screenshot Placeholder:**  
> *Add screenshot showing the final OpenStack Horizon dashboard after successful installation.*

---

## Why Run OpenStack on AWS?

This approach creates a **"Cloud-on-a-Cloud"** architecture—running OpenStack (private cloud) inside AWS EC2 (public cloud). This is perfect for:
- Learning and experimentation without dedicated hardware
- Testing OpenStack features before production deployment
- Developing hybrid cloud solutions

> **⚠️ Important Note:** This setup has known networking limitations due to AWS's MAC address restrictions. For pure learning purposes, consider using DevStack on a single VM as described below—it's simpler and sufficient for practice.

---

## Step a) Launch an EC2 Instance for OpenStack Deployment

### 1. Choose the Right EC2 Configuration

OpenStack is resource-intensive. Select these specifications:

| Setting | Recommended Value | Reason |
|---------|-------------------|--------|
| **Instance type** | `t2.large` or `t3.large` (minimum) | 8GB RAM required for OpenStack |
| **vCPUs** | 2+ | DevStack needs multiple cores |
| **RAM** | 8 GB minimum (16 GB recommended) | OpenStack services consume significant memory |
| **Storage** | 50 GB minimum | OS + OpenStack components + images |
| **OS Image** | Ubuntu 22.04 LTS | Most compatible with DevStack |

<img width="2643" height="196" alt="image" src="https://github.com/user-attachments/assets/39001654-0c69-4c4e-baa8-46facaee76a5" />


### 2. Configure Network and Security

1. **VPC Settings:** Use default VPC or your custom VPC from previous exercise.
2. **Subnet:** Any public subnet (to allow internet access for package downloads).
3. **Auto-assign Public IP:** Enable (for SSH access).
4. **Security Group Rules:**

| Type | Protocol | Port | Source | Purpose |
|------|----------|------|--------|---------|
| SSH | TCP | 22 | Your IP | Remote access |
| HTTP | TCP | 80 | 0.0.0.0/0 | Horizon dashboard |
| HTTPS | TCP | 443 | 0.0.0.0/0 | Secure dashboard access |

<img width="2479" height="1321" alt="image" src="https://github.com/user-attachments/assets/10ce3d4f-552f-4583-815e-849087d43995" />


### 3. Create Key Pair and Launch

1. **Key pair:** Create new or use existing `.pem` key.
2. Click **Launch instance**.
3. Wait for instance status to show **Running**.

> **📸 Screenshot Placeholder:**  
> *Add screenshot showing EC2 instance in "Running" state with public IP visible.*

---

## Step b) Prepare the EC2 Instance for OpenStack

### 1. Connect via SSH

```bash
chmod 400 your-key.pem
ssh -i your-key.pem ubuntu@<your-ec2-public-ip>
```

<img width="2643" height="196" alt="image" src="https://github.com/user-attachments/assets/4ccff3aa-ef76-41f5-aa36-afae21dbf6ce" />


### 2. Update System and Install Dependencies

```bash
# Update package lists
sudo apt update && sudo apt upgrade -y

# Install git and essential tools
sudo apt install -y git python3-pip net-tools
```

<img width="1186" height="300" alt="image" src="https://github.com/user-attachments/assets/a712a4fb-36df-44c8-b86b-8df4fb962600" />


---

## Step c) Install DevStack (Simplest OpenStack Deployment)

DevStack is the official quick-start method for OpenStack—perfect for learning and testing on a single VM.

### 1. Create the Stack User

```bash
# Create dedicated OpenStack user
sudo useradd -s /bin/bash -d /opt/stack -m stack

# Grant sudo privileges without password
echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack

# Switch to stack user
sudo su - stack
```

<img width="2560" height="609" alt="image" src="https://github.com/user-attachments/assets/c96b556f-809d-45b2-8967-0bb24d63bede" />

### 2. Clone DevStack Repository

```bash
# Install git if not already installed
sudo apt install -y git

# Clone DevStack
git clone https://opendev.org/openstack/devstack
cd devstack

# Use a stable branch (recommended)
git checkout stable/2024.1
```

<img width="991" height="470" alt="image" src="https://github.com/user-attachments/assets/9c95fa40-9c6f-4118-9d0d-c834eaa8f47d" />


### 3. Create local.conf Configuration File

This file tells DevStack how to configure your OpenStack deployment.

```bash
cat > local.conf <<EOF
[[local|localrc]]
# Password configuration (use same password for simplicity)
ADMIN_PASSWORD=devstack
DATABASE_PASSWORD=\$ADMIN_PASSWORD
RABBIT_PASSWORD=\$ADMIN_PASSWORD
SERVICE_PASSWORD=\$ADMIN_PASSWORD

# Get the host IP automatically
HOST_IP=\$(hostname -I | awk '{print \$1}')

# Enable essential services
enable_service n-api n-crt n-cond n-sch n-novnc n-cauth
enable_service g-api g-reg
enable_service cinder c-sch c-api c-vol
enable_service q-svc q-dhcp q-meta q-l3

# Disable testing suite (saves time)
disable_service tempest

# Logging configuration
LOGFILE=\$DEST/logs/stack.sh.log
VERBOSE=True
LOG_COLOR=True

# Use QEMU instead of KVM (important for AWS - no nested virtualization)
LIBVIRT_TYPE=qemu

# Reduce resource usage for learning environment
API_WORKERS=1

EOF
```

<img width="749" height="577" alt="image" src="https://github.com/user-attachments/assets/c22f032a-b2a9-47f6-b675-af86250ff151" />


### 4. Run DevStack Installation

```bash
./stack.sh
```

> **⚠️ Installation takes 20-60 minutes** depending on internet speed and instance performance.

> **📸 Screenshot Placeholder:**  
> *Add screenshot showing stack.sh script running (progress output).*

### 5. Verify Successful Installation

Look for this success message at the end:

```
Horizon is now available at http://<your-ip>/dashboard
Keystone is serving at http://<your-ip>/identity/
The default users are: admin and demo
The password: devstack
```

<img width="2560" height="618" alt="image" src="https://github.com/user-attachments/assets/e26b8829-5fd8-42e7-b265-e976a56ac108" />

Error:
<img width="2561" height="738" alt="image" src="https://github.com/user-attachments/assets/754657de-f509-4ed6-a634-a6e7894525d4" />

---

## Step d) Access OpenStack Horizon Dashboard

### 1. Open Horizon Dashboard

1. In your browser, navigate to: `http://<your-ec2-public-ip>/dashboard`
2. Login with:
   - **Username:** `admin` (or `demo`)
   - **Password:** `devstack` (the password you set)

> **📸 Screenshot Placeholder:**  
> *Add screenshot showing Horizon login page.*

### 2. Explore OpenStack Services

After login, you can see:
- **Project** tab: Manage instances, volumes, networks
- **Admin** tab: System management (admin only)
- **Identity** tab: Manage users and projects

> **📸 Screenshot Placeholder:**  
> *Add screenshot showing Horizon dashboard home page.*

---

## 🧹 Clean Up Resources

To avoid ongoing AWS charges:

1. **Stop or terminate the EC2 instance** when not in use.
2. If terminating, also clean up:
   - Security groups (if created specifically for this)
   - Elastic IPs (if allocated)
   - Volumes (ensure they're deleted with instance)

> **📸 Screenshot Placeholder:**  
> *Add screenshot showing EC2 instance termination confirmation.*

---

## 🔧 Troubleshooting Common Issues

| Issue | Likely Cause | Solution |
|-------|--------------|----------|
| Installation fails | Insufficient RAM | Use `t3.large` or larger instance type |
| Cannot access Horizon | Security group missing port 80 | Add HTTP (80) inbound rule |
| Stack.sh errors | Using root user instead of stack | Always run as `stack` user |
| Slow performance | `t2.micro` instance | Upgrade to `t2.large` minimum |

---

## 📊 Summary

| Step | Status |
|------|--------|
| Launch EC2 instance (t2.large, Ubuntu 22.04) | ✅ |
| Configure security group (SSH, HTTP) | ✅ |
| Install DevStack with local.conf | ✅ |
| Access Horizon dashboard | ✅ |
