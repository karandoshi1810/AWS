# 🖥️ Dynamic Website Hosting on Amazon EC2

Deploy a dynamic PHP website on an EC2 instance using Apache as the web server.

---

## 📁 Files Used
- `static-index.html` → used first to verify Apache is working (rename to `index.html`)
- `dynamic-index.php` → the dynamic PHP page (rename to `index.php` for deployment)

---

## ✅ Prerequisites
- An active AWS account
- Access to the AWS Management Console
- A key pair (.pem file) downloaded to your local machine
- Terminal / Command Prompt access (Mac/Linux: Terminal, Windows: PuTTY or Git Bash)

---

## 🚀 Step-by-Step Deployment

### Step 1 — Launch an EC2 Instance

1. Go to **AWS Console → EC2 → Launch Instance**
2. Enter a **Name** (e.g., `my-web-server`)
3. **AMI:** Select **Amazon Linux 2023** (Free Tier eligible)
4. **Instance Type:** Select `t2.micro` (Free Tier eligible)
5. **Key Pair:**
   - Click **Create new key pair**
   - Name it (e.g., `my-key`)
   - Type: RSA, Format: `.pem`
   - Click **Create** — it downloads automatically
   - > ⚠️ Save this file safely. You cannot download it again.
6. **Network Settings → Edit:**
   - Keep default VPC and subnet
   - **Firewall (Security Groups):** Create a new security group
     - Add rule: **HTTP** → Port `80` → Source `0.0.0.0/0`
     - SSH (Port 22) is added by default
7. Leave storage as default (8 GiB)
8. Click **Launch Instance**
9. Wait ~1 minute for the instance to reach **Running** state

---

### Step 2 — Connect to Your EC2 Instance via SSH

**On Mac/Linux:**
```bash
# Navigate to where your .pem file is downloaded
cd ~/Downloads

# Fix permissions on the key file (required)
chmod 400 my-key.pem

# Connect (replace <EC2-PUBLIC-IP> with your instance's public IP)
ssh -i my-key.pem ec2-user@<EC2-PUBLIC-IP>
```

**On Windows (Git Bash):**
```bash
chmod 400 my-key.pem
ssh -i my-key.pem ec2-user@<EC2-PUBLIC-IP>
```

> 📌 Find your Public IP: EC2 Console → Instances → click your instance → copy **Public IPv4 address**

---

### Step 3 — Install Apache Web Server

Run these commands inside your EC2 instance (after SSH):

```bash
# Update the system packages
sudo dnf update -y

# Install Apache (httpd)
sudo dnf install httpd -y

# Start Apache
sudo systemctl start httpd

# Enable Apache to start automatically on reboot
sudo systemctl enable httpd

# Verify Apache is running
sudo systemctl status httpd
```

---

### Step 4A — Deploy Static HTML Page (Quick Test)

Before going dynamic, verify Apache is working with a plain HTML page:

```bash
# Create the index.html file
sudo nano /var/www/html/index.html
```

Paste your `static-index.html` content, then save:
- Press `Ctrl + O` → Enter (to save)
- Press `Ctrl + X` (to exit)

```bash
# Fix file permissions
sudo chmod 644 /var/www/html/index.html
```

**Test it:**
Open a browser and go to:
```
http://<EC2-PUBLIC-IP>
```
You should see your static page. ✅

---

### Step 4B — Deploy Dynamic PHP Page

Now upgrade to a dynamic PHP site:

```bash
# Install PHP
sudo dnf install php -y

# Restart Apache to load PHP
sudo systemctl restart httpd

# Remove the static HTML file
sudo rm /var/www/html/index.html

# Create the PHP file
sudo nano /var/www/html/index.php
```

Paste your `dynamic-index.php` content, then save:
- Press `Ctrl + O` → Enter
- Press `Ctrl + X`

```bash
# Fix file permissions
sudo chmod 644 /var/www/html/index.php
```

**Test it:**
```
http://<EC2-PUBLIC-IP>
```

You should see:
- ✅ Live server timestamp (updates on refresh)
- ✅ Your visitor IP address
- ✅ A session-based page refresh counter

> 💡 **Demo tip:** Refresh the page a few times — watch the counter go up. This proves the page is being generated dynamically by the server, not just served as a static file!

---

### Step 5 — Understanding Elastic IP (Important!)

By default, your EC2 **Public IP changes every time you stop and restart the instance.**

To keep a permanent IP:
1. Go to **EC2 → Elastic IPs → Allocate Elastic IP**
2. Click **Associate Elastic IP**
3. Select your instance → **Associate**

> 💡 Elastic IP is free **only while your instance is running**. Release it when done to avoid charges.

---

## 🔄 Updating Your Website

To update the page content after deployment:

```bash
# SSH back into your instance
ssh -i my-key.pem ec2-user@<EC2-PUBLIC-IP>

# Edit the file directly
sudo nano /var/www/html/index.php

# Make changes, save, and refresh your browser
```

---

## 🧹 Cleanup (After the Session)

To avoid ongoing charges:
1. Go to **EC2 → Instances**
2. Select your instance → **Instance State → Terminate**
3. If you allocated an Elastic IP → **Elastic IPs → Release**

> ⚠️ Terminated instances cannot be recovered. Make sure you've saved anything important.

---

## ⚡ Key Concepts Recap

| Term | Meaning |
|---|---|
| EC2 Instance | A virtual server running in AWS |
| AMI | Amazon Machine Image — the OS template for your instance |
| t2.micro | Small, Free Tier eligible instance type |
| Key Pair (.pem) | Used to securely SSH into your instance |
| Security Group | Firewall rules — controls inbound/outbound traffic |
| Apache (httpd) | Web server software that serves your pages |
| PHP | Server-side scripting language for dynamic content |
| Elastic IP | A static public IP that stays with your instance |
| /var/www/html/ | Apache's default directory for serving web files |

---

## 📊 S3 Static vs EC2 Dynamic — Quick Comparison

| Feature | S3 Static | EC2 Dynamic |
|---|---|---|
| Setup Time | ~5 mins | ~20 mins |
| Cost | Very low | Higher (instance hours) |
| Server-side logic | ❌ No | ✅ Yes (PHP, Python, etc.) |
| Database support | ❌ No | ✅ Yes (connect to RDS) |
| Best for | Portfolios, landing pages | Web apps, e-commerce |

---

## 🔭 Next Steps (Beyond This Session)
- **RDS** — Connect your EC2 site to a managed MySQL/PostgreSQL database
- **Route 53** — Attach a custom domain to your EC2 public IP
- **CloudWatch** — Monitor your EC2 instance (CPU, memory, logs)
- **Load Balancer + Auto Scaling** — Handle traffic spikes automatically
