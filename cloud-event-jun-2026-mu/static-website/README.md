# 🪣 Static Website Hosting on Amazon S3

Deploy a static website on AWS S3 in minutes — no server required.

---

## 📁 Files Used
- `static-index.html` → your homepage (rename to `index.html` before uploading)
- `error.html` → optional error page (shown on broken links)

---

## ✅ Prerequisites
- An active AWS account
- Access to the AWS Management Console
- Your `index.html` file ready to upload

---

## 🚀 Step-by-Step Deployment

### Step 1 — Create an S3 Bucket

1. Go to **AWS Console → S3 → Create Bucket**
2. Enter a **Bucket Name** (e.g., `my-static-website-demo`)
   > ⚠️ Bucket names must be globally unique and lowercase
3. Select your **AWS Region** (e.g., `us-east-1`)
4. Under **Block Public Access settings**, **uncheck** "Block all public access"
5. Confirm the warning checkbox
6. Leave all other settings as default
7. Click **Create Bucket**

---

### Step 2 — Upload Your Files

1. Open the bucket you just created
2. Click **Upload → Add Files**
3. Select `index.html` (and `error.html` if you have one)
4. Click **Upload**

---

### Step 3 — Enable Static Website Hosting

1. Inside your bucket, go to the **Properties** tab
2. Scroll down to **Static website hosting**
3. Click **Edit**
4. Select **Enable**
5. Set:
   - **Index document:** `index.html`
   - **Error document:** `error.html` (optional)
6. Click **Save changes**
7. Copy the **Bucket website endpoint URL** shown at the bottom
   > Looks like: `http://my-static-website-demo.s3-website-us-east-1.amazonaws.com`

---

### Step 4 — Set Bucket Policy (Make Site Public)

1. Go to the **Permissions** tab of your bucket
2. Scroll to **Bucket Policy → Edit**
3. Paste the following policy (replace `your-bucket-name`):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

4. Click **Save changes**

---

### Step 5 — Access Your Website

1. Go back to **Properties → Static website hosting**
2. Click on the **Bucket website endpoint URL**
3. Your site is live! 🎉

---

## 🔄 Updating Your Website

To update content:
1. Edit your `index.html` locally
2. Go to **S3 → your bucket → Upload**
3. Re-upload the file (it will overwrite the old one)
4. Refresh your browser — changes are live immediately

---

## 🧹 Cleanup (After the Session)

To avoid any charges:
1. Go to **S3 → your bucket**
2. Select all files → **Delete**
3. Then go back, select the bucket → **Delete bucket**
4. Confirm by typing the bucket name

---

## ⚡ Key Concepts Recap

| Term | Meaning |
|---|---|
| S3 Bucket | A container that stores your files |
| Object | Any file stored in S3 (HTML, image, etc.) |
| Static Website | A site with fixed content — no server-side logic |
| Bucket Policy | JSON rules that control who can access your bucket |
| Endpoint URL | The auto-generated URL AWS gives your S3 website |

---

## 🔭 Next Steps (Beyond This Session)
- **CloudFront** — CDN to make your site faster globally + enable HTTPS
- **Route 53** — Attach a custom domain (e.g., `www.yoursite.com`)
- **S3 Versioning** — Keep history of file changes
