# Launch Your First Amazon EC2 Instance

**Objective:** Deploy a virtual machine on AWS using Amazon EC2.

---

## Step a) Launch an EC2 Instance from the AWS Management Console

1. Log in to your [AWS Management Console](https://aws.amazon.com/console/).
2. In the top search bar, type **EC2** and click on the **EC2** service under “Services”.
3. On the EC2 Dashboard, click the orange **Launch instance** button.

<img width="2755" height="673" alt="image" src="https://github.com/user-attachments/assets/d1e6ca49-0590-4b1a-8f43-0d2336e8c625" />


---

## Step b) Use a Pre-configured AMI (e.g., Amazon Linux 2)

1. Under **Name and tags**, enter a name for your instance, e.g., `MyFirstEC2`.
2. In the **Application and OS Images (Amazon Machine Image – AMI)** section:
   - Click **Quick Start** tab.
   - Search for `Amazon Linux 2 AMI (HVM)` – choose the **free tier eligible** version.
3. Under **Instance type**, select `t3.micro` (free tier eligible).
4. Under **Key pair (login)**:
   - Click **Create new key pair**.
   - Name it (e.g., `my-ec2-key`), choose **RSA** and `.pem` format.
   - **Download and save this file securely** – you’ll need it for SSH.
5. Keep all other defaults. Click **Launch instance** at the bottom right.

<img width="2558" height="1406" alt="image" src="https://github.com/user-attachments/assets/a50bf3a6-967d-410b-bd1b-4b0ab80d7eef" />
<img width="2560" height="1439" alt="image" src="https://github.com/user-attachments/assets/9ae1e4f2-ba8b-4afb-950d-62c1acc12eac" />


---

## Step c) Configure Security Groups to Allow SSH Access

> *You can configure this during instance launch or edit after. Here’s how to do it during launch:*

1. In the **Network settings** section, click **Edit**.
2. Under **Security group rules**, ensure there is a rule with:
   - **Type:** SSH  
   - **Source:** My IP (or `0.0.0.0/0` if testing – but My IP is safer)
3. Leave all other rules as default.

<img width="3384" height="857" alt="image" src="https://github.com/user-attachments/assets/cf15058c-26da-494d-8ba8-23e53675c71f" />


> ⚠️ *Security best practice: Only allow SSH from your specific IP address.*

---

## Step d) Connect to the Instance Using SSH

### After the instance status shows **Running** and **2/2 checks passed**:

1. Go back to EC2 Dashboard → **Instances**.
2. Select your instance. Click the **Connect** button (top right).
3. Choose the **SSH client** tab.
4. Copy the example command, which looks like:
   ```bash
   ssh -i "my-ec2-key.pem" ec2-user@your-instance-public-dns
   ```

### On your terminal (Mac/Linux) or Git Bash (Windows):

```bash
# Move your .pem file to a secure folder, then:
chmod 400 my-ec2-key.pem
ssh -i "my-ec2-key.pem" ec2-user@<your-instance-public-ip-or-dns>
```
<img width="2560" height="1274" alt="image" src="https://github.com/user-attachments/assets/f391002b-bd5f-478f-a5a7-c8f78e47953c" />


---

## ✅ Expected Result

After successful SSH connection, you’ll see a welcome message and a command prompt like:

```
[ec2-user@ip-xxx-xxx-xxx-xxx ~]$
```

You have now launched and connected to your first EC2 instance!

<img width="1435" height="388" alt="image" src="https://github.com/user-attachments/assets/409c4ba0-af54-40e5-a6e7-c49d28d2b7db" />


---
