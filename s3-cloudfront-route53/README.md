# Static Website Hosting with S3 + CloudFront + Route 53

This guide shows how to deploy a secure and scalable static website using:

- **Amazon S3** – Static site hosting
- **CloudFront** – CDN with HTTPS
- **ACM** – TLS certificate management
- **Route 53** – DNS zone and routing

---

## Prerequisites

- AWS Account
- Domain name (e.g. `thempm.xyz`)
- Access to your domain registrar (e.g. GoDaddy)

---

## 🛠️ Setup Steps

### 1. Create S3 Bucket

- **Name:** `thempm.xyz`
- Enable **Static website hosting**
- Set:
  - Index document: `index.html`
  - (Optional) Error document: `error.html`
- Upload your website files

### 2. Make S3 Bucket Public

Attach the following **Bucket Policy** to allow public read access:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicRead",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::thempm.xyz/*"
    }
  ]
}
````


---

### 3. Request TLS Certificate via ACM

* Go to **AWS Certificate Manager** (ACM) in **us-east-1**
* Request a certificate for:

  * `*.thempm.xyz`

* Use **DNS validation**
* Copy validation records to Route 53 or your registrar to verify ownership

---

### 4. Create CloudFront Distribution

* **Origin Domain Name:**
  `thempm.xyz.s3-website-ap-southeast-1.amazonaws.com`
* **Viewer Protocol Policy:** Redirect HTTP to HTTPS
* **Alternate Domain Names (CNAMEs):**

  * `thempm.xyz`
  * `www.thempm.xyz`
* **SSL Certificate:** Use the one from ACM
* **Default Root Object:** `index.html`

---

### 5. Create Route 53 Hosted Zone

* Create a **Hosted Zone** for `thempm.xyz`
* Create the following DNS records:

#### A Record (Root domain)

* **Name:** `thempm.xyz`
* **Type:** A
* **Alias:** Yes
* **Target:** CloudFront distribution

#### CNAME Record (www subdomain)

* **Name:** `www`
* **Type:** CNAME
* **Value:** CloudFront distribution domain (e.g. `d3acfvpbngbe66.cloudfront.net`)

---

### 6. Update Domain Nameservers

In your domain registrar (e.g. GoDaddy):

* Replace your nameservers with the ones from Route 53:

```
ns-1044.awsdns-02.org
ns-2049.awsdns-64.com
ns-1783.awsdns-30.co.uk
ns-540.awsdns-03.net
```

---

### 7. Test Your Website

Once DNS changes propagate (can take a few minutes to hours):

* Visit: [https://thempm.xyz](https://thempm.xyz)
* Visit: [https://www.thempm.xyz](https://www.thempm.xyz)

You should see your static site served via CloudFront with HTTPS!



---


---

## 💡 Optional Enhancements

* Redirect `www.thempm.xyz` to `thempm.xyz` (or vice versa) using:

  * S3 + CloudFront redirect bucket
  * Lambda\@Edge
* Add caching headers for performance
* Enable versioning in S3
* Set up custom error pages

---

