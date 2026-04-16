# Deploying a Static Website on the Cloud

**Objective:** Host a static website using cloud storage services.

---

## Option Selected: AWS S3 (Amazon Simple Storage Service)

<img width="2545" height="1394" alt="image" src="https://github.com/user-attachments/assets/e4c5a67f-7555-4899-a72e-26e563807da2" />


---

## Step a) Deploy a Static Website Using AWS S3

### 1. Create an S3 Bucket

1. Log in to [AWS Management Console](https://aws.amazon.com/console/).
2. Search for **S3** and open the S3 Dashboard.
3. Click **Create bucket**.

4. Configure basic settings:
   - **Bucket name:** `my-static-website-[your-unique-name]`  
     *(Must be globally unique – e.g., `my-site-john-2026`)*
   - **AWS Region:** Choose closest to your audience (e.g., `us-east-1`)
   - **Object Ownership:** Leave as default (ACLs disabled)

<img width="2552" height="1137" alt="image" src="https://github.com/user-attachments/assets/054dbe9a-5515-480b-9c30-699bc8dfa335" />


### 2. Disable "Block Public Access" (Required for Static Website)

1. Scroll to **Block Public Access settings for this bucket**.
2. Click **Edit** → **Uncheck** the box:
   - ☐ Block *all* public access
3. Check the acknowledgement box:  
   *"I acknowledge that the current settings might result in this bucket and the objects within becoming public."*
4. Click **Save changes**.

<img width="1751" height="517" alt="image" src="https://github.com/user-attachments/assets/b7251142-9879-490c-b83f-0b962e40ac3d" />

### 3. Enable Static Website Hosting

1. After bucket creation, click into your bucket.
2. Go to **Properties** tab.
3. Scroll to **Static website hosting** → Click **Edit**.
4. Select **Enable**.
5. Configure:
   - **Index document:** `index.html`
   - **Error document:** `error.html` (optional)
6. Click **Save changes**.

<img width="2560" height="1267" alt="image" src="https://github.com/user-attachments/assets/9e2f7dac-2037-4bac-b3dd-ff5698a2547e" />


7. Note the **Bucket website endpoint** URL that appears (e.g.,  
   `http://my-static-website-john.s3-website-us-east-1.amazonaws.com`)

> **📸 Screenshot Placeholder:**  
> *Add screenshot highlighting the Bucket website endpoint URL.*

### 4. Upload Website Files

1. Create a simple `index.html` file on your computer:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Cloud Website</title>
</head>
<body>
    <h1>Hello from AWS S3!</h1>
    <p>My static website is live on the cloud.</p>
</body>
</html>
```

2. In S3 bucket → Click **Upload** → **Add files**.
3. Select your `index.html` (and optionally `error.html`, `style.css`, etc.).
4. Click **Upload**.

<img width="2560" height="1087" alt="image" src="https://github.com/user-attachments/assets/3e8aee33-bd0a-49b3-8a55-5700518aced3" />

---

## Step b) Configure Permissions and Enable Public Access

### 1. Make Objects Publicly Readable

**Option A – Individual file method:**
1. Select the uploaded `index.html` checkbox.
2. Click **Actions** → **Make public using ACL** → **Make public**.

**Option B – Bucket policy method (recommended for all files):**

1. Go to bucket **Permissions** tab.
2. Scroll to **Bucket policy** → Click **Edit**.
3. Paste the following policy (replace `my-static-website-john` with your bucket name):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::my-static-website-john/*"
        }
    ]
}
```

<img width="2542" height="1407" alt="image" src="https://github.com/user-attachments/assets/6fa34644-f1d4-474e-bd59-eb2a041a0452" />


4. Click **Save changes**.

### 2. Verify Bucket Policy

- Look for a warning or error. If none, policy is valid.
- You should see: *"This bucket has a public bucket policy"* under Permissions.

---

## ✅ Test Your Static Website

1. Go back to bucket **Properties** → **Static website hosting**.
2. Copy the **Bucket website endpoint** URL.
3. Paste into a browser.

**Expected result:** Your `index.html` page displays.

<img width="1887" height="473" alt="image" src="https://github.com/user-attachments/assets/46c62af8-851e-430d-961e-07469cfd19b7" />
<img width="2652" height="642" alt="image" src="https://github.com/user-attachments/assets/c8b2c8f0-26ca-463e-8274-412b17adf102" />


## 🎯 Summary

| Task | Status |
|------|--------|
| Create S3 bucket with unique name | ✅ |
| Disable Block Public Access | ✅ |
| Enable Static Website Hosting | ✅ |
| Upload index.html | ✅ |
| Add bucket policy for public read | ✅ |
| Website accessible via endpoint URL | ✅ |
