# S3 Static Site Setup (Vite + React)

This guide walks through deploying a static site (e.g., Vite + React) to AWS S3 with proper configuration and troubleshooting steps.

---

## 1. Create the S3 Bucket
**Navigation:** AWS Console → S3 → **Create bucket**

### Settings
* **Bucket name:** Must be globally unique (Example: `www.cashflowapp.io`).
* **Region:** Choose the one closest to your users (e.g., `Canada Central`).

### Public Access
* **Uncheck:** "Block all public access".
* **Acknowledge:** Confirm the warning checkbox.

> [!IMPORTANT]
> This is required so your site can be publicly accessible via the internet.

---

## 2. Build Your App
To prepare your project for production, run the build command in your terminal:

```bash
npm run build
```

This generates a `/dist` folder containing your minified production files.

---

## 3. Upload Files to S3
1. Open your bucket in the AWS Console.
2. Click **Upload**.
3. Upload the *contents* of the `/dist` folder (do **NOT** upload the folder itself).

**Your bucket structure should look like this:**
```plaintext
index.html
assets/
  index-xxxxx.js
  index-xxxxx.css
```

---

## 4. Enable Static Website Hosting
**Navigation**: Bucket → **Properties** → **Static website hosting**

**Configuration**
* **Status:** Enable static website hosting
* **Index document:** `index.html`
* **Error document:** `index.html`

> [!TIP]
> Setting the Error document to `index.html` is required for Single-Page Applications (SPAs) to allow React Router to handle page refreshes without 404 errors.

---

## 5. Set Bucket Policy
**Navigation:** Permissions → **Bucket Policy**

Paste the following JSON (replace `YOUR-BUCKET-NAME` with your actual bucket name):

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
        }
    ]
}
```

---

## 6. Access Your Site
1. Navigate to **Properties** → **Static website hosting**.
2. Copy the **Bucket website endpoint**, which looks like: `http://your-bucket-name.s3-website-<region>.amazonaws.com`
3. Open this URL in your browser.

---

## 🛠 Troubleshooting
| Issue | Solution |
| :--- | :--- |
| **Blank Page** | Ensure your `vite.config.js` includes: `base: './'` |
| **404 on Refresh** | Ensure the **Error document** in S3 properties is set to `index.html`. |
| **403 Forbidden** | Check that the bucket policy is applied and public access is off. |
| **Assets Not Loading** | Upload the contents of /dist, not the folder. Ensure Vite uses relative paths. |
| **Works Locally but Not S3** | Run `npm run preview` to test the build locally before uploading. |
| **JS Errors in Production** |	Check the browser console. Common causes include hydration issues or unsupported syntax. |

---

## ⚡ Optional: Cache Optimization

To improve performance, set metadata on your files within S3:

* **For** `/assets/*`: `Cache-Control: public, max-age=31536000, immutable`
* **For** `index.html`:`Cache-Control: no-cache`

---

## 📋 Deployment Summary

1. `npm run build`
2. Upload `/dist` → **S3**
3. **Done!**
