# 🌐 Domain + HTTPS Setup (GoDaddy + Route 53 + ACM + CloudFront + S3)

This guide documents how to set up a production-ready frontend using:

- GoDaddy (Domain Registrar)
- AWS Route 53 (DNS)
- AWS ACM (SSL Certificate)
- AWS CloudFront (CDN + HTTPS)
- AWS S3 (Static Hosting)

---

## 🧠 Architecture Overview

User → Route 53 (DNS) → CloudFront (CDN + SSL) → S3 (Static Website)

---

# 🧾 STEP 1 — Buy Domain (GoDaddy)

1. Purchase your domain (e.g. osayskitchenandcafe.com)
2. Go to:
   - Domain Settings → Nameservers

---

# 🌍 STEP 2 — Create Hosted Zone (Route 53)

1. Go to AWS → Route 53
2. Click Hosted zones
3. Click Create hosted zone

Input:
- Domain name: osayskitchenandcafe.com
- Type: Public hosted zone

4. After creation, copy the 4 NS records

---

# 🔗 STEP 3 — Connect GoDaddy → Route 53

1. Go to GoDaddy
2. Open domain → Nameservers
3. Select Custom
4. Paste the 4 NS records from Route 53
5. Save

Wait 2–10 minutes

---

# 🔐 STEP 4 — Request SSL Certificate (ACM)

IMPORTANT: Use region us-east-1

1. Go to AWS → Certificate Manager (ACM)
2. Click Request certificate
3. Choose Public certificate
4. Add domain:
   inventory.osayskitchenandcafe.com
5. Validation method: DNS

---

# 🧩 STEP 5 — Add DNS Validation (CNAME)

ACM will give:

Name:
_abc123.inventory.osayskitchenandcafe.com

Value:
_xyz456.acm-validations.aws

Go to Route 53 → Hosted zone

Create record:
- Type: CNAME
- Name: _abc123.inventory
- Value: _xyz456.acm-validations.aws

Wait until status = ISSUED

---

# ☁️ STEP 6 — Create CloudFront Distribution

1. Go to CloudFront → Create distribution
2. Origin: Select S3 bucket
3. Viewer protocol policy: Redirect HTTP to HTTPS
4. Alternate domain:
   inventory.osayskitchenandcafe.com
5. Attach SSL certificate

Deploy (5–10 mins)

---

# 🌐 STEP 7 — Connect Domain to CloudFront

Go to Route 53 → Create record:

- Type: A
- Name: inventory
- Alias: ON
- Target: CloudFront distribution
- Routing policy: Simple

---

# 🎉 DONE

Access:
https://inventory.osayskitchenandcafe.com

---

# 🧠 Notes

- Route 53 auto-appends domain names
- Always use CNAME for ACM validation
- CloudFront certificates must be in us-east-1
- DNS changes may take a few minutes to propagate
